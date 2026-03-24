# Structure Patterns

> These patterns show common project folder structures.
> Use the variant that matches your project's architecture.
> During Tier 2 setup, the matching variant is kept and others are removed.

---

## Shared Layers (all architectures)

```plaintext
repository-name/
├── src/
│   ├── {ProjectName}.Domain/
│   │   ├── Common/
│   │   ├── Exceptions/
│   │   ├── Models/
│   │   └── Utils/
│   │
│   ├── {ProjectName}.Infrastructure/
│   │   ├── Caches/
│   │   ├── Persistence/
│   │   │   ├── Configurations/
│   │   │   └── Repositories/
│   │   ├── Services/
│   │   └── Migrations/
│   │
│   └── {ProjectName}.WebApi/
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

---

## Variant: CQRS + MediatR (Application + WebApi)

```plaintext
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
│   │   │   │   ├── Commands/
│   │   │   │   │   └── {CommandName}/
│   │   │   │   │       ├── {CommandName}Command.cs
│   │   │   │   │       ├── {CommandName}CommandHandler.cs
│   │   │   │   │       └── {CommandName}CommandValidator.cs
│   │   │   │   └── Dtos/
│   │   │   └── {FeatureB}/
│   │   └── Utils/
│   │
│   └── {ProjectName}.WebApi/
│       ├── Endpoints/
│       │   ├── {FeatureA}Endpoints.cs
│       │   └── {FeatureB}Endpoints.cs
```

---

## Variant: Service Layer (Application + WebApi)

```plaintext
│   ├── {ProjectName}.Application/
│   │   ├── Common/
│   │   │   └── Interfaces/
│   │   ├── Services/
│   │   │   ├── {FeatureA}Service.cs
│   │   │   └── {FeatureB}Service.cs
│   │   ├── Dtos/
│   │   │   ├── {FeatureA}/
│   │   │   └── {FeatureB}/
│   │   ├── Validators/
│   │   └── Utils/
│   │
│   └── {ProjectName}.WebApi/
│       ├── Controllers/
│       │   ├── {FeatureA}Controller.cs
│       │   └── {FeatureB}Controller.cs
```

---

## Variant: Vertical Slices (Application + WebApi)

```plaintext
│   ├── {ProjectName}.Application/
│   │   ├── Common/
│   │   │   └── Interfaces/
│   │   ├── Features/
│   │   │   ├── {FeatureA}/
│   │   │   │   ├── Get{FeatureA}.cs          (handler + request + response)
│   │   │   │   ├── Create{FeatureA}.cs
│   │   │   │   └── {FeatureA}Validator.cs
│   │   │   └── {FeatureB}/
│   │   └── Utils/
│   │
│   └── {ProjectName}.WebApi/
│       ├── Endpoints/
│       │   ├── {FeatureA}Endpoints.cs
│       │   └── {FeatureB}Endpoints.cs
```
