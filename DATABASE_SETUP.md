# Configuração de Banco de Dados - PYREST-FRAMEWORK

Este guia explica como configurar o banco de dados com Prisma no PYREST-FRAMEWORK.

## 📋 Opções de Instalação

### 1. **Instalação Básica (Sem Banco de Dados)**
```bash
pip install pyrest-framework
```
- ✅ Funciona imediatamente
- ✅ Usa repositório em memória
- ✅ Ideal para desenvolvimento e testes

### 2. **Instalação com PostgreSQL**
```bash
pip install pyrest-framework[postgres]
```
- ✅ Inclui Prisma + PostgreSQL
- ✅ Configuração automática
- ✅ Ideal para produção

### 3. **Instalação com MySQL**
```bash
pip install pyrest-framework[mysql]
```
- ✅ Inclui Prisma + MySQL
- ✅ Configuração automática

### 4. **Instalação Completa**
```bash
pip install pyrest-framework[database]
```
- ✅ Inclui todos os bancos suportados
- ✅ Máxima flexibilidade

## 🗄️ Configuração do PostgreSQL

### 1. **Instalar PostgreSQL**

#### Windows:
```bash
# Download do instalador oficial
# https://www.postgresql.org/download/windows/
```

#### macOS:
```bash
brew install postgresql
brew services start postgresql
```

#### Linux (Ubuntu/Debian):
```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
sudo systemctl start postgresql
sudo systemctl enable postgresql
```

### 2. **Criar Banco de Dados**
```bash
# Conectar ao PostgreSQL
sudo -u postgres psql

# Criar usuário e banco
CREATE USER pyrest_user WITH PASSWORD 'your_password';
CREATE DATABASE pyrest_demo OWNER pyrest_user;
GRANT ALL PRIVILEGES ON DATABASE pyrest_demo TO pyrest_user;
\q
```

### 3. **Configurar Variável de Ambiente**
```bash
# Windows
set DATABASE_URL=postgresql://pyrest_user:your_password@localhost:5432/pyrest_demo

# macOS/Linux
export DATABASE_URL=postgresql://pyrest_user:your_password@localhost:5432/pyrest_demo
```

## 🔧 Configuração do Prisma

### 1. **Instalar Prisma CLI**
```bash
# Via npm
npm install -g prisma

# Via yarn
yarn global add prisma
```

### 2. **Configuração Automática**
O framework configura automaticamente o Prisma:

```python
from pyrest import setup_database

# Configuração automática
db_manager = setup_database(
    database_url="postgresql://user:password@localhost:5432/database",
    provider="postgresql",
    auto_setup=True
)
```

### 3. **Configuração Manual**
```python
from pyrest import DatabaseConfig, PrismaSetup

# Configuração manual
config = DatabaseConfig(
    database_url="postgresql://user:password@localhost:5432/database",
    provider="postgresql"
)

setup = PrismaSetup(config)
setup.create_prisma_directory()
setup.create_schema_file()
setup.install_prisma()
setup.generate_client()
setup.push_schema()
```

## 📁 Estrutura Gerada

Após a configuração, será criada esta estrutura:

```
seu-projeto/
├── prisma/
│   ├── schema.prisma          # Schema do banco
│   ├── migrations/            # Migrações
│   └── generated/             # Cliente gerado
├── app.py                     # Sua aplicação
└── .env                       # Variáveis de ambiente
```

## 🔄 Comandos do Prisma

### Gerar Cliente
```bash
prisma generate
```

### Fazer Push do Schema
```bash
prisma db push
```

### Criar Migração
```bash
prisma migrate dev --name init
```

### Visualizar Dados
```bash
prisma studio
```

## 💻 Exemplo de Uso

### 1. **Configuração Básica**
```python
from pyrest import create_app, setup_database, PrismaRepository, UserService, UserController

# Configura banco
db_manager = setup_database(
    "postgresql://user:password@localhost:5432/database"
)

# Cria repositório
user_repository = PrismaRepository("user", db_manager)

# Cria service e controller
user_service = UserService(user_repository)
user_controller = UserController(user_service)
```

### 2. **Configuração com Fallback**
```python
from pyrest import setup_database, PrismaRepository, InMemoryRepository

# Tenta conectar ao banco
db_manager = setup_database()

if db_manager.client:
    # Usa Prisma
    repository = PrismaRepository("user", db_manager)
else:
    # Fallback para memória
    repository = InMemoryRepository()
```

### 3. **Schema Personalizado**
```python
from pyrest import create_prisma_schema

# Define modelos personalizados
custom_models = {
    "Customer": {
        "fields": [
            {"name": "id", "type": "String", "attributes": ["@id", "@default(cuid)"]},
            {"name": "name", "type": "String"},
            {"name": "email", "type": "String", "attributes": ["@unique"]},
            {"name": "phone", "type": "String", "optional": True},
            {"name": "created_at", "type": "DateTime", "attributes": ["@default(now())"]}
        ]
    }
}

# Cria schema
setup = create_prisma_schema(custom_models)
setup.generate_client()
setup.push_schema()
```

## 🚨 Solução de Problemas

### Erro: "Prisma CLI not found"
```bash
# Instalar Prisma CLI
npm install -g prisma
# ou
yarn global add prisma
```

### Erro: "Connection failed"
```bash
# Verificar se PostgreSQL está rodando
sudo systemctl status postgresql

# Verificar credenciais
psql -h localhost -U pyrest_user -d pyrest_demo
```

### Erro: "Database does not exist"
```bash
# Criar banco
createdb pyrest_demo
# ou
psql -c "CREATE DATABASE pyrest_demo;"
```

### Erro: "Permission denied"
```bash
# Dar permissões ao usuário
psql -c "GRANT ALL PRIVILEGES ON DATABASE pyrest_demo TO pyrest_user;"
```

## 📊 Comparação de Performance

| Banco | Velocidade | Complexidade | Produção |
|-------|------------|--------------|----------|
| In-Memory | ⚡⚡⚡⚡⚡ | ⚡ | ❌ |
| SQLite | ⚡⚡⚡⚡ | ⚡⚡ | ⚠️ |
| PostgreSQL | ⚡⚡⚡⚡ | ⚡⚡⚡ | ✅ |
| MySQL | ⚡⚡⚡ | ⚡⚡⚡ | ✅ |

## 🎯 Recomendações

### **Desenvolvimento:**
- Use `InMemoryRepository` para testes rápidos
- Use SQLite para desenvolvimento local

### **Produção:**
- Use PostgreSQL com Prisma
- Configure variáveis de ambiente
- Use migrações para controle de versão

### **Testes:**
- Use `InMemoryRepository` para testes unitários
- Use banco separado para testes de integração

## 🔗 Recursos Adicionais

- [Documentação do Prisma](https://www.prisma.io/docs)
- [PostgreSQL Tutorial](https://www.postgresql.org/docs/current/tutorial.html)
- [Python Database Best Practices](https://docs.python.org/3/library/sqlite3.html)

---

**Happy coding! 🚀** 