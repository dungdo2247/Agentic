
```plaintext
repository-name/
├── src/
│   ├── {ProjectName}.Domain/
│   │   ├── Common/
│   │   ├── Exceptions/
│   │   ├── Models/
│   │   └── Utils/
│   │
│   ├── {ProjectName}.Application/
│   │   ├── Common/
│   │   │   ├── Behaviors/
│   │   │   └── Interfaces/
│   │   ├── Features/
│   │   │   ├── {FeatureA}/
│   │   │   │   ├── Queries/
│   │   │   │   │   └── {QueryName}/
│   │   │   │   │       ├── {QueryName}Query.cs
│   │   │   │   │       ├── {QueryName}QueryHandler.cs
│   │   │   │   │       └── {QueryName}QueryValidator.cs
│   │   │   │   └── Dtos/
│   │   │   └── {FeatureB}/
│   │   └── Utils/
│   │
│   ├── {ProjectName}.Infrastructure/
│   │   ├── Caches/
│   │   ├── Services/
│   │   └── Integrations/
│   │
│   └── {ProjectName}.WebApi/
│       ├── Endpoints/
│       ├── Extensions/
│       └── Middleware/
│
├── tests/
│   ├── {ProjectName}.Domain.Tests/
│   ├── {ProjectName}.Application.Tests/
│   └── {ProjectName}.IntegrationTests/
│
├── .gitignore
├── docker-compose.yml
├── {ProjectName}.slnx
└── README.md
```
