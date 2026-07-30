---
name: kitchenstories-cookbook-management
description: >-
  Create and curate a Kitchen Stories user cookbook — add saved recipes, articles and external
  recipes, reorder and remove items, and read another user's public cookbooks.
api: kitchenstories:internal-api
base_url: https://api.kitchenstories.io/api
generated: '2026-07-19'
method: generated
source: openapi/kitchenstories-internal-openapi.json
operations:
  - autenticate-credentials
  - user-me
  - cookbook-list
  - cookbook-create
  - cookbook-detail
  - cookbook-update
  - cookbook-delete
  - cookbook-items-list
  - cookbook-item-create
  - cookbook-item-move
  - cookbook-item-delete
  - cookbook-get-recipes-list
  - cookbook-create-ecipes-list
  - cookbook-recipe-move
  - cookbook-recipe-delete
  - cookbook-external-recipes-create
  - external-recipes-create
  - external-recipes-list
  - external-recipes-get
  - external-recipes-delete
  - user-recipe-external-preview
  - public-cookbook-list
  - public-cookbook-detail
  - public-cookbook-items-list
  - public-cookbook-recipes-list
---

# Managing Kitchen Stories cookbooks

A cookbook is a user-curated collection of recipes and articles. All operations here are
user-scoped and require an authenticated bearer token.

## Before you start

1. Authenticate with `autenticate-credentials` (`POST /authenticate/credentials/`) and keep the
   `access_token`.
2. Send `Authorization: Bearer <access_token>` and
   `Accept: application/vnd.ajns.kitchenstories+json; version=3` on every call.
3. Confirm the acting identity with `user-me` (`GET /users/me/`).

## Steps

### 1. List or create a cookbook

- `cookbook-list` (`GET /users/me/cookbooks/`) returns the caller's cookbooks with
  `recipes_count`, `articles_count` and `items_count`.
- `cookbook-create` (`POST /users/me/cookbooks/`) creates one. Keep the returned `id` — every
  subsequent call is scoped by `{cookbook-uuid}`.
- `cookbook-update` (`PUT`) renames it; `cookbook-delete` (`DELETE`) removes it.

### 2. Add content

Two parallel item surfaces exist and they are not interchangeable — pick by content type:

- **Mixed items (recipes *and* articles):** `cookbook-item-create`
  (`POST /users/me/cookbooks/{cookbook-uuid}/items/`), read back with `cookbook-items-list`.
- **Recipes only:** `cookbook-create-ecipes-list`
  (`POST /users/me/cookbooks/{cookbook-uuid}/recipes/`), read back with
  `cookbook-get-recipes-list`.

Prefer the **items** surface for new work; it is the superset.

### 3. Add a recipe from outside Kitchen Stories

1. Preview the target URL with `user-recipe-external-preview`
   (`GET /users/me/external-recipe-preview/`) to see what the API can parse from it.
2. Either register it with `external-recipes-create` (`POST /users/me/external-recipes/`), or
   add it straight into the cookbook with `cookbook-external-recipes-create`
   (`POST /users/me/cookbooks/{cookbook-uuid}/external-items/`).
3. Manage them later with `external-recipes-list`, `external-recipes-get` and
   `external-recipes-delete`.

### 4. Reorder and remove

- Move an item: `cookbook-item-move`
  (`PUT /users/me/cookbooks/{cookbook-uuid}/items/{item-uuid}/`), or `cookbook-recipe-move` on
  the recipes surface.
- Remove an item: `cookbook-item-delete` (`DELETE`), or `cookbook-recipe-delete`.

### 5. Read someone else's public cookbooks

`public-cookbook-list` (`GET /users/{uuid-or-slug}/cookbooks/`), then `public-cookbook-detail`,
`public-cookbook-items-list` or `public-cookbook-recipes-list`. These are read-only and scoped to
the target user's public collections.

## Conventions and error handling

- **Writes are not idempotent.** This API defines no `Idempotency-Key` header on any operation.
  A retried `cookbook-item-create` after a timeout can duplicate the entry. Before retrying a
  write, re-read `cookbook-items-list` and only resend if the item is genuinely absent.
- **Pagination** on the list operations is page-number based: read `data`, follow `links.next`,
  and use `meta.pagination.count` for totals.
- **Trailing slashes are required** on all of these paths.
- **Errors** are `{"detail": "<message>"}`:
  - `401` — token missing or expired; re-authenticate and retry once.
  - `403` — the cookbook or item belongs to another user. Do not retry; this is an ownership
    failure, not a transient one.
  - `404` — the cookbook or item UUID does not exist. Re-resolve from `cookbook-list`.

See `conventions/kitchenstories-conventions.yml` and `errors/kitchenstories-problem-types.yml`.
