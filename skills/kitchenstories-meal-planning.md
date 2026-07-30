---
name: kitchenstories-meal-planning
description: >-
  Build and maintain a Kitchen Stories meal plan for the authenticated user — schedule recipes on
  dates, read the plan back, update entries and remove them, informed by the user's dietary
  personalization preferences and favourite ingredients.
api: kitchenstories:internal-api
base_url: https://api.kitchenstories.io/api
generated: '2026-07-19'
method: generated
source: openapi/kitchenstories-internal-openapi.json
operations:
  - autenticate-credentials
  - user-me
  - mealplan-list
  - mealplan-create
  - mealplan-detail
  - mealplan-update
  - mealplan-delete
  - personalization-get
  - personalizationpreferences-get
  - personalizationoptions-get
  - personalizationoptions-values-get
  - personalization-feed-get
  - user-me-list-favorite-ingredients
  - user-favorite-ingredients-create
  - recipe-list
  - recipe-detail
  - ingredient-list
  - ingredient-units-list
---

# Planning meals on Kitchen Stories

Schedules recipes onto dates for the authenticated user, optionally shaped by their stored dietary
preferences.

## Before you start

1. Authenticate with `autenticate-credentials` (`POST /authenticate/credentials/`).
2. Send `Authorization: Bearer <access_token>` and
   `Accept: application/vnd.ajns.kitchenstories+json; version=3` on every call.
3. Confirm the acting identity with `user-me` (`GET /users/me/`).

## Steps

### 1. Read the user's preferences first

Do not pick recipes blind — the API stores what this user actually eats.

- `personalization-get` (`GET /personalization/`) for the personalization state.
- `personalizationpreferences-get` (`GET /personalization/preferences/`) for the selected
  preferences.
- `personalizationoptions-get` (`GET /personalization/options/`) and
  `personalizationoptions-values-get` (`GET /personalization/options/{uuid}/values/`) to resolve
  what each preference *means* — options and their permitted values are server-defined, so
  resolve them rather than hard-coding any dietary vocabulary.
- `user-me-list-favorite-ingredients` (`GET /users/me/favorite-ingredients/`) for ingredients the
  user has starred.

### 2. Choose recipes

- `personalization-feed-get` (`GET /personalization/feed/`) returns a feed already tailored to
  the user — prefer it over an unfiltered browse.
- Otherwise use `recipe-list` (`GET /recipes/`) with `tag`, `category` or `search`, then
  `recipe-detail` for `servings`, `duration`, `difficulty` and the full ingredient list.

### 3. Read the current plan

`mealplan-list` (`GET /users/me/mealplan/`) returns `data`, an array of meal-plan items. Pass the
`date` query parameter to scope to a window rather than pulling the whole plan.

### 4. Add, change and remove entries

- `mealplan-create` (`POST /users/me/mealplan/`) adds an entry. Keep the returned item UUID.
- `mealplan-detail` (`GET /users/me/mealplan/{mealplan-item-uuid}/`) reads one back.
- `mealplan-update` (`PUT /users/me/mealplan/{mealplan-item-uuid}/`) changes it — use this to
  move a meal to another date rather than deleting and re-creating.
- `mealplan-delete` (`DELETE /users/me/mealplan/{mealplan-item-uuid}/`) removes it.

### 5. Resolve quantities correctly

Ingredient amounts reference server-defined units. Resolve them with `ingredient-units-list`
(`GET /ingredients/units/`) and `ingredient-list` (`GET /ingredients/`) instead of parsing unit
strings out of recipe text.

## Conventions and error handling

- **No idempotency.** There is no `Idempotency-Key` header in this API. A retried
  `mealplan-create` after a network timeout will schedule the meal twice. Always re-read
  `mealplan-list` for the affected date before resending a create.
- **Prefer `mealplan-update` over delete-then-create** — it is a single call and cannot leave the
  plan in a half-modified state.
- **Trailing slashes are required** on all meal-plan paths.
- **Errors** are `{"detail": "<message>"}`:
  - `401` — token missing or expired; re-authenticate and retry once.
  - `403` — the meal-plan item belongs to another user; do not retry.
  - `404` — the item UUID does not exist; re-resolve from `mealplan-list`.
- **No documented rate limit.** No rate-limit headers are returned and no `429` is declared. When
  writing a week of meals, serialize the calls and pace them yourself.

See `conventions/kitchenstories-conventions.yml` and `errors/kitchenstories-problem-types.yml`.
