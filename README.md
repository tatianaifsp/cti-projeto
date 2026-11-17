# CTI Insights

Desenvolvido como projeto integrador do Segundo Semestre/2026 das unidades curriculares **Frameworks Front-end**, **Desenvolvimento Back-end**, **Ciência de Dados**, **Automação Industrial**, **Computação em Nuvem** e **Relações Humanas e Cidadania** do curso de Análise e Desenvolvimento de Sistemas do **SENAI**, a partir da SAG (Situação de Aprendizagem) proposta para a empresa Provedor CTI.

Aplicação web para validação e análise das planilhas comerciais da **Provedor CTI**. O usuário envia a planilha de clientes, vê os erros antes do envio e acompanha os indicadores da carteira em um dashboard. Somente dados válidos são gravados.


| Serviço | Endereço |
|---|---|
| Aplicação (front-end) | https://cti-projeto-deploy.vercel.app |
| API (back-end) | https://cti-api-2plx.onrender.com/api/health |
| Serviço de dados (Python) | https://cti-python.onrender.com/saude |

---

## Funcionalidades

**Usuário**

- `RF-001` Cadastrar usuário: criação de conta com nome, e-mail, senha, matrícula e turma; a conta fica pendente até a aprovação
- `RF-002` Entrar no sistema: login com e-mail e senha, com emissão de token JWT
- `RF-003` Sair do sistema: encerramento da sessão; a sessão também expira automaticamente

**Administração**

- `RF-004` Listar usuários: consulta das contas, com filtro por status (pendente, aprovado ou bloqueado)
- `RF-005` Aprovar usuário: liberação do acesso de uma conta pendente
- `RF-006` Bloquear usuário: suspensão do acesso de uma conta

**Planilha**

- `RF-007` Importar planilha: envio de arquivo `.xlsx` de até 5 MB, por clique ou arrastar e soltar
- `RF-008` Validar planilha: conferência de cada linha no navegador, com relatório dos erros por linha e coluna
- `RF-009` Enviar linhas válidas: apenas as linhas sem erro seguem para o servidor, que as valida novamente
- `RF-010` Processar dados: padronização de nomes, segmentos, valores e datas e gravação de clientes, consultores, segmentos, serviços e contratos

**Dashboard**

- `RF-011` Visualizar indicadores: total de clientes, faturamento total, média, mediana e desvio padrão
- `RF-012` Visualizar gráficos: clientes por segmento e por nível, faturamento por consultor, serviços mais contratados e contratações por mês
- `RF-013` Consultar maiores clientes: os cinco clientes com maior faturamento
- `RF-014` Ler análises: frases geradas automaticamente a partir dos indicadores
- `RF-015` Consultar planilhas anteriores: seleção de um lote já processado

## Tecnologias

| Camada | Tecnologias |
|---|---|
| Front-end | Vue 3, Vite, Pinia, Vue Router, Axios, Tailwind CSS, Chart.js |
| Back-end | Java 21, Spring Boot 3.5, Spring Security, JWT, Spring Data JPA |
| Ciência de dados | Python, FastAPI, pandas, SciPy, SQLAlchemy |
| Banco de dados | PostgreSQL (Neon) |
| Hospedagem | Vercel (front-end) e Render (back-end e Python) |

## Fluxo de dados

```mermaid
flowchart TD

subgraph grupo_front["Front-end - Vue 3"]
  front_upload["Tela de envio<br/>[UploadView.vue]"]
  front_relatorio["Relatório de validação<br/>[RelatorioView.vue]"]
  front_store["Leitura e validação da planilha<br/>[stores/upload.js]"]
  front_auth["Sessão do usuário<br/>[stores/auth.js]"]
  front_admin["Liberação de acesso<br/>[AdminUsuariosView.vue]"]
  front_dash["Dashboard<br/>[DashboardView.vue]"]
  front_api["Cliente HTTP<br/>[services/api.js]"]
end

subgraph grupo_back["Back-end - Spring Boot"]
  back_upload_ctrl["Endpoint de upload<br/>[UploadController.java]"]
  back_upload_srv["Orquestração do upload<br/>[UploadService.java]"]
  back_validacao["Validação das linhas<br/>[ClienteValidacaoService.java]"]
  back_etl["Cliente do ETL<br/>[PythonEtlService.java]"]
  back_auth_ctrl["Endpoints de autenticação<br/>[AuthController.java]"]
  back_auth_srv["Regras de autenticação<br/>[AuthService.java]"]
  back_admin_ctrl["Endpoints de administração<br/>[AdminUsuarioController.java]"]
  back_usuario_srv["Gestão de usuários<br/>[UsuarioService.java]"]
  back_repo[("Repositório de usuários<br/>[UsuarioRepository.java]")]
  back_dash_ctrl["Endpoint do dashboard<br/>[DashboardController.java]"]
  back_dash_srv["Consultas do dashboard<br/>[DashboardService.java]"]
end

subgraph grupo_py["Ciência de dados - Python"]
  py_api["API do ETL<br/>[api.py]"]
  py_main["Coordenação do processamento<br/>[main.py]"]
  py_limpeza["Limpeza e padronização<br/>[limpeza.py]"]
  py_analise["Indicadores estatísticos<br/>[analise.py]"]
  py_insights["Frases de análise<br/>[insights.py]"]
  py_banco["Gravação no banco<br/>[banco.py]"]
end

usuario(("Usuário"))
banco[("PostgreSQL - Neon")]

usuario -->|"envia a planilha .xlsx"| front_upload
front_upload -->|"analisarArquivo()"| front_store
front_store -->|"válidas, inválidas e avisos"| front_relatorio
front_relatorio -->|"liberarParaBackend()"| front_store
front_store -->|"somente linhas válidas"| front_api
front_api -->|"POST /api/uploads/validadas"| back_upload_ctrl
back_upload_ctrl -->|"delega"| back_upload_srv
back_upload_srv -->|"valida novamente"| back_validacao
back_upload_srv -->|"encaminha as válidas"| back_etl
back_etl -->|"POST /etl + X-ETL-Key"| py_api
py_api -->|"processar_lote()"| py_main
py_main -->|"padroniza"| py_limpeza
py_main -->|"calcula"| py_analise
py_analise -->|"interpreta"| py_insights
py_main -->|"persiste"| py_banco
py_banco -->|"grava lote, clientes, contratos e estatísticas"| banco

usuario -->|"entra no sistema"| front_auth
front_auth -->|"login e cadastro"| front_api
front_api -->|"POST /api/auth/login"| back_auth_ctrl
back_auth_ctrl -->|"delega"| back_auth_srv
back_auth_srv -->|"lê e grava usuários"| back_repo

usuario -->|"administra contas"| front_admin
front_admin -->|"lista e altera status"| front_api
front_api -->|"/api/admin/usuarios"| back_admin_ctrl
back_admin_ctrl -->|"delega"| back_usuario_srv
back_usuario_srv -->|"consulta usuários"| back_repo
back_repo -->|"tabela usuario"| banco

usuario -->|"consulta os gráficos"| front_dash
front_dash -->|"obter() e lotes()"| front_api
front_api -->|"GET /api/dashboard"| back_dash_ctrl
back_dash_ctrl -->|"delega"| back_dash_srv
back_dash_srv -->|"lê lotes e estatísticas"| banco

classDef tomAzul fill:#dbeafe,stroke:#2563eb,stroke-width:1.5px,color:#172554
classDef tomAmbar fill:#fef3c7,stroke:#d97706,stroke-width:1.5px,color:#78350f
classDef tomVerde fill:#dcfce7,stroke:#16a34a,stroke-width:1.5px,color:#14532d
classDef tomRosa fill:#ffe4e6,stroke:#e11d48,stroke-width:1.5px,color:#881337
classDef tomIndigo fill:#e0e7ff,stroke:#4f46e5,stroke-width:1.5px,color:#312e81
classDef tomNeutro fill:#f8fafc,stroke:#334155,stroke-width:1.5px,color:#0f172a
class usuario,front_upload,front_relatorio,front_store,front_auth,front_admin,front_api tomAzul
class back_upload_ctrl,back_upload_srv,back_validacao,back_etl tomAmbar
class py_api,py_main,py_limpeza,py_analise,py_insights,py_banco tomVerde
class back_auth_ctrl,back_auth_srv,back_admin_ctrl,back_usuario_srv,back_repo tomRosa
class front_dash,back_dash_ctrl,back_dash_srv tomIndigo
class banco tomNeutro
```

## Banco de dados

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

A tabela `usuario` é gravada pelo back-end. As demais são gravadas pelo serviço Python a cada planilha processada.

## Estrutura do repositório

```
├── frontend/           # aplicação Vue
├── backend/            # API Spring Boot
├── analytics-python/   # ETL e estatística (FastAPI)
├── database/           # schema e consultas SQL
├── docs/               # guias de execução e deploy
├── sample-data/        # planilha de exemplo
└── testes/             # teste integrado
```

## Como executar

**Pré-requisitos:** Node.js 20+, Java 21, Maven 3.9+, Python 3.10+ e um banco PostgreSQL (Neon).

Em cada pasta (`frontend`, `backend` e `analytics-python`), copie `.env.example` para `.env` e preencha as variáveis. Depois, inicie os serviços nesta ordem:

```bash
# 1. Python (porta 8000)
cd analytics-python
python -m venv .venv
.venv\Scripts\activate        # Linux/macOS: source .venv/bin/activate
pip install -r requirements.txt
uvicorn api:app --port 8000

# 2. Back-end (porta 8080)
cd backend
mvn spring-boot:run

# 3. Front-end (porta 5173)
cd frontend
npm install
npm run dev
```

Acesse http://localhost:5173. As tabelas e o usuário administrador são criados automaticamente na primeira execução do back-end.

### Variáveis de ambiente

| Serviço | Variáveis |
|---|---|
| `frontend` | `VITE_API_URL` |
| `backend` | `DB_URL`, `DB_USER`, `DB_PASSWORD`, `JWT_SECRET`, `CORS_ORIGINS`, `ADMIN_EMAIL`, `ADMIN_PASSWORD`, `ETL_URL`, `ETL_KEY` |
| `analytics-python` | `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, `DB_PASSWORD`, `DB_SSLMODE`, `ETL_API_KEY` |

`ETL_KEY` (back-end) e `ETL_API_KEY` (Python) devem ter o mesmo valor.

## API

| Método | Rota | Acesso |
|---|---|---|
| `POST` | `/api/auth/register` | Público |
| `POST` | `/api/auth/login` | Público |
| `GET` | `/api/auth/me` | Autenticado |
| `GET` | `/api/admin/usuarios` | Administrador |
| `PATCH` | `/api/admin/usuarios/{id}/status` | Administrador |
| `POST` | `/api/uploads/validadas` | Autenticado |
| `GET` | `/api/dashboard` | Autenticado |
| `GET` | `/api/dashboard/lotes` | Autenticado |

Rotas autenticadas exigem o cabeçalho `Authorization: Bearer <token>`. O formato dos dados trocados entre os serviços está em [`docs/CONTRATO.md`](docs/CONTRATO.md).

## Testes

```bash
python testes/testar_tudo.py
```

O script verifica o serviço Python e as rotas principais do back-end. Com a planilha `sample-data/dados.xlsx`, o resultado esperado é 38 linhas lidas, 32 válidas e 6 rejeitadas.

## Documentação

- [Back-end](backend/README.md)
- [Front-end](frontend/README.md)
- [Ciência de dados](analytics-python/README.md)
- [Banco de dados](database/README.md)
