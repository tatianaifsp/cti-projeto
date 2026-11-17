# CTI Insights · Banco de dados

Banco **PostgreSQL** do CTI Insights, hospedado no **Neon**. Guarda os usuários do sistema e os dados válidos de cada planilha processada, junto com os indicadores calculados para o dashboard. Linhas inválidas nunca são gravadas.

## Modelo de dados

```mermaid
erDiagram
    UPLOAD_LOTE ||--o{ CLIENTE : "origina"
    UPLOAD_LOTE ||--o| ESTATISTICA_LOTE : "gera"
    CONSULTOR ||--o{ CLIENTE : "atende"
    SEGMENTO ||--o{ CLIENTE : "classifica"
    CLIENTE ||--o{ CONTRATO : "possui"
    SERVICO ||--o{ CONTRATO : "compõe"
    USUARIO {
        bigint id PK
        varchar nome
        varchar email UK
        varchar senha_hash
        varchar matricula
        varchar turma
        varchar perfil
        varchar status
        timestamp criado_em
    }
    UPLOAD_LOTE {
        bigint id PK
        varchar arquivo_nome
        timestamp enviado_em
        varchar status
        int total_linhas
        int linhas_validas
        int linhas_invalidas
        int linhas_com_aviso
        varchar mensagem
    }
    CLIENTE {
        bigint id PK
        bigint lote_id FK
        varchar codigo_cti
        varchar nome
        bigint consultor_id FK
        bigint segmento_id FK
        char nivel
        numeric faturamento_anual
        varchar cidade
        char uf
    }
    CONSULTOR {
        bigint id PK
        varchar nome UK
    }
    SEGMENTO {
        bigint id PK
        varchar nome UK
    }
    SERVICO {
        bigint id PK
        varchar nome UK
    }
    CONTRATO {
        bigint id PK
        bigint cliente_id FK
        bigint servico_id FK
        date data_contratacao
    }
    ESTATISTICA_LOTE {
        bigint lote_id PK "também FK para upload_lote"
        timestamp gerado_em
        text resultado
    }
```

## Tabelas

| Tabela | Conteúdo | Gravada por |
|---|---|---|
| `usuario` | Contas do sistema: nome, e-mail, senha criptografada (BCrypt), perfil e status | Back-end (Java) |
| `upload_lote` | Uma linha por planilha enviada: nome do arquivo, data, status e contagem de linhas | Python |
| `cliente` | Clientes válidos do lote: código, nome, consultor, segmento, nível, faturamento, cidade e UF | Python |
| `consultor` | Consultores, sem repetição | Python |
| `segmento` | Segmentos de mercado, sem repetição | Python |
| `servico` | Serviços oferecidos, sem repetição | Python |
| `contrato` | Serviços contratados por cada cliente, com a data de contratação | Python |
| `estatistica_lote` | Indicadores calculados do lote, em JSON, lidos pelo dashboard | Python |

### Detalhes

**`usuario`**
- `email` é único.
- `perfil`: `USER` ou `ADMIN` (padrão `USER`).
- `status`: `PENDENTE`, `APROVADO` ou `BLOQUEADO` (padrão `PENDENTE`).
- `matricula` e `turma` são opcionais.

**`upload_lote`**
- `status`: `PROCESSANDO` durante o processamento, `PROCESSADO` ao final ou `ERRO`, com o motivo em `mensagem`.
- `total_linhas`, `linhas_validas`, `linhas_invalidas` e `linhas_com_aviso` registram a qualidade da planilha.

**`cliente`**
- `nivel`: `A`, `B` ou `C`.
- `faturamento_anual`: valor com duas casas decimais.
- O código do cliente (`codigo_cti`) não se repete dentro do mesmo lote.

**`contrato`**
- Um cliente não pode ter o mesmo serviço contratado duas vezes.

**`estatistica_lote`**
- Um registro por lote. A coluna `resultado` guarda o JSON com resumo, agrupamentos por segmento, nível, consultor, serviço e mês, correlação, maiores clientes, qualidade e as frases de análise. O formato completo está em [`docs/CONTRATO.md`](../docs/CONTRATO.md).

## Relacionamentos

| Relação | Cardinalidade | Regra |
|---|---|---|
| `upload_lote` → `cliente` | 1 para N | Ao excluir um lote, seus clientes são excluídos |
| `upload_lote` → `estatistica_lote` | 1 para 0 ou 1 | Ao excluir um lote, suas estatísticas são excluídas |
| `consultor` → `cliente` | 1 para N | Cada cliente tem um consultor responsável |
| `segmento` → `cliente` | 1 para N | Cada cliente pertence a um segmento |
| `cliente` → `contrato` | 1 para N | Ao excluir um cliente, seus contratos são excluídos |
| `servico` → `contrato` | 1 para N | Cada contrato se refere a um serviço |

`contrato` resolve a relação N para N entre clientes e serviços. A tabela `usuario` não tem relação com os dados das planilhas.

## Criação das tabelas

As tabelas são criadas automaticamente pelo back-end, a cada inicialização, a partir de `backend/src/main/resources/schema.sql`. O script usa `CREATE TABLE IF NOT EXISTS` e pode ser executado várias vezes sem perda de dados.

O arquivo `database/schema.sql` é uma cópia do mesmo script, para consulta ou execução manual em um cliente SQL como o DBeaver. **Os dois arquivos devem ser mantidos iguais.**

Para atualizar um banco criado por uma versão anterior do projeto, remova as tabelas `upload_linha`, `contrato` e `cliente` (com `DROP TABLE ... CASCADE`) e reinicie o back-end.

## Conexão

O projeto funciona com qualquer PostgreSQL. No Neon, a string de conexão tem o formato `postgresql://USUARIO:SENHA@HOST/BANCO?sslmode=require&channel_binding=require`, e cada serviço a utiliza de um jeito:

| Serviço | Arquivo | Variáveis |
|---|---|---|
| Back-end (JDBC) | `backend/.env` | `DB_URL` no formato `jdbc:postgresql://HOST/BANCO?sslmode=require`, sem usuário, senha e `channel_binding`; `DB_USER`; `DB_PASSWORD` |
| Python (SQLAlchemy) | `analytics-python/.env` | `DB_HOST`, `DB_PORT` (5432), `DB_NAME`, `DB_USER`, `DB_PASSWORD`, `DB_SSLMODE=require` |

- O Neon suspende o banco após um período sem uso; a primeira requisição depois disso pode levar alguns segundos.
- O Neon não exige configuração de firewall. No Azure Database for PostgreSQL, é preciso liberar no firewall o IP de quem acessa e o dos serviços publicados.
- A string de conexão contém a senha: não a inclua no repositório.

## Conferência dos dados

Depois de enviar a planilha de exemplo `sample-data/dados.xlsx` pelo site, o banco deve apresentar:

| Verificação | Esperado |
|---|---|
| Status do lote | `PROCESSADO` |
| Linhas do lote (total / válidas / inválidas) | 38 / 32 / 6 |
| Clientes | 32 |
| Segmentos | 6 |
| Consultores | 4 |
| Serviços | 6 |
| Contratos | 61 |
| Média de faturamento (`estatistica_lote`) | 2.261.250 |

As consultas para essas verificações, e outras para explorar os dados, estão em [`consultas-neon.sql`](consultas-neon.sql).
