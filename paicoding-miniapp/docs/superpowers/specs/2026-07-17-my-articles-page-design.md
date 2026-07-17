# My Articles Page Design

## Goal

Add a native mini-program page where the signed-in user can manage their own articles. The page has two TDesign tabs: `文章` for submitted/published work and `草稿` for unpublished drafts. Drafts and review articles can be edited in the mini-program.

## Navigation And Page Structure

- Register `pages/my-articles/my-articles` in `src/app.json` with the title `我的文章`.
- Wire the existing `我的文章` shortcut on the profile page to open `/pages/my-articles/my-articles`.
- Use one page with TDesign `t-tabs` and two `t-tab-panel` panels: `文章` and `草稿`.
- Keep each tab's list in a full-width `scroll-view` so the scrollbar sits against the screen edge. Put horizontal spacing on the inner list wrapper.
- Keep each tab's paging, loading, refresh, and error state independent.
- Use the existing `article-card` for published articles when opening public detail is correct.
- Use a dedicated owner article row/card when the article needs owner-only status or edit actions.

## Article State Rules

- Treat backend `OFFLINE = 0` as `草稿`.
- Treat backend `ONLINE = 1` as normal articles in the `文章` tab.
- Treat backend `REVIEW = 2` as articles in the `文章` tab with an `审核中` badge.
- Published articles open the public detail page by default.
- Draft and review articles open the mini-program edit page.
- The backend already sends non-whitelisted authors to `REVIEW` when they submit for publishing, so the mini-program should not invent a separate approval state.

## Editing Experience

- Add `pages/article-edit/article-edit` as a native mini-program editor.
- The editor supports loading an existing owned article, editing fields, saving as draft, and submitting for publish.
- The initial field set is title, short title, summary, category, tags, cover URL, source type, source URL, read type, and Markdown content.
- Keep the first version focused on text fields and existing category/tag data; do not add image upload, rich Markdown toolbar, AI SEO generation, paid article configuration, or column selection.
- `保存草稿` sends `actionType = save`.
- `提交发布` sends `actionType = post`; the existing backend service decides whether it becomes `ONLINE` or `REVIEW`.
- After saving, show a toast and return to the list. The list reloads on show so status changes are reflected.

## Backend Contract

- Add authenticated `GET /mini/api/user/articles` with `type`, `page`, and `size`.
- Accept `article` and `draft` for `type`; reject other values as illegal arguments.
- Always derive the user ID from the authenticated request context.
- Add service/DAO support to query the current user's articles by status:
  - `article`: `ONLINE` and `REVIEW`
  - `draft`: `OFFLINE`
- Return `PageListVo<WxMiniArticleDTO>` with `list` and `hasMore`.
- Include article status and status text in the mini article DTO so the UI can display `审核中`.
- Add authenticated `GET /mini/api/user/articles/{articleId}/edit` for owner-only edit detail.
- Add authenticated `POST /mini/api/user/articles/save` accepting an article post request payload and returning the saved article ID.
- Reuse `ArticleWriteService.saveArticle` for save and submit. Do not add new tables.
- Validate ownership before returning edit details or updating an existing article.

## Client Data Flow

- Load the default `文章` tab on entry and lazily load `草稿` on first tab selection.
- Support pull-to-refresh, lower-edge pagination, empty states, first-load failure retry, and end-of-list messaging.
- Use `auth.requestWithLogin` for all owner article list and edit requests.
- Normalize missing list data to an empty array and missing `hasMore` to false.
- On `onShow`, refresh the active tab if the page is returning from the editor after a save.

## Failure Handling

- If a list request fails on first load, show a panel with retry.
- If a later page fails, keep already-loaded rows visible and expose retry through refresh or scrolling again.
- Prevent duplicate list loads and duplicate editor submissions.
- If login expires, rely on the shared auth/request layer.
- If an article is missing or not owned by the current user, show the backend error and keep the user on the list/editor safely.

## Scope

- Add the two-tab owner article list page.
- Add the native mini-program article editor page.
- Add the profile navigation binding.
- Add the mini-program backend endpoints and minimal service/DAO support required for status-filtered owner lists.
- Preserve existing public article list, detail, collection, history, profile, and following behavior.
- Do not add delete, upload, rich text editing, column management, paid reading configuration, or admin approval UI in this iteration.

## Verification

- Add focused client tests for tab loading, independent paging, refresh, empty/error states, draft edit navigation, review badge rendering, and profile navigation.
- Add client tests for editor load, save draft, submit publish, duplicate-submit prevention, and validation errors.
- Add backend tests for list type validation, status filtering, owner-only edit detail, owner-only update, and save/submit service delegation.
- Run mini-program tests, lint for touched files, and `pnpm build`.
- Compile or test affected backend code with Java 8 and the repository's targeted Maven workflow; do not run `mvn clean`.
- If WeChat DevTools is logged in with service port enabled, capture the new page with `wv screenshot` for visual acceptance.
