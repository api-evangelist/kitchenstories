# Kitchen Stories

Kitchen Stories is a Berlin-based, video-first cooking and recipe platform founded in 2013 by
Verena Hubertz and Mengting Boensch, majority-owned by the BSH Group (Bosch Siemens Hausgeraete)
since 2017. It publishes roughly 15,000 recipes and articles with HD step-by-step video tutorials
across iOS, Android, web, smart TVs and Amazon Echo Show, reaching more than 6 million unique
users from over 22 million app downloads. Kitchen Stories Business runs branded content and native
advertising with around 300 brand partners.

- Website — https://www.kitchenstories.com
- GitHub — https://github.com/KitchenStories
- Backed by: point-nine

## API

Kitchen Stories has **no public developer program**, but it does serve its own OpenAPI 3.0.0
description at the root of its production API:

- **Kitchen Stories Internal API** ("Ultron") — https://api.kitchenstories.io/api/
- 110 paths, 157 operations, 176 component schemas
- JWT bearer authentication (`Authorization: Bearer`), plus a vendor `X-Ultron-User` header
- Vendor media type `application/vnd.ajns.kitchenstories+json; version=3`; the major version is
  negotiated by media type and echoed in `x-ultron-api-version`
- Covers recipes, articles, videos, ingredients, utensils, categories, tags, content collections
  and feeds, plus authenticated user cookbooks, meal plans, private and external recipes, likes,
  ratings, comments and personalization

All operations are globally secured, and there is no self-service credential issuance — treat this
as an internal, first-party API rather than a consumable public one.

## Artifacts in this repo

| Artifact | Path | Method |
|---|---|---|
| OpenAPI 3.0.0 | `openapi/kitchenstories-internal-openapi.json` | searched (provider-published) |
| llms.txt | `llms/kitchenstories-llms.txt` | searched (provider-published) |
| Packages | `packages/kitchenstories-packages.yml` | searched |
| Well-Known | `well-known/kitchenstories-well-known.yml` | searched |
| Domain security | `security/kitchenstories-domain-security.yml` | probed |
| Authentication | `authentication/kitchenstories-authentication.yml` | searched |
| Conventions | `conventions/kitchenstories-conventions.yml` | derived |
| Error catalog | `errors/kitchenstories-problem-types.yml` | derived |
| Data model | `data-model/kitchenstories-data-model.yml` | derived |
| Lifecycle | `lifecycle/kitchenstories-lifecycle.yml` | derived |
| Conformance | `conformance/kitchenstories-conformance.yml` | derived |
| MCP server (candidate) | `mcp/kitchenstories-mcp.yml` | derived |
| Overlay | `overlays/kitchenstories-internal-overlay.yaml` | generated |
| Agent Skills (4) | `skills/` | generated |

## Deliberately absent

Recorded as genuine gaps rather than fabricated: no OAuth scopes (the API uses bearer tokens, not
OAuth 2.0), no idempotency contract, no documented rate limits, no AsyncAPI or webhook surface, no
sandbox, no CLI, no SDKs for this API, no changelog, no status page, no deprecation policy, no
`/.well-known/` documents, no security.txt or vulnerability-disclosure program, and no trust
centre or named compliance certification.
