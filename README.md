# Kitchen Stories

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
