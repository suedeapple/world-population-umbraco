# Population Checker — Umbraco

Umbraco 17 CMS backend for the Population Checker project, running on .NET 10. Provides content management via the Umbraco backoffice and a headless Delivery API consumed by a Next.js frontend.

**Live site:** https://population-checker.vercel.app/
**Repository:** https://github.com/suedeapple/population-checker-umbraco

## Tech Stack

| Layer | Technology |
|---|---|
| CMS | [Umbraco 17](https://umbraco.com/) |
| Runtime | .NET 10 |
| Database | SQL Server |
| Delivery API | Umbraco Delivery API (headless) |
| Frontend | Next.js (hosted on Vercel) |
| Deployment | Vercel (frontend) |

## Prerequisites

- [.NET 10 SDK](https://dotnet.microsoft.com/download)
- SQL Server (connection string configured in `appsettings.json`)

## Getting Started

1. Clone the repository
2. Create `population.Web/appsettings.json` with your connection string and any environment-specific settings (this file is git-ignored)
3. Run the application:

```bash
dotnet run --project population.Web
```

The site will be available at `https://localhost:44379`. The Umbraco backoffice is at `/umbraco`.

## Projects

| Project | Description |
|---|---|
| `population.Web` | Umbraco CMS web application |
| `population.Headless` | Headless API extensions and Next.js revalidation webhooks |

## Next.js Integration

The `population.Headless` project sends HMAC-signed webhook requests to a Next.js revalidate endpoint whenever content is published, deleted, or moved to the recycle bin. Configure this via `appsettings.json`:

```json
"NextJs": {
  "Revalidate": {
    "Enabled": true,
    "WebHookUrls": ["https://your-next-app.com/api/revalidate"],
    "WebHookSecret": "your-shared-secret"
  }
}
```
