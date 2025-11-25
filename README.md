# 🏥 Man's Health - Sistema de Gestão de Saúde Masculina

## 📋 Índice
1. [Visão Geral](#visão-geral)
2. [Tecnologias Utilizadas](#tecnologias-utilizadas)
3. [Arquitetura do Sistema](#arquitetura-do-sistema)
4. [Funcionalidades](#funcionalidades)
5. [Estrutura do Projeto](#estrutura-do-projeto)
6. [Banco de Dados](#banco-de-dados)
7. [API Endpoints](#api-endpoints)
8. [Segurança](#segurança)
9. [Deploy](#deploy)
10. [Como Executar Localmente](#como-executar-localmente)
11. [Possíveis Perguntas](#possíveis-perguntas)

---

## 🎯 Visão Geral

**Man's Health** é uma aplicação web full-stack desenvolvida para auxiliar homens no acompanhamento e gestão de sua saúde. O sistema permite:

- 📊 Monitoramento de exames médicos
- 💪 Registro de atividades físicas
- ✅ Criação e acompanhamento de hábitos saudáveis
- 💬 Fórum de discussão sobre saúde
- 🔔 Sistema de notificações
- 👤 Gestão de perfil do usuário

### URLs do Projeto
- **Frontend (Produção)**: https://mans-health-kqlkz9brp-gustavo-s-projects-fcf3e388.vercel.app
- **Backend (API)**: https://mans-health-production.up.railway.app
- **Repositório GitHub**: https://github.com/flashzinkv/-mans-health

---

## 🛠️ Tecnologias Utilizadas

### Frontend
| Tecnologia | Versão | Uso |
|------------|--------|-----|
| **React** | 18.2.0 | Biblioteca principal para UI |
| **React Router DOM** | 6.18.0 | Roteamento SPA |
| **Vite** | 5.0.0 | Build tool e dev server |
| **Axios** | 1.6.2 | Cliente HTTP para APIs |
| **Bootstrap** | 5.3.2 | Framework CSS |
| **React Bootstrap** | 2.9.1 | Componentes Bootstrap para React |
| **Chart.js** | 4.4.0 | Gráficos e visualizações |
| **React Icons** | 4.11.0 | Biblioteca de ícones |
| **Date-fns** | 2.30.0 | Manipulação de datas |

### Backend
| Tecnologia | Versão | Uso |
|------------|--------|-----|
| **Node.js** | 18+ | Runtime JavaScript |
| **Express** | 4.18.2 | Framework web |
| **PostgreSQL** | 14+ | Banco de dados relacional |
| **Sequelize** | 6.35.1 | ORM para PostgreSQL |
| **JWT** | 9.0.2 | Autenticação via tokens |
| **bcryptjs** | 2.4.3 | Hash de senhas |
| **CORS** | 2.8.5 | Cross-Origin Resource Sharing |
| **Helmet** | 7.1.0 | Segurança HTTP headers |
| **Morgan** | 1.10.0 | Logger de requisições |
| **Node-cron** | 3.0.3 | Agendamento de tarefas |

### Infraestrutura
- **Vercel**: Hospedagem do frontend
- **Railway**: Hospedagem do backend + PostgreSQL
- **GitHub**: Versionamento de código
- **Git**: Controle de versão

---

## 🏗️ Arquitetura do Sistema

### Padrão: MVC (Model-View-Controller) + REST API

```
┌─────────────────┐
│                 │
│   FRONTEND      │  React (View)
│   (Vercel)      │  - Components
│                 │  - Pages
│                 │  - Contexts (State)
└────────┬────────┘
         │ HTTPS/REST
         │ JSON
         ▼
┌─────────────────┐
│                 │
│   BACKEND       │  Express (Controller)
│   (Railway)     │  - Routes
│                 │  - Controllers
│                 │  - Middlewares
└────────┬────────┘
         │ Sequelize ORM
         │ SQL
         ▼
┌─────────────────┐
│                 │
│   DATABASE      │  PostgreSQL (Model)
│   (Railway)     │  - Users
│                 │  - Exames
│                 │  - Exercicios
│                 │  - Habitos
│                 │  - Forum
│                 │  - Notifications
└─────────────────┘
```

### Fluxo de Comunicação

1. **Usuário** → Interage com a interface React
2. **Frontend** → Faz requisição HTTP para a API (Axios)
3. **Backend** → Recebe, valida e processa (Express + Middlewares)
4. **Banco de Dados** → Armazena/recupera dados (PostgreSQL + Sequelize)
5. **Backend** → Retorna resposta JSON
6. **Frontend** → Atualiza interface com os dados

---

## ✨ Funcionalidades

### 1. 🔐 Autenticação e Autorização
- **Registro de usuários** com validação de dados
- **Login** com JWT (JSON Web Token)
- **Proteção de rotas** (middleware de autenticação)
- **Logout** seguro
- **Tokens com expiração** configurável (7 dias)

**Tecnologias**: JWT, bcryptjs, Context API

### 2. 📊 Dashboard
- **Visão geral** da saúde do usuário
- **Próximos exames** agendados
- **Progresso de hábitos** diários
- **Estatísticas de exercícios** (gráficos)
- **Últimas atividades** no fórum

**Tecnologias**: Chart.js, React Hooks

### 3. 🔬 Gestão de Exames
- **Adicionar exames** (nome, tipo, data, observações)
- **Listar histórico** de exames
- **Editar** informações
- **Excluir** registros
- **Alertas** para exames próximos
- **Upload de resultados** (opcional)

**Banco**: Tabela `Exames` (userId, tipo, data, resultado)

### 4. 💪 Registro de Exercícios
- **Adicionar treinos** (tipo, duração, calorias, intensidade)
- **Acompanhamento** de progresso
- **Gráficos** de desempenho
- **Histórico** completo de atividades

**Banco**: Tabela `Exercicios` (userId, tipo, duracao, calorias)

### 5. ✅ Hábitos Saudáveis
- **Criar hábitos** personalizados
- **Marcar como completo** diariamente
- **Streaks** (sequências de dias)
- **Progresso visual** (percentual)
- **Histórico** de conclusões

**Banco**: Tabela `Habitos` (userId, nome, frequencia, progresso)

### 6. 💬 Fórum Comunitário
- **Criar posts** sobre saúde
- **Comentar** em discussões
- **Curtir** posts
- **Categorias** de tópicos
- **Moderação** básica

**Banco**: Tabela `ForumPosts` (userId, titulo, conteudo, likes, replies)

### 7. 🔔 Sistema de Notificações
- **Notificações** de exames próximos
- **Lembretes** de hábitos
- **Contador** de não lidas
- **Marcar como lida**
- **Cron jobs** para verificação automática

**Banco**: Tabela `Notifications` (userId, tipo, mensagem, lida)

### 8. 👤 Perfil do Usuário
- **Visualizar** dados pessoais
- **Editar** informações
- **Alterar senha**
- **Estatísticas** gerais
- **Excluir conta**

**Banco**: Tabela `Users` (nome, email, senha, data_nascimento, altura, peso)

---

## 📁 Estrutura do Projeto

```
mans-health/
│
├── frontend/                    # Aplicação React
│   ├── public/                  # Assets estáticos
│   │   └── logo.png            # Logo do projeto
│   │
│   ├── src/
│   │   ├── components/         # Componentes reutilizáveis
│   │   │   ├── Navbar.jsx      # Barra de navegação
│   │   │   └── PrivateRoute.jsx # Proteção de rotas
│   │   │
│   │   ├── contexts/           # Context API (State Global)
│   │   │   ├── AuthContext.jsx # Contexto de autenticação
│   │   │   └── NotificationContext.jsx # Contexto de notificações
│   │   │
│   │   ├── pages/              # Páginas da aplicação
│   │   │   ├── Login.jsx       # Tela de login
│   │   │   ├── Register.jsx    # Tela de registro
│   │   │   ├── Dashboard.jsx   # Dashboard principal
│   │   │   ├── Exames.jsx      # Gestão de exames
│   │   │   ├── Exercicios.jsx  # Registro de exercícios
│   │   │   ├── BemEstar.jsx    # Página de bem-estar
│   │   │   ├── Forum.jsx       # Fórum comunitário
│   │   │   └── Profile.jsx     # Perfil do usuário
│   │   │
│   │   ├── services/           # Serviços de API
│   │   │   └── api.js          # Configuração Axios + Interceptors
│   │   │
│   │   ├── App.jsx             # Componente raiz
│   │   ├── main.jsx            # Entry point
│   │   └── index.css           # Estilos globais
│   │
│   ├── package.json            # Dependências do frontend
│   ├── vite.config.js          # Configuração do Vite
│   └── vercel.json             # Configuração do Vercel
│
├── backend/                     # API REST
│   ├── src/
│   │   ├── config/             # Configurações
│   │   │   └── database.js     # Conexão Sequelize
│   │   │
│   │   ├── controllers/        # Lógica de negócio
│   │   │   ├── authController.js
│   │   │   ├── userController.js
│   │   │   ├── exameController.js
│   │   │   ├── exercicioController.js
│   │   │   ├── habitoController.js
│   │   │   ├── forumController.js
│   │   │   └── notificationController.js
│   │   │
│   │   ├── middlewares/        # Middlewares
│   │   │   ├── auth.js         # Verificação JWT
│   │   │   ├── validator.js    # Validação de dados
│   │   │   └── errorHandler.js # Tratamento de erros
│   │   │
│   │   ├── models/             # Modelos Sequelize (ORM)
│   │   │   ├── User.js
│   │   │   ├── Exame.js
│   │   │   ├── Exercicio.js
│   │   │   ├── Habito.js
│   │   │   ├── ForumPost.js
│   │   │   ├── Notification.js
│   │   │   └── index.js        # Associações
│   │   │
│   │   ├── routes/             # Rotas da API
│   │   │   ├── auth.js
│   │   │   ├── users.js
│   │   │   ├── exames.js
│   │   │   ├── exercicios.js
│   │   │   ├── habitos.js
│   │   │   ├── forum.js
│   │   │   ├── notifications.js
│   │   │   └── index.js
│   │   │
│   │   ├── services/           # Serviços auxiliares
│   │   │   └── notificationService.js
│   │   │
│   │   └── server.js           # Entry point do servidor
│   │
│   ├── package.json            # Dependências do backend
│   └── env.example             # Exemplo de variáveis de ambiente
│
├── .gitignore                  # Arquivos ignorados pelo Git
└── README.md                   # Este arquivo
```

---

## 🗄️ Banco de Dados

### Schema PostgreSQL

#### Tabela: `Users`
```sql
CREATE TABLE Users (
    id SERIAL PRIMARY KEY,
    nome VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    senha VARCHAR(255) NOT NULL,  -- Hash bcrypt
    dataNascimento DATE,
    altura DECIMAL(5,2),
    peso DECIMAL(5,2),
    createdAt TIMESTAMP DEFAULT NOW(),
    updatedAt TIMESTAMP DEFAULT NOW()
);
```

#### Tabela: `Exames`
```sql
CREATE TABLE Exames (
    id SERIAL PRIMARY KEY,
    userId INTEGER REFERENCES Users(id) ON DELETE CASCADE,
    tipo VARCHAR(255) NOT NULL,
    data DATE NOT NULL,
    resultado TEXT,
    observacoes TEXT,
    proximo_exame DATE,
    createdAt TIMESTAMP DEFAULT NOW(),
    updatedAt TIMESTAMP DEFAULT NOW()
);
```

#### Tabela: `Exercicios`
```sql
CREATE TABLE Exercicios (
    id SERIAL PRIMARY KEY,
    userId INTEGER REFERENCES Users(id) ON DELETE CASCADE,
    tipo VARCHAR(255) NOT NULL,
    duracao INTEGER,  -- minutos
    calorias INTEGER,
    intensidade VARCHAR(50),
    data TIMESTAMP DEFAULT NOW(),
    observacoes TEXT,
    createdAt TIMESTAMP DEFAULT NOW(),
    updatedAt TIMESTAMP DEFAULT NOW()
);
```

#### Tabela: `Habitos`
```sql
CREATE TABLE Habitos (
    id SERIAL PRIMARY KEY,
    userId INTEGER REFERENCES Users(id) ON DELETE CASCADE,
    nome VARCHAR(255) NOT NULL,
    descricao TEXT,
    frequencia VARCHAR(50),  -- diario, semanal, mensal
    progresso INTEGER DEFAULT 0,
    meta INTEGER DEFAULT 100,
    concluido BOOLEAN DEFAULT FALSE,
    ultima_conclusao TIMESTAMP,
    createdAt TIMESTAMP DEFAULT NOW(),
    updatedAt TIMESTAMP DEFAULT NOW()
);
```

#### Tabela: `ForumPosts`
```sql
CREATE TABLE ForumPosts (
    id SERIAL PRIMARY KEY,
    userId INTEGER REFERENCES Users(id) ON DELETE CASCADE,
    titulo VARCHAR(255) NOT NULL,
    conteudo TEXT NOT NULL,
    categoria VARCHAR(100),
    likes INTEGER DEFAULT 0,
    replies JSON,  -- Array de respostas
    createdAt TIMESTAMP DEFAULT NOW(),
    updatedAt TIMESTAMP DEFAULT NOW()
);
```

#### Tabela: `Notifications`
```sql
CREATE TABLE Notifications (
    id SERIAL PRIMARY KEY,
    userId INTEGER REFERENCES Users(id) ON DELETE CASCADE,
    tipo VARCHAR(100) NOT NULL,
    mensagem TEXT NOT NULL,
    lida BOOLEAN DEFAULT FALSE,
    createdAt TIMESTAMP DEFAULT NOW(),
    updatedAt TIMESTAMP DEFAULT NOW()
);
```

### Relacionamentos

- **Users** → **Exames** (1:N)
- **Users** → **Exercicios** (1:N)
- **Users** → **Habitos** (1:N)
- **Users** → **ForumPosts** (1:N)
- **Users** → **Notifications** (1:N)

**ORM**: Sequelize gerencia automaticamente as relações com `belongsTo` e `hasMany`

---

## 🌐 API Endpoints

### Base URL: `https://mans-health-production.up.railway.app/api`

### 🔐 Autenticação

| Método | Endpoint | Descrição | Body |
|--------|----------|-----------|------|
| POST | `/auth/register` | Criar nova conta | `{ nome, email, senha }` |
| POST | `/auth/login` | Fazer login | `{ email, senha }` |
| POST | `/auth/logout` | Fazer logout | - |
| GET | `/auth/me` | Obter usuário atual | - |

**Resposta Login**:
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": 1,
    "nome": "João Silva",
    "email": "joao@example.com"
  }
}
```

### 👤 Usuários

| Método | Endpoint | Descrição | Auth |
|--------|----------|-----------|------|
| GET | `/users/profile` | Ver perfil | ✅ |
| PUT | `/users/profile` | Atualizar perfil | ✅ |
| DELETE | `/users/account` | Excluir conta | ✅ |

### 🔬 Exames

| Método | Endpoint | Descrição | Auth |
|--------|----------|-----------|------|
| GET | `/exames` | Listar exames | ✅ |
| GET | `/exames/:id` | Ver exame específico | ✅ |
| GET | `/exames/upcoming` | Próximos exames | ✅ |
| POST | `/exames` | Criar exame | ✅ |
| PUT | `/exames/:id` | Atualizar exame | ✅ |
| DELETE | `/exames/:id` | Excluir exame | ✅ |

### 💪 Exercícios

| Método | Endpoint | Descrição | Auth |
|--------|----------|-----------|------|
| GET | `/exercicios` | Listar exercícios | ✅ |
| GET | `/exercicios/:id` | Ver exercício | ✅ |
| POST | `/exercicios` | Criar exercício | ✅ |
| PUT | `/exercicios/:id` | Atualizar exercício | ✅ |
| DELETE | `/exercicios/:id` | Excluir exercício | ✅ |

### ✅ Hábitos

| Método | Endpoint | Descrição | Auth |
|--------|----------|-----------|------|
| GET | `/habitos` | Listar hábitos | ✅ |
| GET | `/habitos/:id` | Ver hábito | ✅ |
| POST | `/habitos` | Criar hábito | ✅ |
| PUT | `/habitos/:id` | Atualizar hábito | ✅ |
| POST | `/habitos/:id/complete` | Marcar completo | ✅ |
| DELETE | `/habitos/:id` | Excluir hábito | ✅ |

### 💬 Fórum

| Método | Endpoint | Descrição | Auth |
|--------|----------|-----------|------|
| GET | `/forum` | Listar posts | ✅ |
| GET | `/forum/:id` | Ver post | ✅ |
| POST | `/forum` | Criar post | ✅ |
| POST | `/forum/:id/reply` | Responder post | ✅ |
| POST | `/forum/:id/like` | Curtir/Descurtir | ✅ |
| DELETE | `/forum/:id` | Excluir post | ✅ |

### 🔔 Notificações

| Método | Endpoint | Descrição | Auth |
|--------|----------|-----------|------|
| GET | `/notifications` | Listar notificações | ✅ |
| GET | `/notifications/unread-count` | Contador não lidas | ✅ |
| POST | `/notifications` | Criar notificação | ✅ |
| PUT | `/notifications/:id/read` | Marcar como lida | ✅ |
| PUT | `/notifications/mark-all-read` | Marcar todas lidas | ✅ |
| DELETE | `/notifications/:id` | Excluir notificação | ✅ |

---

## 🔒 Segurança

### Implementações de Segurança

1. **Autenticação JWT**
   - Tokens assinados com chave secreta
   - Expiração configurável (7 dias)
   - Armazenamento no localStorage (frontend)

2. **Hashing de Senhas**
   - bcryptjs com 10 rounds de salt
   - Senhas nunca armazenadas em texto plano

3. **CORS (Cross-Origin Resource Sharing)**
   - Origem específica configurada
   - Credentials habilitado
   - Proteção contra requisições não autorizadas

4. **Helmet.js**
   - Content Security Policy
   - X-Frame-Options
   - X-Content-Type-Options
   - Outros headers de segurança

5. **Validação de Dados**
   - Express-validator para input validation
   - Sanitização de dados
   - Proteção contra SQL Injection (ORM)

6. **Middleware de Autenticação**
   ```javascript
   const auth = async (req, res, next) => {
     const token = req.headers.authorization?.split(' ')[1];
     if (!token) return res.status(401).json({ error: 'Não autorizado' });
     
     try {
       const decoded = jwt.verify(token, process.env.JWT_SECRET);
       req.userId = decoded.id;
       next();
     } catch (error) {
       res.status(401).json({ error: 'Token inválido' });
     }
   };
   ```

7. **Variáveis de Ambiente**
   - Senhas e chaves em variáveis de ambiente
   - Não commitadas no Git (.gitignore)

---

## 🚀 Deploy

### Frontend (Vercel)

**URL**: https://mans-health-kqlkz9brp-gustavo-s-projects-fcf3e388.vercel.app

**Configuração**:
- Framework: Vite
- Build Command: `npm run build`
- Output Directory: `dist`
- Node Version: 18.x

**Variável de Ambiente**:
```
VITE_API_URL=https://mans-health-production.up.railway.app/api
```

**Deploy Automático**: Push para branch `main` no GitHub

### Backend (Railway)

**URL**: https://mans-health-production.up.railway.app

**Configuração**:
- Root Directory: `backend`
- Start Command: `npm start`
- Node Version: 18.x

**Variáveis de Ambiente**:
```
NODE_ENV=production
PORT=5000
DATABASE_URL=[gerado automaticamente]
JWT_SECRET=[chave secreta 64 caracteres]
JWT_EXPIRES_IN=7d
CORS_ORIGIN=[URL do frontend na Vercel]
```

**Deploy Automático**: Push para branch `main` no GitHub

### Banco de Dados (Railway - PostgreSQL)

**Versão**: PostgreSQL 14
**Tamanho**: 512 MB (plano gratuito)
**Conexão**: DATABASE_URL fornecida automaticamente
**Backup**: Automático pelo Railway

---

## 💻 Como Executar Localmente

### Pré-requisitos
- Node.js 18+ instalado
- PostgreSQL 14+ instalado
- Git instalado

### 1. Clonar o Repositório
```bash
git clone https://github.com/flashzinkv/-mans-health.git
cd mans-health
```

### 2. Configurar Backend

```bash
cd backend
npm install
```

Criar arquivo `.env`:
```env
NODE_ENV=development
PORT=5000
DB_HOST=localhost
DB_PORT=5432
DB_NAME=manshealth
DB_USER=postgres
DB_PASSWORD=sua_senha
JWT_SECRET=sua_chave_secreta_aqui
JWT_EXPIRES_IN=7d
CORS_ORIGIN=http://localhost:5173
```

Criar banco de dados:
```sql
CREATE DATABASE manshealth;
```

Iniciar backend:
```bash
npm run dev
```

### 3. Configurar Frontend

```bash
cd ..
npm install
```

Criar arquivo `.env`:
```env
VITE_API_URL=http://localhost:5000/api
```

Iniciar frontend:
```bash
npm run dev
```

### 4. Acessar
- Frontend: http://localhost:5173
- Backend: http://localhost:5000

---

## ❓ Possíveis Perguntas dos Professores

### 1. **Por que escolheu React em vez de outras bibliotecas/frameworks?**

**Resposta**: Escolhi React porque:
- É a biblioteca mais popular e com maior comunidade
- Component-based architecture facilita reutilização de código
- Virtual DOM oferece ótima performance
- Ecossistema rico (React Router, Context API, etc)
- Experiência de aprendizado valiosa para o mercado

### 2. **Como funciona a autenticação JWT?**

**Resposta**: 
1. Usuário faz login com email/senha
2. Backend valida credenciais
3. Backend gera token JWT assinado com chave secreta
4. Token é enviado ao frontend e armazenado no localStorage
5. Em cada requisição, frontend envia token no header Authorization
6. Backend valida token antes de processar requisição
7. Se token inválido/expirado, retorna 401 Unauthorized

**Vantagens**: Stateless (não precisa armazenar sessões no servidor), escalável, seguro

### 3. **Por que usar ORM (Sequelize) em vez de SQL puro?**

**Resposta**:
- **Abstração**: Escrever JavaScript em vez de SQL
- **Segurança**: Proteção automática contra SQL Injection
- **Migrações**: Controle de versão do banco de dados
- **Validações**: Validação de dados antes de salvar
- **Relacionamentos**: Gerenciamento automático de foreign keys
- **Portabilidade**: Trocar de banco (MySQL, PostgreSQL) sem mudar código

### 4. **Como funciona o CORS e por que é necessário?**

**Resposta**:
CORS (Cross-Origin Resource Sharing) é necessário porque:
- Frontend (vercel.app) e Backend (railway.app) estão em domínios diferentes
- Navegadores bloqueiam requisições cross-origin por segurança (Same-Origin Policy)
- Configuramos CORS no backend para permitir apenas nosso frontend
- Isso previne que sites maliciosos façam requisições não autorizadas

### 5. **Como garantir a segurança das senhas?**

**Resposta**:
- Uso de **bcryptjs** para hash de senhas
- **10 rounds de salt** (custo computacional balanceado)
- Senhas **nunca armazenadas em texto plano**
- Hash é **one-way** (não pode ser revertido)
- Cada senha tem salt único (mesmo senhas iguais geram hashes diferentes)

### 6. **Explique a arquitetura MVC do projeto**

**Resposta**:
- **Model** (Sequelize): Estrutura dos dados e relacionamentos
- **View** (React): Interface do usuário e apresentação
- **Controller** (Express): Lógica de negócio e processamento
- **Separação de responsabilidades**: Cada camada tem função específica
- **Facilita manutenção**: Mudanças em uma camada não afetam outras

### 7. **Como funciona o sistema de notificações?**

**Resposta**:
- **Cron jobs** rodam diariamente às 8h da manhã
- Verificam exames com data próxima (7 dias)
- Criam notificações automáticas no banco
- Frontend consulta API para buscar notificações
- **Context API** gerencia estado global de notificações
- Contador de não lidas atualizado em tempo real

### 8. **Por que usar Vite em vez de Create React App?**

**Resposta**:
- **Muito mais rápido**: Build até 100x mais rápido
- **HMR (Hot Module Replacement)**: Atualizações instantâneas
- **Menor bundle size**: Otimização automática
- **ESM nativo**: Usa módulos ES nativos do navegador
- **Configuração simples**: Menos complexo que Webpack
- **Futuro do React**: CRA está deprecated

### 9. **Como funciona o deploy automático?**

**Resposta**:
1. Código é commitado no Git
2. Push para GitHub (branch main)
3. **Vercel** detecta mudanças automaticamente
4. Faz build do frontend (npm run build)
5. Deploy para CDN global
6. **Railway** também detecta mudanças
7. Faz build do backend e restart do servidor
8. Deploy com zero downtime

### 10. **Quais melhorias futuras você faria?**

**Resposta**:
- **Testes automatizados** (Jest, React Testing Library)
- **CI/CD** mais robusto (GitHub Actions)
- **WebSockets** para notificações em tempo real
- **PWA** (Progressive Web App) para uso offline
- **Upload de arquivos** (resultados de exames)
- **Integração com APIs** de saúde (Google Fit, Apple Health)
- **Analytics** (Google Analytics, Mixpanel)
- **Monitoramento** (Sentry para erros)
- **Cache** (Redis para performance)
- **Kubernetes** para escalabilidade

### 11. **Como lidar com erros na aplicação?**

**Resposta**:
**Frontend**:
- Try-catch em requisições async
- Error boundaries para erros de renderização
- Mensagens amigáveis ao usuário
- Fallback UI quando algo falha

**Backend**:
- Middleware de tratamento de erros centralizado
- Logs estruturados (Morgan)
- Status codes HTTP apropriados
- Validação de entrada de dados
- Tratamento de erros do banco

### 12. **Como funciona o Context API?**

**Resposta**:
- **Global state management** sem Redux
- AuthContext gerencia estado de autenticação
- Compartilha currentUser e funções (login, logout)
- Evita prop drilling (passar props por múltiplos níveis)
- useContext hook para acessar valores
- Provider envolve toda a aplicação

---

## 📊 Métricas do Projeto

- **Linhas de Código**: ~10.000+
- **Componentes React**: 15+
- **Endpoints API**: 35+
- **Tabelas no Banco**: 6
- **Dependências**: 30+
- **Tempo de Desenvolvimento**: ~40 horas
- **Commits Git**: 5+

---

## 📚 Referências e Documentação

- **React**: https://react.dev
- **Express**: https://expressjs.com
- **Sequelize**: https://sequelize.org
- **JWT**: https://jwt.io
- **Vercel**: https://vercel.com/docs
- **Railway**: https://docs.railway.app
- **PostgreSQL**: https://www.postgresql.org/docs
- **Vite**: https://vitejs.dev

---

## 👨‍💻 Desenvolvido por

**Gustavo Fontes Dias**

---

## 📄 Licença

MIT License - Projeto educacional

---

## 🎓 Conclusão

Este projeto demonstra conhecimentos em:
- ✅ Desenvolvimento Full-Stack
- ✅ Arquitetura REST API
- ✅ Autenticação e Segurança
- ✅ Banco de Dados Relacional
- ✅ Deploy e DevOps
- ✅ Versionamento com Git
- ✅ Boas práticas de código
- ✅ UI/UX Design

**Ideal para apresentação acadêmica e portfólio profissional!** 🚀

