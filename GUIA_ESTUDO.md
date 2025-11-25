# 📖 Guia de Estudo - Man's Health

## Para Apresentação e Defesa do Projeto

Este documento contém explicações técnicas detalhadas para você dominar todos os conceitos do projeto e responder qualquer pergunta dos professores.

---

## 📚 Índice

1. [Conceitos Fundamentais](#conceitos-fundamentais)
2. [Fluxos Detalhados](#fluxos-detalhados)
3. [Código-Chave Explicado](#código-chave-explicado)
4. [Decisões de Arquitetura](#decisões-de-arquitetura)
5. [Troubleshooting Comum](#troubleshooting-comum)
6. [Perguntas Avançadas](#perguntas-avançadas)

---

## 🎯 Conceitos Fundamentais

### 1. O que é uma SPA (Single Page Application)?

**Resposta Simples**: Aplicação web que carrega uma única página HTML e atualiza dinamicamente o conteúdo sem recarregar a página.

**No Nosso Projeto**:
- React carrega `index.html` uma vez
- React Router muda componentes sem recarregar página
- Mais rápido e melhor UX que sites tradicionais
- AJAX/Fetch para comunicar com backend

**Vantagens**:
- ⚡ Performance superior
- 📱 Sensação de app nativo
- 🔄 Menos carga no servidor
- 💾 Cache eficiente

**Desvantagens**:
- 🔍 SEO pode ser desafiador (mas resolvemos com SSR se necessário)
- 📦 Bundle inicial maior
- 🧩 Mais complexo que MPA tradicional

---

### 2. O que é REST API?

**REST** = Representational State Transfer

**Princípios**:
1. **Stateless**: Cada requisição é independente
2. **Client-Server**: Separação clara de responsabilidades
3. **Uniform Interface**: Endpoints padronizados
4. **Cacheable**: Respostas podem ser cacheadas

**No Nosso Projeto**:
```
GET    /api/exames        → Listar recursos
POST   /api/exames        → Criar recurso
GET    /api/exames/:id    → Ver um recurso
PUT    /api/exames/:id    → Atualizar recurso
DELETE /api/exames/:id    → Excluir recurso
```

**HTTP Status Codes Usados**:
- `200 OK`: Sucesso
- `201 Created`: Recurso criado
- `400 Bad Request`: Dados inválidos
- `401 Unauthorized`: Não autenticado
- `404 Not Found`: Recurso não existe
- `500 Internal Server Error`: Erro no servidor

---

### 3. O que é JWT (JSON Web Token)?

**Estrutura**: `header.payload.signature`

**Exemplo**:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJpZCI6MSwibm9tZSI6IkpvYW8iLCJpYXQiOjE2MzAwMDAwMDB9.
abc123xyz456signature
```

**Header** (Base64):
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

**Payload** (Base64):
```json
{
  "id": 1,
  "nome": "João",
  "iat": 1630000000,
  "exp": 1630604800
}
```

**Signature**: 
```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  SECRET_KEY
)
```

**Por que é seguro?**
- Assinatura garante integridade
- Se alguém modificar payload, assinatura fica inválida
- Apenas servidor conhece SECRET_KEY
- Tokens têm expiração (7 dias no nosso caso)

---

### 4. O que é ORM (Object-Relational Mapping)?

**Definição**: Técnica que mapeia objetos de código para tabelas de banco de dados.

**Sem ORM (SQL Puro)**:
```javascript
const result = await db.query(
  'SELECT * FROM users WHERE email = $1',
  [email]
);
const user = result.rows[0];
```

**Com ORM (Sequelize)**:
```javascript
const user = await User.findOne({
  where: { email: email }
});
```

**Vantagens**:
- ✅ Código mais limpo e legível
- ✅ Proteção contra SQL Injection automática
- ✅ Validações built-in
- ✅ Migrações de schema
- ✅ Relacionamentos facilitados
- ✅ Portabilidade entre bancos

**No Nosso Projeto**:
```javascript
// Modelo
const User = sequelize.define('User', {
  nome: DataTypes.STRING,
  email: {
    type: DataTypes.STRING,
    unique: true,
    validate: { isEmail: true }
  }
});

// Relacionamento
User.hasMany(Exame);
Exame.belongsTo(User);

// Uso
const exames = await user.getExames();
```

---

### 5. Como funciona o bcrypt?

**Hash de Senha**:
```javascript
const senha = "minhaSenha123";
const hash = await bcrypt.hash(senha, 10);
// Resultado: $2b$10$abc...xyz (60 caracteres)
```

**Estrutura do Hash**:
```
$2b$10$saltsaltsaltsaltsalthashhashhash
│  │  │                      │
│  │  └─ Salt               └─ Hash final
│  └─ Cost (2^10 = 1024 iterações)
└─ Algoritmo (bcrypt)
```

**Verificação**:
```javascript
const senhaCorreta = await bcrypt.compare("minhaSenha123", hash);
// true ou false
```

**Por que 10 rounds?**
- Balanço entre segurança e performance
- 2^10 = 1024 iterações
- ~100ms para processar (aceitável)
- Dificulta brute-force attacks

---

## 🔄 Fluxos Detalhados

### Fluxo 1: Registro de Usuário

```
FRONTEND                    BACKEND                     DATABASE
   │                           │                           │
   │ 1. Preenche form          │                           │
   │    (nome, email, senha)   │                           │
   │                           │                           │
   │ 2. POST /api/auth/register│                           │
   ├──────────────────────────>│                           │
   │                           │                           │
   │                           │ 3. Valida dados           │
   │                           │    (email válido?)        │
   │                           │                           │
   │                           │ 4. Hash senha             │
   │                           │    (bcrypt 10 rounds)     │
   │                           │                           │
   │                           │ 5. INSERT INTO Users      │
   │                           ├──────────────────────────>│
   │                           │                           │
   │                           │ 6. Retorna User           │
   │                           │<──────────────────────────┤
   │                           │                           │
   │                           │ 7. Gera JWT token         │
   │                           │    (jwt.sign())           │
   │                           │                           │
   │ 8. { token, user }        │                           │
   │<──────────────────────────┤                           │
   │                           │                           │
   │ 9. Salva token            │                           │
   │    localStorage.setItem() │                           │
   │                           │                           │
   │10. Redireciona /dashboard │                           │
   └──────────────────────────>│                           │
```

### Fluxo 2: Login de Usuário

```
FRONTEND                    BACKEND                     DATABASE
   │                           │                           │
   │ 1. POST /api/auth/login   │                           │
   │    { email, senha }       │                           │
   ├──────────────────────────>│                           │
   │                           │                           │
   │                           │ 2. SELECT * FROM Users    │
   │                           │    WHERE email = ?        │
   │                           ├──────────────────────────>│
   │                           │                           │
   │                           │ 3. Retorna user           │
   │                           │<──────────────────────────┤
   │                           │                           │
   │                           │ 4. bcrypt.compare()       │
   │                           │    (senha, hash)          │
   │                           │                           │
   │                           │ 5. Se correto:            │
   │                           │    - Gera JWT             │
   │                           │    - Expira em 7 dias     │
   │                           │                           │
   │ 6. { token, user }        │                           │
   │<──────────────────────────┤                           │
   │                           │                           │
   │ 7. Armazena token         │                           │
   │    Context API            │                           │
```

### Fluxo 3: Requisição Autenticada

```
FRONTEND                    BACKEND                     DATABASE
   │                           │                           │
   │ 1. GET /api/exames        │                           │
   │    Headers:               │                           │
   │    Authorization:         │                           │
   │    Bearer eyJhbG...       │                           │
   ├──────────────────────────>│                           │
   │                           │                           │
   │                           │ 2. Middleware auth        │
   │                           │    - Extrai token         │
   │                           │    - jwt.verify()         │
   │                           │    - Valida assinatura    │
   │                           │    - Verifica expiração   │
   │                           │                           │
   │                           │ 3. Token válido:          │
   │                           │    req.userId = decoded.id│
   │                           │    next()                 │
   │                           │                           │
   │                           │ 4. SELECT * FROM Exames   │
   │                           │    WHERE userId = ?       │
   │                           ├──────────────────────────>│
   │                           │                           │
   │                           │ 5. Retorna exames         │
   │                           │<──────────────────────────┤
   │                           │                           │
   │ 6. JSON response          │                           │
   │<──────────────────────────┤                           │
```

---

## 💻 Código-Chave Explicado

### 1. Middleware de Autenticação

```javascript
// backend/src/middlewares/auth.js
const jwt = require('jsonwebtoken');

const auth = async (req, res, next) => {
  try {
    // 1. Extrai token do header Authorization
    const token = req.headers.authorization?.split(' ')[1];
    
    // 2. Verifica se token existe
    if (!token) {
      return res.status(401).json({ 
        error: 'Token não fornecido' 
      });
    }
    
    // 3. Verifica e decodifica token
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    
    // 4. Adiciona userId na requisição
    req.userId = decoded.id;
    
    // 5. Permite prosseguir
    next();
    
  } catch (error) {
    // Token inválido ou expirado
    res.status(401).json({ error: 'Token inválido' });
  }
};

module.exports = auth;
```

**Como usar**:
```javascript
// Rota protegida
router.get('/exames', auth, exameController.getAll);
//                     ↑
//                   Middleware
```

---

### 2. Interceptor do Axios

```javascript
// frontend/src/services/api.js
import axios from 'axios';

const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL
});

// INTERCEPTOR DE REQUISIÇÃO
// Adiciona token automaticamente
api.interceptors.request.use(
  (config) => {
    // Pega token do localStorage
    const token = localStorage.getItem('token');
    
    // Se existe, adiciona no header
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    
    return config;
  },
  (error) => {
    return Promise.reject(error);
  }
);

// INTERCEPTOR DE RESPOSTA
// Trata erros globalmente
api.interceptors.response.use(
  (response) => response,
  (error) => {
    // Se token inválido/expirado
    if (error.response?.status === 401) {
      // Limpa dados e redireciona
      localStorage.removeItem('token');
      localStorage.removeItem('user');
      window.location.href = '/login';
    }
    
    return Promise.reject(error);
  }
);

export default api;
```

**Benefício**: Não precisa adicionar token manualmente em cada requisição!

---

### 3. Context API para Autenticação

```javascript
// frontend/src/contexts/AuthContext.jsx
import { createContext, useState, useContext, useEffect } from 'react';
import { authAPI } from '../services/api';

const AuthContext = createContext();

export function AuthProvider({ children }) {
  const [currentUser, setCurrentUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  // Verifica se há usuário logado ao carregar
  useEffect(() => {
    const token = localStorage.getItem('token');
    const user = localStorage.getItem('user');
    
    if (token && user) {
      setCurrentUser(JSON.parse(user));
    }
    
    setLoading(false);
  }, []);
  
  // Função de login
  const login = async (email, senha) => {
    const response = await authAPI.login({ email, senha });
    
    // Salva token e user
    localStorage.setItem('token', response.data.token);
    localStorage.setItem('user', JSON.stringify(response.data.user));
    
    // Atualiza estado
    setCurrentUser(response.data.user);
    
    return response.data;
  };
  
  // Função de logout
  const logout = async () => {
    await authAPI.logout();
    
    // Limpa dados
    localStorage.removeItem('token');
    localStorage.removeItem('user');
    
    setCurrentUser(null);
  };
  
  const value = {
    currentUser,
    login,
    logout,
    loading
  };
  
  return (
    <AuthContext.Provider value={value}>
      {!loading && children}
    </AuthContext.Provider>
  );
}

// Hook customizado
export function useAuth() {
  return useContext(AuthContext);
}
```

**Como usar em qualquer componente**:
```javascript
const { currentUser, logout } = useAuth();

console.log(currentUser.nome); // "João Silva"
```

---

### 4. Modelo Sequelize com Relacionamentos

```javascript
// backend/src/models/User.js
const { DataTypes } = require('sequelize');

module.exports = (sequelize) => {
  const User = sequelize.define('User', {
    id: {
      type: DataTypes.INTEGER,
      primaryKey: true,
      autoIncrement: true
    },
    nome: {
      type: DataTypes.STRING,
      allowNull: false,
      validate: {
        notEmpty: true,
        len: [3, 100]
      }
    },
    email: {
      type: DataTypes.STRING,
      allowNull: false,
      unique: true,
      validate: {
        isEmail: true
      }
    },
    senha: {
      type: DataTypes.STRING,
      allowNull: false
    },
    dataNascimento: {
      type: DataTypes.DATEONLY
    },
    altura: {
      type: DataTypes.DECIMAL(5, 2),
      validate: {
        min: 50,
        max: 300
      }
    },
    peso: {
      type: DataTypes.DECIMAL(5, 2),
      validate: {
        min: 20,
        max: 500
      }
    }
  }, {
    timestamps: true,
    tableName: 'users'
  });
  
  // Método de instância
  User.prototype.toJSON = function() {
    const values = { ...this.get() };
    delete values.senha; // Não expor senha
    return values;
  };
  
  return User;
};
```

**Relacionamentos**:
```javascript
// backend/src/models/index.js
const User = require('./User')(sequelize);
const Exame = require('./Exame')(sequelize);

// Um usuário tem muitos exames
User.hasMany(Exame, {
  foreignKey: 'userId',
  onDelete: 'CASCADE'
});

// Um exame pertence a um usuário
Exame.belongsTo(User, {
  foreignKey: 'userId'
});

// Usar relacionamento
const user = await User.findByPk(1, {
  include: [Exame] // Eager loading
});
console.log(user.Exames); // Array de exames
```

---

## 🏗️ Decisões de Arquitetura

### 1. Por que separar frontend e backend?

**Arquitetura Monolítica** (tudo junto):
```
server.js
├── routes/
├── views/        ← HTML gerado no servidor
└── public/       ← Assets estáticos
```

**Arquitetura Desacoplada** (nosso projeto):
```
frontend/         ← React (Vercel)
backend/          ← API REST (Railway)
```

**Vantagens da Separação**:
1. **Escalabilidade**: Frontend e backend escalam independentemente
2. **Tecnologias diferentes**: React + Express (melhores ferramentas)
3. **Múltiplos clientes**: Web, mobile, desktop podem usar mesma API
4. **Deploy independente**: Atualizar frontend sem tocar backend
5. **Times separados**: Frontend e backend podem ser desenvolvidos em paralelo
6. **Performance**: CDN para frontend, servidor para backend

---

### 2. Por que usar Context API em vez de Redux?

**Context API** (nosso projeto):
```javascript
const { currentUser } = useAuth();
```

**Redux**:
```javascript
const currentUser = useSelector(state => state.auth.currentUser);
const dispatch = useDispatch();
```

**Por que Context API?**:
- ✅ Built-in no React (sem dependência extra)
- ✅ Mais simples para apps pequenos/médios
- ✅ Menos boilerplate
- ✅ Suficiente para nosso caso de uso
- ✅ Boa performance com poucos consumidores

**Quando usar Redux?**:
- App muito grande (50+ componentes)
- Estado muito complexo
- Necessidade de time-travel debugging
- Middleware complexo

---

### 3. Por que localStorage em vez de cookies?

**localStorage** (nosso projeto):
```javascript
localStorage.setItem('token', token);
```

**Cookies**:
```javascript
document.cookie = `token=${token}; HttpOnly; Secure`;
```

**Vantagens localStorage**:
- ✅ Fácil de usar (API simples)
- ✅ Capacidade maior (10MB vs 4KB)
- ✅ Não enviado automaticamente em requisições
- ✅ JavaScript tem acesso direto

**Desvantagens localStorage**:
- ❌ Vulnerável a XSS (Cross-Site Scripting)
- ❌ Não tem flag HttpOnly

**Mitigação**:
- Usamos Content Security Policy (Helmet)
- Sanitizamos inputs
- Token expira em 7 dias

**Quando usar Cookies**:
- Necessidade de HttpOnly (mais seguro)
- Server-side rendering
- Requisições automáticas

---

## 🐛 Troubleshooting Comum

### Problema 1: CORS Error

**Erro**:
```
Access to XMLHttpRequest blocked by CORS policy
```

**Causa**:
- Frontend (vercel.app) e Backend (railway.app) em domínios diferentes
- CORS_ORIGIN configurado errado

**Solução**:
```javascript
// backend/src/server.js
app.use(cors({
  origin: 'https://frontend-correto.vercel.app', // ← URL exata
  credentials: true
}));
```

---

### Problema 2: Token Inválido

**Erro**:
```
401 Unauthorized: Token inválido
```

**Causas**:
1. Token expirou (7 dias)
2. JWT_SECRET diferente no servidor
3. Token corrompido no localStorage

**Solução**:
```javascript
// Limpar e fazer login novamente
localStorage.clear();
window.location.href = '/login';
```

---

### Problema 3: Banco de Dados Não Conecta

**Erro**:
```
Unable to connect to database
```

**Causas**:
1. DATABASE_URL errada
2. PostgreSQL não está rodando
3. Firewall bloqueando conexão

**Verificar**:
```bash
# Ver variáveis
echo $DATABASE_URL

# Testar conexão
psql $DATABASE_URL
```

---

## 🎓 Perguntas Avançadas

### 1. Como otimizar performance do React?

**Resposta**:

**1. React.memo** (Evitar re-renders desnecessários):
```javascript
const Exame = React.memo(({ exame }) => {
  return <div>{exame.tipo}</div>;
});
```

**2. useMemo** (Cachear cálculos pesados):
```javascript
const totalCalorias = useMemo(() => {
  return exercicios.reduce((acc, ex) => acc + ex.calorias, 0);
}, [exercicios]);
```

**3. useCallback** (Cachear funções):
```javascript
const handleClick = useCallback(() => {
  console.log('Clicked');
}, []);
```

**4. Code Splitting** (Carregar sob demanda):
```javascript
const Dashboard = lazy(() => import('./pages/Dashboard'));

<Suspense fallback={<Loading />}>
  <Dashboard />
</Suspense>
```

**5. Virtual Lists** (Para listas grandes):
```javascript
// react-window
<FixedSizeList
  height={600}
  itemCount={exames.length}
  itemSize={50}
>
  {ExameRow}
</FixedSizeList>
```

---

### 2. Como implementar paginação?

**Backend**:
```javascript
// GET /api/exames?page=2&limit=10
async getAll(req, res) {
  const page = parseInt(req.query.page) || 1;
  const limit = parseInt(req.query.limit) || 10;
  const offset = (page - 1) * limit;
  
  const { count, rows } = await Exame.findAndCountAll({
    where: { userId: req.userId },
    limit,
    offset,
    order: [['data', 'DESC']]
  });
  
  res.json({
    exames: rows,
    totalPages: Math.ceil(count / limit),
    currentPage: page,
    totalItems: count
  });
}
```

**Frontend**:
```javascript
const [page, setPage] = useState(1);
const [exames, setExames] = useState([]);

useEffect(() => {
  const fetchExames = async () => {
    const response = await api.get(`/exames?page=${page}&limit=10`);
    setExames(response.data.exames);
  };
  fetchExames();
}, [page]);
```

---

### 3. Como fazer upload de arquivos?

**Backend** (Multer):
```javascript
const multer = require('multer');

const storage = multer.diskStorage({
  destination: 'uploads/',
  filename: (req, file, cb) => {
    cb(null, Date.now() + '-' + file.originalname);
  }
});

const upload = multer({ storage });

router.post('/exames/:id/resultado', 
  auth, 
  upload.single('arquivo'),
  async (req, res) => {
    const exame = await Exame.findByPk(req.params.id);
    exame.arquivoResultado = req.file.path;
    await exame.save();
    res.json(exame);
  }
);
```

**Frontend**:
```javascript
const handleUpload = async (e) => {
  const formData = new FormData();
  formData.append('arquivo', e.target.files[0]);
  
  await api.post(`/exames/${id}/resultado`, formData, {
    headers: { 'Content-Type': 'multipart/form-data' }
  });
};

return <input type="file" onChange={handleUpload} />;
```

---

### 4. Como implementar busca/filtros?

**Backend**:
```javascript
// GET /api/exames?search=sangue&tipo=laboratorial
async search(req, res) {
  const { search, tipo } = req.query;
  
  const where = { userId: req.userId };
  
  if (search) {
    where.tipo = { [Op.like]: `%${search}%` };
  }
  
  if (tipo) {
    where.tipo = tipo;
  }
  
  const exames = await Exame.findAll({ where });
  res.json(exames);
}
```

**Frontend**:
```javascript
const [filtros, setFiltros] = useState({ search: '', tipo: '' });

const buscar = async () => {
  const params = new URLSearchParams(filtros).toString();
  const response = await api.get(`/exames?${params}`);
  setExames(response.data);
};
```

---

### 5. Como adicionar testes?

**Backend** (Jest + Supertest):
```javascript
// __tests__/auth.test.js
const request = require('supertest');
const app = require('../src/server');

describe('POST /api/auth/register', () => {
  it('deve criar novo usuário', async () => {
    const response = await request(app)
      .post('/api/auth/register')
      .send({
        nome: 'João',
        email: 'joao@test.com',
        senha: '123456'
      });
    
    expect(response.status).toBe(201);
    expect(response.body).toHaveProperty('token');
  });
});
```

**Frontend** (React Testing Library):
```javascript
// __tests__/Login.test.jsx
import { render, screen, fireEvent } from '@testing-library/react';
import Login from '../pages/Login';

test('deve fazer login com sucesso', async () => {
  render(<Login />);
  
  fireEvent.change(screen.getByLabelText(/email/i), {
    target: { value: 'joao@test.com' }
  });
  
  fireEvent.change(screen.getByLabelText(/senha/i), {
    target: { value: '123456' }
  });
  
  fireEvent.click(screen.getByRole('button', { name: /entrar/i }));
  
  expect(await screen.findByText(/dashboard/i)).toBeInTheDocument();
});
```

---

## 🎯 Checklist Final para Apresentação

### Antes da Apresentação

- [ ] Testar aplicação completa (frontend + backend)
- [ ] Garantir que deploy está funcionando
- [ ] Ter exemplos de uso prontos (criar usuário, adicionar exame, etc)
- [ ] Revisar este documento
- [ ] Preparar diagrama de arquitetura (desenhar se necessário)
- [ ] Testar cenários de erro (login inválido, token expirado)

### Durante a Apresentação

- [ ] Mostrar aplicação rodando
- [ ] Demonstrar funcionalidades principais
- [ ] Mostrar código relevante (não todo o código)
- [ ] Explicar decisões de arquitetura
- [ ] Ter backup (screenshots) caso internet falhe

### Perguntas a se Preparar

- [ ] "Por que escolheu estas tecnologias?"
- [ ] "Como garantir segurança?"
- [ ] "Como escalar o sistema?"
- [ ] "Quais as limitações?"
- [ ] "O que faria diferente?"
- [ ] "Como testar a aplicação?"
- [ ] "Como fazer deploy?"
- [ ] "Quanto custa manter em produção?"

---

## 📈 Melhorias Futuras (para mencionar)

1. **Testes Automatizados**
   - Jest para backend
   - React Testing Library para frontend
   - Cypress para testes E2E

2. **CI/CD**
   - GitHub Actions
   - Deploy automático com testes
   - Rollback automático se falhar

3. **Monitoramento**
   - Sentry para erros
   - LogRocket para sessões
   - Google Analytics para métricas

4. **Performance**
   - Redis para cache
   - CDN para assets
   - Lazy loading de imagens

5. **Features**
   - Notificações push
   - Chat em tempo real (WebSockets)
   - Integração com wearables
   - Machine learning para previsões

---

## 🎓 Conclusão

Com este guia você está preparado para:
- ✅ Explicar qualquer parte técnica do projeto
- ✅ Responder perguntas dos professores com confiança
- ✅ Demonstrar conhecimento profundo
- ✅ Justificar decisões de arquitetura
- ✅ Propor melhorias futuras

**Boa sorte na apresentação! 🚀**

