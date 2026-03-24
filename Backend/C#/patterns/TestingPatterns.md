# Testing Patterns

> These patterns cover common test implementations.
> Use the variant that matches your project's architecture.
> During Tier 2 setup, irrelevant variants are removed and project-specific patterns are generated.

---

## Variant: CQRS — Unit Test for Query Handler

```csharp
using FluentAssertions;
using NSubstitute;

public class {QueryName}QueryHandlerTests
{
    private readonly I{Entity}Repository _repository = Substitute.For<I{Entity}Repository>();
    private readonly {QueryName}QueryHandler _handler;

    public {QueryName}QueryHandlerTests() => _handler = new(_repository);

    [Fact]
    public async Task Handle_WhenEntityExists_ShouldReturnDto()
    {
        var id = Guid.NewGuid();
        _repository.GetByIdAsync(id, Arg.Any<CancellationToken>())
            .Returns(new {Entity} { Id = id });

        var result = await _handler.Handle(new {QueryName}Query(id), CancellationToken.None);

        result.Should().NotBeNull();
        result!.Id.Should().Be(id);
    }

    [Fact]
    public async Task Handle_WhenEntityNotFound_ShouldReturnNull()
    {
        _repository.GetByIdAsync(Arg.Any<Guid>(), Arg.Any<CancellationToken>())
            .Returns(({Entity}?)null);

        var result = await _handler.Handle(new {QueryName}Query(Guid.NewGuid()), CancellationToken.None);

        result.Should().BeNull();
    }
}
```

---

## Variant: Service Layer — Unit Test for Service

```csharp
using FluentAssertions;
using NSubstitute;

public class {Entity}ServiceTests
{
    private readonly I{Entity}Repository _repository = Substitute.For<I{Entity}Repository>();
    private readonly ILogger<{Entity}Service> _logger = Substitute.For<ILogger<{Entity}Service>>();
    private readonly {Entity}Service _service;

    public {Entity}ServiceTests() => _service = new(_repository, _logger);

    [Fact]
    public async Task GetByIdAsync_WhenEntityExists_ShouldReturnDto()
    {
        var id = Guid.NewGuid();
        _repository.GetByIdAsync(id, Arg.Any<CancellationToken>())
            .Returns(new {Entity} { Id = id });

        var result = await _service.GetByIdAsync(id, CancellationToken.None);

        result.Should().NotBeNull();
        result!.Id.Should().Be(id);
    }

    [Fact]
    public async Task GetByIdAsync_WhenEntityNotFound_ShouldReturnNull()
    {
        _repository.GetByIdAsync(Arg.Any<Guid>(), Arg.Any<CancellationToken>())
            .Returns(({Entity}?)null);

        var result = await _service.GetByIdAsync(Guid.NewGuid(), CancellationToken.None);

        result.Should().BeNull();
    }
}
```

---

## Unit Test — FluentValidation Validator (all patterns)

```csharp
using FluentValidation.TestHelper;

public class {CommandName}ValidatorTests
{
    private readonly {CommandName}Validator _validator = new();

    [Fact]
    public void Validate_WhenAllFieldsValid_ShouldPass()
    {
        var result = _validator.TestValidate(new {CommandName} { /* valid props */ });
        result.ShouldNotHaveAnyValidationErrors();
    }

    [Fact]
    public void Validate_WhenIdIsEmpty_ShouldFailOnId()
    {
        var result = _validator.TestValidate(new {CommandName} { Id = Guid.Empty });
        result.ShouldHaveValidationErrorFor(x => x.Id);
    }
}
```

---

## Integration Test — API Endpoint

```csharp
public class {DomainModel}EndpointsTests : IClassFixture<WebApplicationFactory<Program>>
{
    private readonly HttpClient _client;

    public {DomainModel}EndpointsTests(WebApplicationFactory<Program> factory)
        => _client = factory.CreateClient();

    [Fact]
    public async Task Get_WhenEntityExists_ShouldReturn200()
    {
        var id = Guid.NewGuid(); // pre-seeded in test DB

        var response = await _client.GetAsync($"/api/v1/{domainmodel}/{id}");

        response.StatusCode.Should().Be(HttpStatusCode.OK);
    }
}
```
