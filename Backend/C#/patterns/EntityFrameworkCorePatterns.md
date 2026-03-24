# Entity Framework Core Patterns

## Audit Fields Pattern

Use a centralized persistence pattern to apply audit fields consistently for all auditable entities.

```csharp
public override async Task<int> SaveChangesAsync(CancellationToken cancellationToken = default)
{
    var now = dateTimeProvider.UtcNow;
    var userId = userContext.UserId;

    foreach (var entry in ChangeTracker.Entries<BaseEntity>())
    {
        switch (entry.State)
        {
            case EntityState.Added:
                entry.Entity.CreatedBy = userId;
                entry.Entity.CreatedAt = now;
                entry.Entity.UpdatedBy = userId;
                entry.Entity.UpdatedAt = now;
                break;

            case EntityState.Modified:
                entry.Property(x => x.CreatedBy).IsModified = false;
                entry.Property(x => x.CreatedAt).IsModified = false;

                entry.Entity.UpdatedBy = userId;
                entry.Entity.UpdatedAt = now;
                break;
        }
    }

    var result = await base.SaveChangesAsync(cancellationToken);
    await DispatchDomainEvents();
    return result;
}
```