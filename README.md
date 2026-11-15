# Sistema de RH — API de funcionários na Azure

API REST para cadastro e manutenção de funcionários, desenvolvida como solução para o [desafio .NET com Azure da DIO](https://github.com/digitalinnovationone/trilha-net-azure-desafio).

O projeto foi atualizado do **.NET 6.0 para .NET 10.0** e foi concebido para funcionar em nuvem, utilizando serviços da **Microsoft Azure** para hospedar a API, armazenar os dados dos funcionários e registrar o histórico de alterações.

## Funcionalidades

- Consulta de funcionário por identificador.
- Cadastro, atualização e remoção de funcionários.
- Registro das ações de inclusão, atualização e remoção no Azure Table Storage.
- Documentação interativa dos endpoints com Swagger.

## Arquitetura e serviços Azure

| Componente | Responsabilidade |
|---|---|
| ASP.NET Core Web API | Disponibiliza os endpoints REST e pode ser hospedada no Azure App Service. |
| Azure SQL Database (SQL Server) | Armazena os registros de funcionários por meio do Entity Framework Core. |
| Azure Table Storage | Armazena os logs das alterações. Cada log registra a ação, os dados do funcionário em JSON e os campos da entidade. |

A API usa a connection string `ConexaoPadrao` para acessar o banco SQL e `SAConnectionString` para acessar o Azure Table Storage. A tabela de logs é definida por `AzureTableName` e criada pela aplicação caso ainda não exista. Para uso em nuvem, configure essas opções com os dados dos recursos Azure correspondentes.

## Tecnologias

- .NET 10.0 / ASP.NET Core
- Entity Framework Core 10
- SQL Server
- Azure.Data.Tables
- Swagger / OpenAPI

## Endpoints

| Verbo | Rota | Descrição |
|---|---|---|
| `GET` | `/Funcionario/{id}` | Consulta um funcionário pelo identificador. |
| `POST` | `/Funcionario` | Cadastra um funcionário e registra a inclusão. |
| `PUT` | `/Funcionario/{id}` | Atualiza os dados de um funcionário e registra a alteração. |
| `DELETE` | `/Funcionario/{id}` | Remove um funcionário e registra a remoção. |

Exemplo de corpo para criação ou atualização:

```json
{
  "nome": "Ana Silva",
  "endereco": "Rua Exemplo, 123",
  "ramal": "1234",
  "emailProfissional": "ana.silva@empresa.com",
  "departamento": "TI",
  "salario": 5000.00,
  "dataAdmissao": "2024-01-15T00:00:00Z"
}
```

## Executar localmente

Pré-requisito: [.NET 10 SDK](https://dotnet.microsoft.com/download/dotnet/10.0).

1. Configure as connection strings do banco SQL e do Azure Table Storage.
2. Na pasta do projeto, restaure dependências e inicie a API:

   ```powershell
   dotnet restore
   dotnet run
   ```

3. Abra o Swagger em `https://localhost:7090/swagger` (ou na URL informada pela aplicação).

O arquivo `appsettings.json` contém valores vazios para as connection strings. Em desenvolvimento, elas podem ser fornecidas por *User Secrets* ou variáveis de ambiente, sem gravar credenciais no repositório. Os nomes das variáveis de ambiente seguem a hierarquia de configuração do .NET:

```text
ConnectionStrings__ConexaoPadrao
ConnectionStrings__SAConnectionString
ConnectionStrings__AzureTableName
```

## Banco de dados

As migrations do Entity Framework Core estão no diretório `Migrations`. Com a connection string do banco configurada, aplique-as usando:

```powershell
dotnet ef database update
```

## Implantação na Azure

Para executar em nuvem, publique a API no **Azure App Service** e configure no serviço as connection strings para o **Azure SQL Database** e o **Azure Table Storage**. A aplicação utiliza as configurações de ambiente do App Service para obter esses valores; não inclua credenciais em arquivos versionados.
