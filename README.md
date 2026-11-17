# CTI Insights

**Projeto Integrador · SENAI · Análise e Desenvolvimento de Sistemas**

Aplicação web que transforma as planilhas comerciais da empresa **Provedor CTI** em informação confiável: valida os dados, descarta o que está errado e apresenta indicadores e gráficos sobre a carteira de clientes.

**Acesse a aplicação:** https://cti-projeto-deploy.vercel.app

---

## Apresentação do projeto

A CTI Comunicação de Dados e Tecnologia é um provedor de internet que registra seus dados comerciais em planilhas Excel: clientes, consultores, segmentos de mercado, nível do cliente (A, B ou C), faturamento anual e serviços contratados. Com o crescimento da carteira, ficou difícil identificar nessas planilhas quais segmentos faturam mais, como está o desempenho de cada consultor e onde estão os erros de cadastro.

O **CTI Insights** resolve esse problema em três etapas:

1. **Validação.** O usuário envia a planilha e vê, antes de qualquer gravação, quais linhas têm erro e por quê.
2. **Tratamento.** Somente as linhas válidas seguem para o servidor, que as confere novamente e as encaminha a um serviço de ciência de dados para padronização e cálculo dos indicadores.
3. **Análise.** Um dashboard apresenta os indicadores estatísticos, os gráficos da carteira e frases que interpretam os resultados.

O projeto integra os conteúdos das unidades curriculares **Frameworks Front-end**, **Desenvolvimento Back-end**, **Ciência de Dados**, **Automação Industrial**, **Computação em Nuvem** e **Relações Humanas e Cidadania**, e utiliza apenas serviços gratuitos de hospedagem.

| | |
|---|---|
| **Aplicação publicada** | https://cti-projeto-deploy.vercel.app |
| **API (back-end)** | https://cti-api-2plx.onrender.com/api/health |
| **Serviço de dados (Python)** | https://cti-python.onrender.com/saude |
| **Repositório** | https://github.com/tatianaifsp/cti-projeto-deploy |

---

## Tecnologias

| Camada | Tecnologias | Pasta | Hospedagem |
|---|---|---|---|
| Front-end | Vue 3, Vite 6, Pinia, Vue Router, Axios, Tailwind CSS 4, Chart.js | `frontend/` | Vercel |
| Back-end | Java 21, Spring Boot 3.5.6, Spring Security, JWT, Spring Data JPA | `backend/` | Render |
| Ciência de dados | Python, FastAPI, pandas, NumPy, SciPy, SQLAlchemy | `analytics-python/` | Render |
| Banco de dados | PostgreSQL | `database/` | Neon |

---

## Requisitos funcionais

**Usuários e acesso**

| Código | Requisito | Descrição |
|---|---|---|
| `RF-001` | Cadastrar usuário | Criação de conta com nome, e-mail, senha, matrícula e turma. A conta é criada com status PENDENTE |
| `RF-002` | Autenticar usuário | Login com e-mail e senha; retorna um token JWT |
| `RF-003` | Manter sessão | O token é guardado no navegador, conferido ao abrir o site e descartado no logout ou quando expira |
| `RF-004` | Listar usuários | O administrador consulta os usuários, com filtro por status |
| `RF-005` | Liberar ou bloquear acesso | O administrador altera o status da conta para APROVADO, BLOQUEADO ou PENDENTE |

**Importação da planilha**

| Código | Requisito | Descrição |
|---|---|---|
| `RF-006` | Importar planilha | Seleção de arquivo `.xlsx` de até 5 MB, por clique ou arrastar e soltar |
| `RF-007` | Validar no navegador | Validação de cada linha e exibição de um relatório com erros e avisos por linha e coluna |
| `RF-008` | Enviar linhas válidas | Somente as linhas sem erro são enviadas ao servidor |
| `RF-009` | Revalidar no servidor | O back-end aplica as mesmas regras e devolve as linhas rejeitadas |

**Tratamento e análise dos dados**

| Código | Requisito | Descrição |
|---|---|---|
| `RF-010` | Padronizar dados | Ajuste de nomes, segmentos, valores monetários e datas |
| `RF-011` | Gravar o lote | Gravação do lote, dos clientes, consultores, segmentos, serviços e contratos |
| `RF-012` | Calcular indicadores | Média, mediana, desvio padrão, quartis, distribuições por segmento, nível, consultor, serviço e mês, e correlação |
| `RF-013` | Gerar análises | Produção de frases interpretando os indicadores |

**Dashboard**

| Código | Requisito | Descrição |
|---|---|---|
| `RF-014` | Exibir dashboard | Cartões de indicadores, gráficos, maiores clientes e frases de análise do lote mais recente |
| `RF-015` | Consultar lotes anteriores | Seleção de um lote já gravado para exibir seus indicadores |

---

## Requisitos não funcionais

| Código | Requisito |
|---|---|
| `RNF-001` | Senhas armazenadas apenas como hash BCrypt |
| `RNF-002` | Autenticação *stateless* com JWT assinado (HS256) e validade configurável |
| `RNF-003` | Validação dos dados no cliente e no servidor, com as mesmas regras |
| `RNF-004` | Linhas inválidas nunca são persistidas |
| `RNF-005` | Credenciais e segredos fora do código, em variáveis de ambiente |
| `RNF-006` | Comunicação entre back-end e Python protegida por chave (`X-ETL-Key`) |
| `RNF-007` | Erros da API padronizados no formato `{"erro": "mensagem"}` |
| `RNF-008` | Limites de entrada: arquivo de até 5 MB e até 5000 linhas por envio |
| `RNF-009` | Infraestrutura em planos gratuitos (Vercel, Render e Neon) |

---

## Instalação rápida

Pré-requisitos: Git, Node.js 20+, Java 21, Maven 3.9+ e Python 3.10+. Detalhes na seção [Pré-requisitos](#4-pré-requisitos).

**1. Clonar o repositório**

```bash
git clone https://github.com/tatianaifsp/cti-projeto-deploy.git
cd cti-projeto-deploy
```

**2. Configurar as variáveis de ambiente.** Em `frontend/`, `backend/` e `analytics-python/`, copie o `.env.example` para `.env` e preencha (ver [seção 5](#5-configuração-do-banco-de-dados) e [seção 6](#6-instalação-e-execução-local)).

**3. Iniciar as três aplicações**, cada uma em um terminal e nesta ordem:

```bash
# Terminal 1 · Python
cd analytics-python
python -m venv .venv
.venv\Scripts\activate              # Linux/macOS: source .venv/bin/activate
pip install -r requirements.txt
uvicorn api:app --port 8000

# Terminal 2 · Back-end
cd backend
mvn spring-boot:run

# Terminal 3 · Front-end
cd frontend
npm install
npm run dev
```

**4. Acessar** http://localhost:5173 com o administrador definido no `.env` do back-end (padrão: `admin@cti.local` / `Admin@12345`).

---

## Implementações futuras

- **Chatbot de perguntas fixas** sobre os dados da carteira, sem uso de inteligência artificial.

---

# Documentação técnica

## Sumário da documentação técnica

1. [Arquitetura](#1-arquitetura)
2. [Estrutura do repositório](#2-estrutura-do-repositório)
3. [Fluxo de dados](#3-fluxo-de-dados)
4. [Pré-requisitos](#4-pré-requisitos)
5. [Configuração do banco de dados](#5-configuração-do-banco-de-dados)
6. [Instalação e execução local](#6-instalação-e-execução-local)
7. [Front-end em detalhe](#7-front-end-em-detalhe)
8. [Back-end](#8-back-end)
9. [Ciência de dados (Python)](#9-ciência-de-dados-python)
10. [Modelo de dados](#10-modelo-de-dados)
11. [Testes](#11-testes)
12. [Deploy](#12-deploy)
13. [Solução de problemas](#13-solução-de-problemas)
14. [Documentação complementar](#14-documentação-complementar)

---

## 1. Arquitetura

O sistema é dividido em três aplicações independentes, que se comunicam por HTTP, e um banco de dados compartilhado. O caminho completo dos dados está no diagrama da [seção 3](#3-fluxo-de-dados).

| Aplicação | Responsabilidade | Grava no banco |
|---|---|---|
| **Front-end** | Interface. Lê o Excel no navegador, valida cada linha, envia apenas as válidas e exibe o dashboard | Não |
| **Back-end** | Segurança e regras de negócio: autenticação (JWT), nova validação das linhas e encaminhamento ao Python | Apenas a tabela `usuario` |
| **Python** | ETL: padroniza os dados, calcula os indicadores estatísticos e grava tudo no banco | Lotes, clientes, contratos e estatísticas |

**Decisões de projeto**

- **Validação em duas etapas.** O front-end valida para dar retorno imediato ao usuário; o back-end valida de novo porque qualquer código no navegador pode ser alterado. As regras são as mesmas nas duas camadas.
- **Linhas inválidas nunca são gravadas.** Elas são apenas exibidas ao usuário (na tela e na resposta da API).
- **Responsabilidade única de gravação.** Os dados da planilha são gravados apenas pelo Python. O Java não tem entidades para eles.
- **Leitura pelo back-end.** O dashboard lê os resultados por meio da API Java (`GET /api/dashboard`), e nunca acessa o banco diretamente.

---

## 2. Estrutura do repositório

```
cti-projeto-deploy/
├── frontend/              # aplicação Vue (interface)
│   ├── src/
│   │   ├── main.js        # inicialização do app
│   │   ├── router/        # rotas e guarda de navegação
│   │   ├── stores/        # estado global (Pinia): auth.js e upload.js
│   │   ├── services/      # api.js: cliente HTTP (axios)
│   │   ├── views/         # telas
│   │   └── components/    # componentes reutilizáveis
│   ├── .env.example
│   ├── vite.config.js
│   └── vercel.json        # regra de rotas para o deploy na Vercel
├── backend/               # API Spring Boot (ver backend/README.md)
├── analytics-python/      # ETL e estatística em Python (ver analytics-python/README.md)
├── database/              # schema.sql e consultas de conferência
├── docs/                  # guias passo a passo
├── sample-data/           # dados.xlsx: planilha de exemplo
├── testes/                # testar_tudo.py: teste automatizado de todas as partes
└── .vscode/tasks.json     # tarefas do VS Code para iniciar cada serviço
```

---

## 3. Fluxo de dados

O diagrama mostra os caminhos que os dados percorrem no sistema: o envio da planilha, a autenticação, a administração de contas e a consulta ao dashboard. Cada cor representa um fluxo, e cada caixa indica o arquivo responsável.

```mermaid
flowchart TD

subgraph grupo_front["Front-end · Vue 3"]
  front_upload["Tela de envio<br/>[UploadView.vue]"]
  front_relatorio["Relatório de validação<br/>[RelatorioView.vue]"]
  front_store["Leitura e validação da planilha<br/>[stores/upload.js]"]
  front_auth["Sessão do usuário<br/>[stores/auth.js]"]
  front_admin["Liberação de acesso<br/>[AdminUsuariosView.vue]"]
  front_dash["Dashboard<br/>[DashboardView.vue]"]
  front_api["Cliente HTTP<br/>[services/api.js]"]
end

subgraph grupo_back["Back-end · Spring Boot"]
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

subgraph grupo_py["Ciência de dados · Python"]
  py_api["API do ETL<br/>[api.py]"]
  py_main["Coordenação do processamento<br/>[main.py]"]
  py_limpeza["Limpeza e padronização<br/>[limpeza.py]"]
  py_analise["Indicadores estatísticos<br/>[analise.py]"]
  py_insights["Frases de análise<br/>[insights.py]"]
  py_banco["Gravação no banco<br/>[banco.py]"]
end

usuario(("Usuário"))
banco[("PostgreSQL · Neon")]

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

click front_upload "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/frontend/src/views/UploadView.vue"
click front_relatorio "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/frontend/src/views/RelatorioView.vue"
click front_store "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/frontend/src/stores/upload.js"
click front_auth "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/frontend/src/stores/auth.js"
click front_admin "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/frontend/src/views/AdminUsuariosView.vue"
click front_dash "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/frontend/src/views/DashboardView.vue"
click front_api "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/frontend/src/services/api.js"
click back_upload_ctrl "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/backend/src/main/java/com/cti/provedor/controller/UploadController.java"
click back_upload_srv "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/backend/src/main/java/com/cti/provedor/service/UploadService.java"
click back_validacao "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/backend/src/main/java/com/cti/provedor/service/ClienteValidacaoService.java"
click back_etl "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/backend/src/main/java/com/cti/provedor/service/PythonEtlService.java"
click back_auth_ctrl "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/backend/src/main/java/com/cti/provedor/controller/AuthController.java"
click back_auth_srv "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/backend/src/main/java/com/cti/provedor/service/AuthService.java"
click back_admin_ctrl "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/backend/src/main/java/com/cti/provedor/controller/AdminUsuarioController.java"
click back_usuario_srv "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/backend/src/main/java/com/cti/provedor/service/UsuarioService.java"
click back_repo "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/backend/src/main/java/com/cti/provedor/repository/UsuarioRepository.java"
click back_dash_ctrl "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/backend/src/main/java/com/cti/provedor/controller/DashboardController.java"
click back_dash_srv "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/backend/src/main/java/com/cti/provedor/service/DashboardService.java"
click py_api "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/analytics-python/api.py"
click py_main "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/analytics-python/main.py"
click py_limpeza "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/analytics-python/limpeza.py"
click py_analise "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/analytics-python/analise.py"
click py_insights "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/analytics-python/insights.py"
click py_banco "https://github.com/tatianaifsp/cti-projeto-deploy/blob/main/analytics-python/banco.py"

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

| Fluxo | Cor | Caminho |
|---|---|---|
| Envio da planilha | Azul → âmbar → verde | `upload.js` lê e valida o Excel no navegador; o relatório mostra os erros; somente as linhas válidas vão para `POST /api/uploads/validadas`. O Spring valida novamente e envia as aprovadas para `POST /etl`. O Python padroniza, calcula e grava no banco |
| Autenticação | Azul → rosa | `auth.js` envia e-mail e senha; o `AuthService` confere a senha com BCrypt e o status da conta e devolve o token JWT |
| Administração | Azul → rosa | A tela de liberação de acesso lista os usuários e altera o status por meio do `UsuarioService` |
| Dashboard | Índigo | A tela de gráficos chama `GET /api/dashboard`; o `DashboardService` lê os lotes e o JSON de estatísticas gravado pelo Python |

> Os nomes dos arquivos no diagrama são clicáveis no GitHub quando o navegador permite.

O formato exato dos dados trocados entre as partes (colunas da planilha, corpo das requisições e JSON das estatísticas) está documentado em [`docs/CONTRATO.md`](docs/CONTRATO.md). **Qualquer alteração nesse contrato deve ser feita nas três aplicações.**

---

## 4. Pré-requisitos

| Ferramenta | Versão | Usada em | Verificação |
|---|---|---|---|
| Git | qualquer recente | clonar o projeto | `git --version` |
| Node.js | 20 LTS ou superior | front-end | `node -v` |
| npm | incluído no Node.js | front-end | `npm -v` |
| Java (JDK) | 21 | back-end | `java -version` |
| Maven | 3.9 ou superior | back-end | `mvn -v` |
| Python | 3.10 ou superior | ciência de dados | `python --version` |
| Conta no Neon | plano gratuito | banco de dados | — |

Editor recomendado: **VS Code**, com as extensões sugeridas em `.vscode/extensions.json`.

```bash
git clone https://github.com/tatianaifsp/cti-projeto-deploy.git
cd cti-projeto-deploy
```

---

## 5. Configuração do banco de dados

Crie um projeto no [Neon](https://neon.tech) e copie a string de conexão, que tem este formato:

```
postgresql://USUARIO:SENHA@HOST/BANCO?sslmode=require&channel_binding=require
```

O back-end e o Python leem essa conexão de formas diferentes:

| Aplicação | Arquivo | Como preencher |
|---|---|---|
| Back-end (JDBC) | `backend/.env` | `DB_URL=jdbc:postgresql://HOST/BANCO?sslmode=require`, `DB_USER=USUARIO`, `DB_PASSWORD=SENHA` |
| Python (SQLAlchemy) | `analytics-python/.env` | `DB_HOST=HOST`, `DB_PORT=5432`, `DB_NAME=BANCO`, `DB_USER=USUARIO`, `DB_PASSWORD=SENHA`, `DB_SSLMODE=require` |

- Remova o parâmetro `channel_binding`: o driver JDBC não precisa dele.
- **Não é necessário criar as tabelas manualmente.** O back-end executa `schema.sql` (com `CREATE TABLE IF NOT EXISTS`) toda vez que inicia.
- A string contém a senha. Nunca a inclua no Git: os arquivos `.env` já estão no `.gitignore`.

Para usar o **Azure Database for PostgreSQL** (servidor flexível), o processo é o mesmo; libere no firewall o seu IP e o dos serviços publicados.

---

## 6. Instalação e execução local

As três aplicações rodam ao mesmo tempo, **cada uma em um terminal**. Inicie-as nesta ordem, porque cada uma depende da seguinte:

| Ordem | Aplicação | Motivo |
|---|---|---|
| 1 | Python | O back-end o chama durante o upload |
| 2 | Back-end | Cria as tabelas e o administrador; o front-end depende dele |
| 3 | Front-end | Interface usada pelo usuário |

> No VS Code, as três aplicações também podem ser iniciadas por **Terminal → Run Task**, usando as tarefas definidas em `.vscode/tasks.json`.

### 6.1 Python (terminal 1)

```bash
cd analytics-python
python -m venv .venv                  # cria um ambiente virtual isolado
.venv\Scripts\activate                # Windows
# source .venv/bin/activate           # Linux/macOS
pip install -r requirements.txt       # instala pandas, FastAPI, SQLAlchemy etc.
copy .env.example .env                # Windows (Linux/macOS: cp .env.example .env)
```

Preencha o `.env` com os dados do banco (seção 5) e, se quiser, com `ETL_API_KEY`. Em seguida:

```bash
python testar_conexao.py              # opcional: confirma o acesso ao banco
uvicorn api:app --port 8000
```

**Verificação:** http://localhost:8000/saude deve responder `{"status":"ok"}`. A documentação interativa da API fica em http://localhost:8000/docs.

### 6.2 Back-end (terminal 2)

```bash
cd backend
copy .env.example .env                # Windows (Linux/macOS: cp .env.example .env)
```

| Variável | Descrição |
|---|---|
| `DB_URL`, `DB_USER`, `DB_PASSWORD` | Conexão com o banco (seção 5) |
| `JWT_SECRET` | Segredo que assina os tokens; no mínimo 32 caracteres |
| `CORS_ORIGINS` | Origem do front-end: `http://localhost:5173` |
| `ADMIN_EMAIL`, `ADMIN_PASSWORD` | Administrador criado na primeira execução |
| `ETL_URL` | Endpoint do Python: `http://localhost:8000/etl` |
| `ETL_KEY` | Deve ser igual a `ETL_API_KEY` do Python (vazio nas duas = sem chave) |

```bash
mvn spring-boot:run
```

**Verificação:** após a mensagem `Started CtiProvedorApplication`, http://localhost:8080/api/health deve responder `{"status":"ok"}`.

### 6.3 Front-end (terminal 3)

```bash
cd frontend
npm install                           # instala as dependências do package.json
copy .env.example .env                # Windows (Linux/macOS: cp .env.example .env)
npm run dev
```

O `.env` do front-end tem uma única variável:

```properties
VITE_API_URL=http://localhost:8080/api
```

**Verificação:** abra http://localhost:5173 e entre com o administrador definido em `ADMIN_EMAIL` e `ADMIN_PASSWORD` (padrão: `admin@cti.local` / `Admin@12345`).

### 6.4 Primeiro uso

1. Entre como administrador.
2. Abra **Enviar planilha** e selecione `sample-data/dados.xlsx`.
3. O relatório deve mostrar **38 linhas: 32 válidas e 6 com erro**.
4. Clique em **Liberar** para enviar as 32 linhas válidas.
5. Abra **Gráficos** (`/dashboard`) para ver os indicadores calculados pelo Python.

---

## 7. Front-end em detalhe

### 7.1 Dependências

| Pacote | Função |
|---|---|
| `vue` | Framework de interface (Composition API com `<script setup>`) |
| `vite` + `@vitejs/plugin-vue` | Servidor de desenvolvimento e empacotador da build de produção |
| `vue-router` | Navegação entre telas sem recarregar a página (SPA) |
| `pinia` | Gerenciamento de estado global (stores) |
| `axios` | Cliente HTTP para chamar o back-end |
| `read-excel-file` | Leitura do `.xlsx` diretamente no navegador, sem enviar o arquivo ao servidor |
| `chart.js` | Gráficos do dashboard |
| `tailwindcss` + `@tailwindcss/vite` | Estilização por classes utilitárias |
| `@fontsource/barlow` | Fonte Barlow servida localmente |

### 7.2 Scripts

| Comando | O que faz |
|---|---|
| `npm run dev` | Inicia o servidor de desenvolvimento em http://localhost:5173, com recarga automática |
| `npm run build` | Gera a versão de produção, otimizada, na pasta `dist/` |
| `npm run preview` | Serve localmente a pasta `dist/` para conferir a build antes do deploy |

### 7.3 Organização em camadas

As telas nunca chamam o axios diretamente. Cada camada tem uma responsabilidade:

| Camada | Pasta | Responsabilidade |
|---|---|---|
| Telas | `views/` | Exibir dados e capturar ações do usuário |
| Componentes | `components/` | Partes visuais reutilizadas por várias telas |
| Estado | `stores/` | Guardar dados compartilhados e implementar a lógica (login, leitura e validação da planilha) |
| Comunicação | `services/api.js` | Concentrar todas as chamadas HTTP ao back-end |
| Navegação | `router/index.js` | Definir as rotas e quem pode acessar cada uma |

### 7.4 Inicialização (`main.js`)

1. Cria a aplicação Vue com `createApp(App)`.
2. Registra o Pinia (`app.use(createPinia())`).
3. Chama `useAuthStore().validarSessao()`: se houver um token salvo, consulta `GET /api/auth/me` para confirmar que ele ainda é válido; caso contrário, encerra a sessão.
4. Registra o roteador e monta o app em `<div id="app">`.

### 7.5 Comunicação com o back-end (`services/api.js`)

- **URL base.** Lida de `import.meta.env.VITE_API_URL`. O Vite só expõe ao código variáveis com o prefixo `VITE_`, e o valor é **incorporado no momento da build**: ao mudar o endereço da API em produção, é preciso gerar a build de novo.
- **Interceptador de requisição.** Adiciona `Authorization: Bearer <token>` a todas as chamadas.
- **Interceptador de resposta.** Se a API responder `401` em uma rota que não seja de login ou cadastro, o token expirou: a sessão é encerrada.
- **Tratamento de erros.** `mensagemDeErro()` converte qualquer falha numa mensagem legível. O back-end responde erros no formato `{"erro": "mensagem"}`.
- **Serviços disponíveis:**

| Serviço | Funções | Rotas chamadas |
|---|---|---|
| `authService` | `entrar`, `cadastrar`, `eu` | `/auth/login`, `/auth/register`, `/auth/me` |
| `adminService` | `listar`, `alterarStatus` | `/admin/usuarios`, `/admin/usuarios/{id}/status` |
| `uploadService` | `enviarValidadas` | `/uploads/validadas` |
| `dashboardService` | `obter`, `lotes` | `/dashboard`, `/dashboard/lotes` |

> Para evitar importação circular entre a store e o service, a store de autenticação entrega ao `api.js` duas funções, uma que lê o token e outra que faz o logout, por meio de `configurarApi()`.

### 7.6 Stores (Pinia)

**`stores/auth.js` — sessão do usuário**

| Membro | Tipo | Descrição |
|---|---|---|
| `token`, `usuario` | estado | Token JWT e dados do usuário logado |
| `logado`, `ehAdmin` | computados | Indicam se há sessão e se o perfil é `ADMIN` |
| `entrar()`, `cadastrar()`, `sair()` | ações | Login, cadastro (conta criada como PENDENTE) e logout |
| `validarSessao()` | ação | Confere o token salvo ao abrir o site |

A sessão é persistida no `localStorage`, na chave `cti-sessao`, para sobreviver ao recarregamento da página.

**`stores/upload.js` — leitura, validação e envio da planilha**

| Etapa | Função | Detalhes |
|---|---|---|
| 1. Conferir o arquivo | `analisarArquivo()` | Aceita apenas `.xlsx` de até 5 MB |
| 2. Ler | `lerPlanilha()` | Lê a aba `upload_clientes` (ou a primeira aba, se ela não existir); normaliza os nomes das colunas e ignora linhas vazias |
| 3. Validar | `validarLinhas()` | Aplica as mesmas regras do back-end (`ClienteValidacaoService`): obrigatórios, tamanho, código único, segmento, nível A/B/C, faturamento, serviços, data e UF |
| 4. Enviar | `liberarParaBackend()` | Envia **somente** as linhas válidas, informando em `descartadas` quantas ficaram de fora |

Valores computados usados pelas telas: `validas`, `invalidas`, `comAviso` e `podeLiberar`.

**Colunas esperadas na planilha:** `codigo_cliente`, `nome_cliente`, `consultor`, `segmento`, `nivel_cliente`, `faturamento_anual`, `servicos_contratados`, `data_contratacao` (obrigatórias), `cidade` e `uf` (opcionais).

### 7.7 Rotas e controle de acesso

| Caminho | Tela | Acesso |
|---|---|---|
| `/` | `HomeView` | Público |
| `/login` | `LoginView` | Apenas visitante |
| `/cadastro` | `CadastroView` | Apenas visitante |
| `/upload` | `UploadView` | Autenticado |
| `/relatorio` | `RelatorioView` | Autenticado |
| `/dashboard` | `DashboardView` | Autenticado |
| `/admin/usuarios` | `AdminUsuariosView` | Autenticado com perfil ADMIN |
| qualquer outro | `NotFoundView` | Público |

A guarda global (`router.beforeEach`) lê os metadados de cada rota:
- `requerLogin` sem sessão → redireciona para `/login`;
- `requerAdmin` sem perfil ADMIN → redireciona para `/`;
- `soVisitante` com sessão ativa → redireciona para `/upload`.

> A guarda de rotas melhora a experiência do usuário, mas **não é uma barreira de segurança**: quem protege os dados é o back-end, que exige o token em cada requisição.

As telas são carregadas sob demanda (`component: () => import(...)`), o que reduz o tamanho do carregamento inicial.

### 7.8 Telas

| Tela | Função |
|---|---|
| `HomeView` | Página inicial |
| `LoginView` / `CadastroView` | Autenticação e criação de conta |
| `UploadView` | Seleção do arquivo (clique ou arrastar e soltar) |
| `RelatorioView` | Resultado da validação no navegador e botão **Liberar** |
| `AdminUsuariosView` | Aprovação e bloqueio de contas (somente ADMIN) |
| `DashboardView` | Indicadores, 5 gráficos, maiores clientes e frases de análise do último lote |

Componentes: `AppHeader` (menu), `BaseChart` (encapsula um gráfico Chart.js de barra, linha ou rosca), `KpiCard` (cartão de indicador) e `StatusBadge` (status do lote).

---

## 8. Back-end

API REST em Spring Boot organizada em camadas (`controller`, `service`, `repository`, `model`, `dto`, `config`). A documentação completa, com o fluxo do upload, as regras de validação, a autenticação e o código comentado, está em **[`backend/README.md`](backend/README.md)**.

| Método | Rota | Acesso |
|---|---|---|
| `GET` | `/api/health` | Público |
| `POST` | `/api/auth/register`, `/api/auth/login` | Público |
| `GET` | `/api/auth/me` | Autenticado |
| `GET` | `/api/admin/usuarios` | ADMIN |
| `PATCH` | `/api/admin/usuarios/{id}/status` | ADMIN |
| `POST` | `/api/uploads/validadas` | Autenticado |
| `GET` | `/api/dashboard`, `/api/dashboard/lotes` | Autenticado |

---

## 9. Ciência de dados (Python)

API em FastAPI que recebe as linhas já validadas e executa o ETL. Ela **não devolve dados**: grava no banco e responde `{"ok": true}`.

| Arquivo | Função |
|---|---|
| `api.py` | Define as rotas e confere a chave `X-ETL-Key` |
| `main.py` | Coordena o processamento: cria o lote, limpa, calcula e grava |
| `limpeza.py` | Padroniza textos (`ana souza` → `Ana Souza`, `IND.` → `Indústria`), números e datas |
| `analise.py` | Calcula média, mediana, desvio padrão, quartis, totais por segmento, nível, consultor, serviço e mês, e a correlação |
| `insights.py` | Gera as frases de análise exibidas no dashboard |
| `banco.py` | Único módulo que acessa o banco |
| `config.py` | Lê o `.env` |

| Rota | Função |
|---|---|
| `GET /saude` | Verifica se a API está em execução |
| `POST /etl` | Recebe o lote do back-end, processa e grava |
| `POST /testar` | Processa um JSON de teste **sem gravar** (use `amostras/clientes.json`) |

Detalhes em [`analytics-python/README.md`](analytics-python/README.md) e [`docs/CIENCIA-DE-DADOS.txt`](docs/CIENCIA-DE-DADOS.txt).

---

## 10. Modelo de dados

| Tabela | Conteúdo | Gravada por |
|---|---|---|
| `usuario` | Credenciais (senha em BCrypt), perfil e status | Back-end |
| `upload_lote` | Uma linha por planilha enviada: nome, status e contagens | Python |
| `cliente` | Clientes válidos | Python |
| `consultor`, `segmento`, `servico` | Tabelas de domínio, sem repetição | Python |
| `contrato` | Relação cliente × serviço, com data | Python |
| `estatistica_lote` | Resultado dos cálculos em JSON, lido pelo dashboard | Python |

### Schema do banco de dados (modelo entidade-relacionamento)

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
        bigint lote_id PK, FK
        timestamp gerado_em
        text resultado
    }
```

**Regras do modelo**

- Cada `upload_lote` origina vários `cliente`s e no máximo uma `estatistica_lote`. Ao excluir um lote, seus clientes, contratos e estatísticas são excluídos em cascata.
- `consultor`, `segmento` e `servico` são tabelas de domínio com nome único, o que evita repetição de texto nos clientes.
- `contrato` resolve a relação N:N entre `cliente` e `servico`; a restrição `UNIQUE (cliente_id, servico_id)` impede o mesmo serviço duas vezes para um cliente.
- `UNIQUE (lote_id, codigo_cti)` impede código de cliente repetido dentro do mesmo lote.
- `usuario` não tem relação com os dados da planilha: o serviço Python não conhece o usuário que enviou o lote.

O script de criação fica em `backend/src/main/resources/schema.sql`; `database/schema.sql` é uma cópia para consulta. Consultas de conferência: [`database/consultas-neon.sql`](database/consultas-neon.sql).

---

## 11. Testes

### Teste automatizado

Com o Python e o back-end em execução, na raiz do projeto:

```bash
python testes/testar_tudo.py            # testa as partes que estiverem ligadas
python testes/testar_tudo.py --gravar   # também envia 2 linhas de teste e grava no banco
```

O script verifica a limpeza e os cálculos do Python, as rotas `/saude` e `/testar`, o health check, o login do administrador e as respostas `401` e `400` do upload. O resultado final é `Resumo: N OK, N FALHA, N PULOU`. **Para o deploy, deve haver 0 FALHA.**

### Teste manual com a planilha de exemplo

Após enviar `sample-data/dados.xlsx`, o banco deve conter:

| Verificação | Valor esperado |
|---|---|
| Linhas do lote (total / válidas / inválidas) | 38 / 32 / 6 |
| Clientes | 32 |
| Segmentos / consultores / serviços | 6 / 4 / 6 |
| Contratos | 61 |
| Média de faturamento | 2.261.250 |

---

## 12. Deploy

| Aplicação | Plataforma | Configuração |
|---|---|---|
| Python | Render (Web Service) | Diretório `analytics-python`; build `pip install -r requirements.txt`; start `uvicorn api:app --host 0.0.0.0 --port $PORT` |
| Back-end | Render (Docker) | Diretório `backend`; usa o `Dockerfile` (build com Maven e execução com JRE 21) |
| Front-end | Vercel | Diretório `frontend`; build `npm run build`; saída `dist/` |
| Banco | Neon | Sem alteração |

**Ordem:** Python → back-end → front-end → atualizar `CORS_ORIGINS` do back-end com o endereço da Vercel → testar.

**Variáveis de ambiente.** Os arquivos `.env` não vão para o repositório: os mesmos valores devem ser cadastrados no painel de cada serviço. Em produção, use `ETL_URL=https://<python>.onrender.com/etl`, `CORS_ORIGINS=https://<front>.vercel.app` (sem `/` no final) e `VITE_API_URL=https://<backend>.onrender.com/api`.

**Rotas do Vue na Vercel.** O arquivo `frontend/vercel.json` redireciona qualquer caminho para `index.html`. Sem ele, recarregar a página em `/dashboard` resultaria em erro 404, porque essa rota só existe no Vue Router, não no servidor.

**Endereços publicados.** Front-end: `https://cti-projeto-deploy.vercel.app` · Back-end: `https://cti-api-2plx.onrender.com` · Python: `https://cti-python.onrender.com`.

**Plano gratuito do Render.** Os serviços hibernam após um período sem uso; a primeira requisição depois disso pode levar cerca de um minuto.

Passo a passo completo: [`docs/DEPLOY-RENDER-VERCEL.txt`](docs/DEPLOY-RENDER-VERCEL.txt) (ou [`docs/DEPLOY-AZURE-VERCEL.txt`](docs/DEPLOY-AZURE-VERCEL.txt) para o Azure).

---

## 13. Solução de problemas

| Sintoma | Causa provável | Solução |
|---|---|---|
| Tela: "Não consegui falar com o servidor" | Back-end desligado ou `VITE_API_URL` incorreta | Inicie o back-end e confira o `.env` do front-end (deve terminar em `/api`) |
| Erro de CORS no console do navegador (F12) | `CORS_ORIGINS` não contém o endereço do front-end | Corrija a variável no back-end, sem `/` no final |
| Alterei `VITE_API_URL` e nada mudou | Variáveis `VITE_` são lidas na inicialização e na build | Reinicie o `npm run dev` ou gere a build de novo |
| `npm install` falha | Versão do Node.js antiga | Instale o Node.js 20 LTS ou superior |
| Erro 404 ao recarregar a página na Vercel | `vercel.json` ausente | Confirme que `frontend/vercel.json` existe |
| Login: "aguarda a liberação do administrador" | Conta com status PENDENTE | Aprove o usuário em **Liberar acessos**, como ADMIN |
| Upload: `503` | Python desligado ou `ETL_URL` incorreta | Inicie o Python e confira a URL |
| Upload: `502 ... respondeu 401` | `ETL_KEY` diferente de `ETL_API_KEY` | Use o mesmo valor nos dois `.env` |
| Upload: `502 ... respondeu 500` | Falha do Python ao gravar (senha ou firewall do banco) | Veja o terminal do Python |
| Back-end não inicia: `JWT_SECRET precisa ter pelo menos 32 caracteres` | Segredo curto | Aumente o `JWT_SECRET` |
| Primeira requisição muito lenta | Neon ou Render hibernando | Comportamento esperado do plano gratuito |

---

## 14. Documentação complementar

| Documento | Conteúdo |
|---|---|
| [`backend/README.md`](backend/README.md) | Back-end em detalhe, com código comentado |
| [`frontend/README.md`](frontend/README.md) | Resumo do front-end |
| [`analytics-python/README.md`](analytics-python/README.md) | Resumo do Python |
| [`database/README.md`](database/README.md) | Tabelas e conferência dos dados |
| [`docs/CONTRATO.md`](docs/CONTRATO.md) | Contrato de dados entre as três aplicações |
| [`docs/FRONT-AXIOS.txt`](docs/FRONT-AXIOS.txt) | Guia do front-end com axios |
| [`docs/BACKEND.txt`](docs/BACKEND.txt) | Guia do back-end |
| [`docs/CIENCIA-DE-DADOS.txt`](docs/CIENCIA-DE-DADOS.txt) | Guia do ETL em Python |
| [`docs/TESTES.txt`](docs/TESTES.txt) | Guia de testes |
| [`docs/DEPLOY-RENDER-VERCEL.txt`](docs/DEPLOY-RENDER-VERCEL.txt) | Deploy no Render e na Vercel |
