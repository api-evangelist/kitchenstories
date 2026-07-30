---
name: kitchenstories-community-engagement
description: >-
  Participate in the Kitchen Stories community as the authenticated user — comment on feed items,
  attach comment images, like and unlike content and comments, rate recipes, and report abuse.
api: kitchenstories:internal-api
base_url: https://api.kitchenstories.io/api
generated: '2026-07-19'
method: generated
source: openapi/kitchenstories-internal-openapi.json
operations:
  - autenticate-credentials
  - user-me
  - user-public-detail
  - other-feeditem
  - comment-list
  - comment-create
  - comment-images-list
  - comment-image
  - likes-list
  - like-create
  - like-delete
  - likes-comment-list
  - like-comment-create
  - like-comment-delete
  - rating-list
  - rating-create
  - rating-update
  - abuse-create
  - upload-image
---

# Engaging with the Kitchen Stories community

Comments, likes and ratings on Kitchen Stories content, acting as the authenticated user.

> **The unit of engagement is the feed item, not the recipe.** Recipes, articles and videos are
> all feed items and share one identifier space. Likes and ratings attach to a `feeditem-uuid`,
> not to a recipe UUID. Resolve the feed item with `other-feeditem`
> (`GET /feed-item/{uuid-or-pk}/`) before liking or rating anything.

## Before you start

1. Authenticate with `autenticate-credentials` (`POST /authenticate/credentials/`).
2. Send `Authorization: Bearer <access_token>` and
   `Accept: application/vnd.ajns.kitchenstories+json; version=3` on every call.
3. Confirm the acting identity with `user-me` (`GET /users/me/`).

## Steps

### 1. Read the conversation

`comment-list` (`GET /users/comments/`) returns comments, filterable by `feed_item`. Each comment
carries `author`, `comment`, `like_count`, `reply_count`, `recent_answers`, `images`, and both
`original_comment` and `original_language_code` — the community is multilingual, so a comment may
have been translated. Show the original when language matters.

### 2. Post a comment

`comment-create` (`POST /users/comments/`), scoped to a feed item.

To attach a picture, post the comment first, then call `comment-image`
(`POST /users/comments/{comment-uuid}/images/`) with the returned comment UUID. Read images back
with `comment-images-list`. General media goes through `upload-image` (`POST /upload/image/`).

### 3. Like and unlike

- Content: `like-create` (`POST /users/me/likes/feed-items/`) and `like-delete`
  (`DELETE /users/me/likes/feed-items/{feeditem-uuid}/`); read with `likes-list`
  (`GET /users/me/likes/feed-items/`).
- Comments: `like-comment-create` (`POST /users/me/likes/comments/`), `like-comment-delete`
  (`DELETE /users/me/likes/comments/{comment-uuid}/`), read with `likes-comment-list`.

**Do not use `likes-list-old` (`GET /users/me/likes/`).** It is superseded by the
`/users/me/likes/feed-items/` surface. It carries no formal deprecation marker and Kitchen
Stories publishes no deprecation policy, so treat it as legacy but do not assume a removal date.

### 4. Rate a recipe

- `rating-create` (`POST /users/me/rating/`) to rate for the first time.
- `rating-update` (`PUT /users/me/rating/{feeditem-uuid}/`) to change an existing rating.
- `rating-list` (`GET /users/me/rating/`) to read the user's ratings.

Check `rating-list` first: a user has at most one rating per feed item, so a second
`rating-create` is a conflict, not a second vote. Branch to `rating-update` when a rating already
exists.

### 5. Report abuse

`abuse-create` (`POST /report/abuse/`) files a report. Moderation itself happens on the QA
surface (`qa-ugc-accept` / `qa-ugc-reject`), which requires an internal moderator role — do not
call it as an ordinary user.

## Conventions and error handling

- **No idempotency.** There is no `Idempotency-Key` header in this API. A retried
  `comment-create` will post the comment twice, and a retried `like-create` may error. Before
  retrying any write here, re-read the corresponding list operation and only resend if the effect
  is genuinely missing.
- **Likes are the safest retry** — re-reading `likes-list` cheaply confirms state. Comments are
  the most dangerous; never blind-retry one.
- **Pagination** is page-number based with the `links` / `meta.pagination` envelope.
- **Errors** are `{"detail": "<message>"}`:
  - `401` — token missing or expired; re-authenticate and retry once.
  - `403` — not permitted: editing another user's comment, or calling a QA operation without the
    moderator role. Do not retry.
  - `404` — the feed item, comment or rating UUID does not exist.
- **No documented rate limit.** No rate-limit headers are returned and no `429` is declared. Pace
  bulk engagement yourself.

See `conventions/kitchenstories-conventions.yml` and `errors/kitchenstories-problem-types.yml`.
