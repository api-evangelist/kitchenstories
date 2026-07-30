---
name: kitchenstories-recipe-discovery
description: >-
  Browse, search and retrieve Kitchen Stories recipes, articles and videos from the Kitchen
  Stories Internal API, including recommendations, the recipe of the day, categories and tags.
api: kitchenstories:internal-api
base_url: https://api.kitchenstories.io/api
generated: '2026-07-19'
method: generated
source: openapi/kitchenstories-internal-openapi.json
operations:
  - autenticate-credentials
  - other-search-suggestions
  - recipe-list
  - recipe-detail
  - recipe-recommendation-list
  - recipe-of-the-day
  - article-list
  - article-detail
  - video-list
  - video-detail
  - category-list
  - category-detail
  - tag-list
  - feed-list
---

# Discovering content on Kitchen Stories

Read-only discovery across the Kitchen Stories content catalog: recipes, editorial articles,
videos, categories and tags.

> **Access.** This is a first-party internal API. There is no public developer program and no
> self-service credential issuance — every operation requires a bearer token, including the
> read-only ones below. Do not attempt to call it without a credential the operator already holds.

## Before you start

1. Obtain a JWT with `autenticate-credentials` (`POST /authenticate/credentials/`) and read
   `access_token` from the response.
2. Send it on every request as `Authorization: Bearer <access_token>`.
3. Set `Accept: application/vnd.ajns.kitchenstories+json; version=3` to pin the major version.
   Responses echo `x-ultron-api-version: 3`.
4. Every path requires a trailing slash. The single exception in the whole API is
   `GET /users/validate/email`.

## Steps

### 1. Find candidate content

- For a free-text starting point, call `other-search-suggestions`
  (`GET /search/suggestions/`) with `search`.
- To browse, call `recipe-list` (`GET /recipes/`), `article-list` (`GET /articles/`) or
  `video-list` (`GET /videos/`).
- To see the mixed editorial stream of recipes, articles and videos together, call `feed-list`
  (`GET /feed/`).

Useful filters on the list operations: `slug`, `tag`, `category`, `type`, `ids`, `language`,
and `order` (`date` or `likes`, descending only — any other value is silently ignored).

### 2. Page through results

List responses use a page-number envelope:

```json
{ "data": [ ... ],
  "links": { "first": "...", "last": "...", "next": "...", "prev": "..." },
  "meta":  { "pagination": { "page": 1, "pages": 42, "count": 837 } } }
```

Follow `links.next` until it is absent, or drive `?page=` from `meta.pagination.pages`. Never
assume a fixed page size — read `meta.pagination.count`.

### 3. Keep payloads small

Pass `simplified=true` on any operation that accepts it (16 do, including `recipe-list`,
`article-list` and `article-detail`) to get the reduced representation. Use it whenever you only
need titles, slugs and images; fetch the full object only for the item you actually need.

### 4. Retrieve the full item

Call `recipe-detail` (`GET /recipes/{uuid-or-slug}/`), `article-detail` or `video-detail`. The
path parameter accepts **either** the UUID **or** the human-readable slug, so a slug taken from a
kitchenstories.com URL works directly.

A recipe carries `steps`, `ingredients`, `utensils`, `nutrition`, `servings`, `difficulty`,
`duration`, `author`, `video` and `howto_videos`.

### 5. Expand outward

- `recipe-recommendation-list` (`GET /recipes/{uuid-or-slug}/recommendations/`) for related
  recipes; `article-recommendation-list` and `video-recommendations` do the same for those types.
- `recipe-of-the-day` (`GET /recipe-of-the-day/`) for the editorial pick.
- `category-list` / `category-detail` to walk the category tree — a category returns both its
  materialized `path` and its `children`.
- `tag-list` for the flat tag vocabulary.

## Conventions and error handling

- **Caching.** Reads return an `ETag` and `Cache-Control: public, max-age=5400`. Send
  `If-None-Match` on repeat reads and honour `304`.
- **Language.** Content is multilingual. Pass the `language` query parameter or an
  `Accept-Language` header; responses `Vary` on it.
- **Errors** use a flat envelope, not RFC 9457: `{"detail": "<message>"}` under
  `application/vnd.ajns.kitchenstories+json`.
  - `401` — missing, invalid or expired token. Re-authenticate and retry once.
  - `403` — authenticated but not permitted.
  - `404` — no such UUID or slug. Do not retry; re-resolve from a list operation.
- **No idempotency contract and no documented rate limit.** No `Idempotency-Key` header exists
  anywhere in this API and no rate-limit headers are returned. Back off politely on your own
  schedule rather than relying on a signal that is not sent.

See `conventions/kitchenstories-conventions.yml` and `errors/kitchenstories-problem-types.yml`.
