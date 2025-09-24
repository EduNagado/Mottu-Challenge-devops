# Mottu - GEF

## 📋 Sobre o Projeto
Este projeto foi desenvolvido como parte do Challenge da disciplina DevOps Tools and Cloud Computing.
O objetivo foi criar e realizar o deploy de uma API utilizando Java com Spring Boot e Banco de Dados Azure SQL.

## Proposta do Projeto
Implementar uma API que ofereça recursos para o gerenciamento de motos, pátios e usuários, contemplando operações de criação, leitura, edição e remoção de informações.

## Tecnologias Utilizadas

- Java 21  
- Spring Boot 3.5.6  
- Spring Security com JWT  
- SpringDoc OpenAPI (Swagger)  
- Azure SQL Database  
- Gradle

  ## Passo a passo do deploy
```bash
#!/bin/bash
# ============================================
# 🚀 Script de Deploy - API Java no Azure
# Autor: Eduardo
# Projeto: GefMottu - Gestão de Motos e Pátios
# ============================================

# 🔑 Variáveis principais
RESOURCE_GROUP="mottu-gef-dev-rg"
LOCATION="brazilsouth"
SQL_SERVER_NAME="gef-mottu-sqlsrv"
SQL_ADMIN_USER="admsql"
SQL_ADMIN_PASS="SUA_SENHA_FORTE_AQUI"
SQL_DB_NAME="mottu_db"
APP_SERVICE_PLAN="GefMottuPlan"
WEBAPP_NAME="GefMottu"
GITHUB_REPO="https://github.com/EduNagado/Mottu-Challenge.git"
BRANCH="master"

# ⚠️ Antes de rodar, exporte o token do GitHub:
# export GITHUB_TOKEN="SEU_TOKEN_GITHUB"

# ============================================
# 1️⃣ Criar Resource Group
# ============================================
az group create -n $RESOURCE_GROUP -l $LOCATION

# ============================================
# 2️⃣ Criar Servidor SQL
# ============================================
az sql server create \
  -l $LOCATION \
  -g $RESOURCE_GROUP \
  -n $SQL_SERVER_NAME \
  -u $SQL_ADMIN_USER \
  -p $SQL_ADMIN_PASS \
  --enable-public-network true

# ============================================
# 3️⃣ Criar Banco de Dados
# ============================================
az sql db create \
  -g $RESOURCE_GROUP \
  -s $SQL_SERVER_NAME \
  -n $SQL_DB_NAME \
  --service-objective Basic \
  --backup-storage-redundancy Local \
  --zone-redundant false

# ============================================
# 4️⃣ Criar Regra de Firewall (Acesso Total)
# ============================================
az sql server firewall-rule create \
  -g $RESOURCE_GROUP \
  -s $SQL_SERVER_NAME \
  -n AllowAll \
  --start-ip-address 0.0.0.0 \
  --end-ip-address 255.255.255.255

# ============================================
# 5️⃣ Criar App Service Plan
# ============================================
az appservice plan create \
  --name $APP_SERVICE_PLAN \
  --resource-group $RESOURCE_GROUP \
  --sku B1 \
  --is-linux

# ============================================
# 6️⃣ Criar Web App (App Service) para Java 21
# ============================================
az webapp create \
  --resource-group $RESOURCE_GROUP \
  --plan $APP_SERVICE_PLAN \
  --name $WEBAPP_NAME \
  --runtime "JAVA|21-java21"

# ============================================
# 7️⃣ Configurar Deploy Automático via GitHub
# ============================================
az webapp deployment source config \
  --name $WEBAPP_NAME \
  --resource-group $RESOURCE_GROUP \
  --repo-url $GITHUB_REPO \
  --branch $BRANCH \
  --git-token $GITHUB_TOKEN

# ============================================
# ✅ Fim do Script
# Sua aplicação será buildada pelo GitHub Actions
# e publicada automaticamente no App Service.
# ============================================

```
## App Service
https://gef-mottu-dev-app-a2dffngahzayd3an.brazilsouth-01.azurewebsites.net/swagger-ui/swagger-ui/index.html#/

## Endpoints e exemplos de testes

### Usuario
| Método | Endpoint           | Descrição                       |
|--------|--------------------|--------------------------------|
| GET    | /usuarios            | Lista todos os usuários cadastrados      |
| GET    | /usuarios            | Consulta usuário por ID                  |
| POST   | /usuarios            | Cadastra novo usuário                    |
| PUT    | /usuarios            | Atualiza dados de um usuário             |
| DELETE | Usuario/{id}         | Remove uma usuário pelo ID               |

**Exemplo POST**
```json
{
  "nome": "Eduado Enrique",
  "email": "eduardo.nagado@gmail.com",
  "password": "Eduardo2004-",
  "cpf": "123.456.789-09",
  "cargo": "ADMINISTRADOR"
}
```
**Exemplo PUT**
```json
{
  "nome": "Eduado Henrique",
  "email": "eduardo.nagado@gmail.com",
  "password": "Eduardo2004-",
  "cpf": "123.456.789-09",
  "cargo": "ADMINISTRADOR"
}
```

### Moto
| Método | Endpoint           | Descrição                       |
|--------|--------------------|--------------------------------|
| GET    | /motos             | Lista todas as motos                        |
| GET    | /moto/{id}         | Consulta moto por ID                        |
| POST   | /moto              | Cadastra nova moto                          |
| PUT    | /moto/{id}         | Atualiza dados de uma moto                  |
| DELETE | /moto/{id}         | Remove uma moto pelo ID                     |

**Exemplo POST**
```json
{
  "modelo": "MOTTU_POP",
  "ano": 2015,
  "cor": "Preta",
  "placa": "ABC1D23",
  "status": "DISPONIVEL"
}
```

**Exemplo PUT**
```json
{
  "modelo": "MOTTU_POP",
  "ano": 2020,
  "cor": "Preta",
  "placa": "ABC1D23",
  "status": "DISPONIVEL"
}
```

### Patio
| Método | Endpoint           | Descrição                       |
|--------|--------------------|--------------------------------|
| GET    | patios             | Lista todos os pátios cadastrados          |
| GET    | patios/{id}        | Retorna os dados de um pátio pelo ID       |
| POST   | patios             | Cadastra um novo pátio                     |
| PUT    | patios/{id}        | Atualiza os dados de um pátio existente    |
| DELETE | patios/{id}        | Remove um pátio do sistema pelo ID         |


**Exemplo POST**
```json

{
  "localizacao": "Patio Sul",
  "quantidadeVagas": 2690
}
```

**Exemplo PUT**
```json
{
  "localizacao": "Patio Sul",
  "quantidadeVagas": 3000
}
```
