---
applyTo: "**/appsettings*.json"
---

# Application Settings Instructions

## Structure Rules

All configuration in `appsettings.json` must be organized under **two root sections**:

| Section | Purpose |
|---|---|
| `"connection"` | All external connection strings and endpoints (database, cache, blob storage, external APIs) |
| `"application"` | All application logic settings (feature flags, retry policies, cache durations, business defaults) |

> `Logging` and `AllowedHosts` remain at root level as per ASP.NET Core convention -- keep their default casing.

## JSON Key Casing

- All custom configuration keys must use **camelCase**
- Example: `"cacheDurationInHours"`, `"maxPageSize"`, `"hangfireDatabase"`

## Rules

1. **Never** place connection strings outside `"connection"`
2. **Never** place logic/business settings inside `"connection"`
3. Use strongly-typed options classes bound via `IOptions<T>` pattern
4. Connection strings are read using `configuration.GetSection("connection")["database"]` or a dedicated options class
5. Sensitive values (passwords, keys) should use **User Secrets** in development and **environment variables or Azure Key Vault** in production -- never commit real credentials
6. `appsettings.Development.json` may override values but must follow the same section structure

## Strongly-Typed Options Classes

| Class | Section Bound To | Location |
|---|---|---|
| `ConnectionOptions` | `"connection"` | `Application/Common/Options/` |
| `ApplicationOptions` | `"application"` | `Application/Common/Options/` |

> See `patterns/SettingsPatterns.md` for code samples.
## API Key

- The API key belongs under the `"application"` section as `apiKey`
- **Never commit a real value** — use `dotnet user-secrets` locally and environment variables or Azure Key Vault in production
- The `ApplicationOptions` class must expose an `ApiKey` property bound to `application:apiKey`
