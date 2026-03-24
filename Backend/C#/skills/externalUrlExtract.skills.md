# External URL Extract Skill

## Overview

The **ExternalUrlExtractSkill** is an agentic skill responsible for
extracting content from external URLs in a resilient, adaptive, and
cost-optimized manner.

It implements a **multi-tier extraction strategy**:

-   **Tier 1 (Fast & Cheap):** HttpClient-based HTML fetch
-   **Tier 2 (Fallback):** Headless browser (Playwright) when bot
    detection is triggered

This approach ensures: - High performance for simple/public websites -
Robust handling of bot protection systems (Cloudflare, Akamai, DataDome,
etc.) - Cost efficiency by only using expensive resources when necessary

------------------------------------------------------------------------

## Responsibilities

-   Fetch HTML content from external URLs
-   Detect bot protection mechanisms (hard block & soft block)
-   Automatically fallback to browser-based extraction when required
-   Return normalized HTML content for downstream processing
    (AngleSharp, chunking pipeline, etc.)

------------------------------------------------------------------------

## Architecture

    Input URL
       │
       ├── Tier 1: HttpClient
       │       │
       │       ├── Success → Return HTML
       │       │
       │       └── Bot Detected → Fallback
       │
       └── Tier 2: Playwright (Headless Browser)
               │
               └── Return HTML

------------------------------------------------------------------------

## Interface Contract

``` csharp
public interface IExternalUrlExtractSkill
{
    Task<ExtractResult> ExecuteAsync(
        string url,
        CancellationToken ct);
}

public record ExtractResult(
    string Content,
    bool IsFromBrowser,
    string? Error);
```

------------------------------------------------------------------------

## Bot Detection Strategy

``` csharp
public interface IBotDetectionService
{
    bool IsBotBlocked(HttpResponseMessage response, string content);
}
```

### Detection Rules

**Hard Blocks** - 403 Forbidden - 429 Too Many Requests - 503 Service
Unavailable

**Soft Blocks** - "Checking your browser" - "Just a moment" -
"cf-browser-verification" - Low-quality or empty HTML

------------------------------------------------------------------------

## Implementation Details

### Tier 1: HttpClient

``` csharp
var request = new HttpRequestMessage(HttpMethod.Get, url);

request.Headers.UserAgent.ParseAdd(
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 Chrome/122.0.0.0 Safari/537.36");

request.Headers.Accept.ParseAdd("text/html,application/xhtml+xml");
request.Headers.AcceptLanguage.ParseAdd("en-US,en;q=0.9");
```

------------------------------------------------------------------------

### Tier 2: Playwright

``` csharp
using var playwright = await Playwright.CreateAsync();
var browser = await playwright.Chromium.LaunchAsync(new() { Headless = true });

var page = await browser.NewPageAsync();
await page.GotoAsync(url);

var html = await page.ContentAsync();
```

------------------------------------------------------------------------

## Orchestration Flow

``` csharp
public async Task<ExtractResult> ExecuteAsync(string url, CancellationToken ct)
{
    try
    {
        var response = await _httpClient.SendAsync(
            new HttpRequestMessage(HttpMethod.Get, url), ct);

        var html = await response.Content.ReadAsStringAsync(ct);

        if (!_botDetection.IsBotBlocked(response, html))
        {
            return new ExtractResult(html, false, null);
        }

        var browserHtml = await _browserExtractor.ExtractAsync(url, ct);

        return new ExtractResult(browserHtml, true, null);
    }
    catch (Exception ex)
    {
        return new ExtractResult(string.Empty, false, ex.Message);
    }
}
```

------------------------------------------------------------------------

## Observability

Log: - Url - TierUsed - BotDetected - Duration - ContentLength

------------------------------------------------------------------------

## Summary

-   Multi-tier extraction
-   Bot detection (status + HTML)
-   Automatic fallback
-   Cost optimized
-   Extensible design
