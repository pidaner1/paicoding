# My Articles Page Design

## Goal

Add a native mini-program page where the signed-in user can view their own articles. The page has two TDesign tabs: `文章` for submitted/published work and `草稿` for unpublished drafts. Drafts can be submitted for publishing, but the mini-program does not create or edit article content in this iteration.

## Navigation And Page Structure

- Register `pages/my-articles/my-articles` in `src/app.json` with the title `我的文章`.
- Wire the existing `我的文章` shortcut on the profile page to open `/pages/my-articles/my-articles`.
- Use one page with TDesign `t-tabs` and two `t-tab-panel` panels: `文章` and `草稿`.
- Keep each tab's list in a full-width `scroll-view` so the scrollbar sits against the screen edge. Put horizontal spacing on the inner list wrapper.
- Keep each tab's paging, loading, refresh, and error state independent.
- Use a dedicated owner article row/card so status and draft publish actions can be shown consistently.

## Article State Rules

- Treat backend `OFFLINE = 0` as `草稿`.
- Treat backend `ONLINE = 1` as normal articles in the `文章` tab.
- Treat backend `REVIEW = 2` as articles in the `文章` tab with an `审核中` badge.
- Published articles open the public detail page by default.
- Published and review articles open the public detail page.
- Draft articles stay on the list and expose a `发布` action.
- The backend already sends non-whitelisted authors to `REVIEW` when they submit for publishing, so the mini-program should not invent a separate approval state.

## Draft Publishing

- Each draft card has a `发布` button.
- Tapping `发布` calls a backend publish endpoint with the draft article ID only.
- The backend loads the existing draft, validates ownership, rebuilds an `ArticlePostReq` from stored content, and sends `actionType = post`.
- The existing backend service decides whether the result becomes `ONLINE` or `REVIEW`.
- After success, show `已发布` or `已提交审核`, refresh the draft list, and refresh the article list if it has been loaded.
- Do not add mini-program article creation or editing in this iteration.

## Backend Contract

- Add authenticated `GET /mini/api/user/articles` with `type`, `page`, and `size`.
- Accept `article` and `draft` for `type`; reject other values as illegal arguments.
- Always derive the user ID from the authenticated request context.
- Add service/DAO support to query the current user's articles by status:
  - `article`: `ONLINE` and `REVIEW`
  - `draft`: `OFFLINE`
- Return `PageListVo<WxMiniArticleDTO>` with `list` and `hasMore`.
- Include article status and status text in the mini article DTO so the UI can display `审核中`.
- Add authenticated `POST /mini/api/user/articles/{articleId}/publish` for owner-only draft publishing.
- Reuse `ArticleWriteService.saveArticle` for publishing. Do not add new tables.
- Validate ownership and ensure only `OFFLINE` drafts can be published.

## Client Data Flow

- Load the default `文章` tab on entry and lazily load `草稿` on first tab selection.
- Support pull-to-refresh, lower-edge pagination, empty states, first-load failure retry, and end-of-list messaging.
- Use `auth.requestWithLogin` for all owner article list and edit requests.
- Normalize missing list data to an empty array and missing `hasMore` to false.
- Publishing a draft refreshes the draft tab and refreshes the article tab if it is already loaded.

## Failure Handling

- If a list request fails on first load, show a panel with retry.
- If a later page fails, keep already-loaded rows visible and expose retry through refresh or scrolling again.
- Prevent duplicate list loads and duplicate draft publish submissions.
- If login expires, rely on the shared auth/request layer.
- If an article is missing or not owned by the current user, show the backend error and keep the user on the list/editor safely.

## Scope

- Add the two-tab owner article list page.
- Add the profile navigation binding.
- Add the mini-program backend endpoints and minimal service/DAO support required for status-filtered owner lists and draft publishing.
- Preserve existing public article list, detail, collection, history, profile, and following behavior.
- Do not add create, edit, delete, upload, rich text editing, column management, paid reading configuration, or admin approval UI in this iteration.

## Verification

- Add focused client tests for tab loading, independent paging, refresh, empty/error states, review badge rendering, profile navigation, and draft publishing.
- Add backend tests for list type validation, status filtering, owner-only draft publishing, non-owner rejection, non-draft rejection, and publish service delegation.
- Run mini-program tests, lint for touched files, and `pnpm build`.
- Compile or test affected backend code with Java 8 and the repository's targeted Maven workflow; do not run `mvn clean`.
- If WeChat DevTools is logged in with service port enabled, capture the new page with `wv screenshot` for visual acceptance.
