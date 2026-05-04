# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

**Repository:** https://github.com/suedeapple/population-checker-umbraco

## Commands

```bash
# Build
dotnet build

# Run (HTTPS on https://localhost:44379)
dotnet run --project population.Web

# Build individual projects
dotnet build population.Web
dotnet build population.Headless
```

There are no automated tests in this repository.

## Architecture

This is an **Umbraco 17 CMS** solution targeting **.NET 10** with two projects:

### population.Web
The main Umbraco web application. Hosts the backoffice, Razor-based frontend, and all Umbraco APIs. Content models are auto-generated into `population.Web/umbraco/models/` — do not edit these files manually. In development, `ModelsBuilder` is set to `SourceCodeAuto` (models regenerate on schema change); in production it is `SourceCodeManual`.

### population.Headless
A class library referenced by `population.Web` that adds headless/API capabilities:

- **Startup/HeadlessComposer.cs** — Umbraco composer that registers the Swagger configuration. Entry point for all headless wiring.
- **Revalidate/** — Next.js ISR webhook system. When content is published, deleted, or trashed, `NextJsRevalidatePublishedNotificationHandler` fires and `NextJsRevalidateService` sends an HMAC-SHA256-signed HTTP POST to the configured Next.js revalidate endpoint. Controlled via `NextJs:Revalidate` config (disabled by default).

Umbraco composers (classes implementing `IComposer`) are the correct extension point for registering services, notification handlers, and middleware — they run at startup automatically, no manual registration in `Program.cs` required.

## Key Configuration

`appsettings.json` is git-ignored (contains the SQL Server connection string and webhook secret). Use `appsettings.Development.json` for local overrides — this file is also git-ignored.

Notable settings:
- `Umbraco:CMS:DeliveryApi:Enabled: true` — the Delivery API is on
- `Umbraco:CMS:ModelsBuilder:ModelsMode` — `SourceCodeAuto` in dev, `SourceCodeManual` in prod
- `NextJs:Revalidate:Enabled` — toggle the webhook revalidation system
- `NextJs:Revalidate:WebHookSecret` — shared secret for HMAC signing

## Content Types

Umbraco content types in use: `HomePage`, `Continent`, `Country`, `Year`, `Settings`. Generated model classes live in `population.Web/umbraco/models/`.

## NuGet / Package Management

Central Package Management is enabled via `population.Web/Directory.Packages.props`. Add new packages with a `<PackageVersion>` entry there and a `<PackageReference>` (no version) in the `.csproj`.
