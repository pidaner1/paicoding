# Profile Edit Page Design

## Goal

Add a dedicated native mini-program page for editing the current user's avatar, nickname, and profile. The page must support WeChat's native avatar and nickname quick-selection capabilities while reusing the existing backend APIs.

## Page Structure

- Register `pages/profile-edit/profile-edit` in `src/app.json`.
- Navigate to it from the edit icon on `pages/profile/profile`.
- Show the current avatar at the top of the page.
- Use a `button` with `open-type="chooseAvatar"` for WeChat avatar selection.
- Use an `input` with `type="nickname"` for WeChat nickname quick fill and manual editing.
- Use a `textarea` for comfortable profile editing, limited to 225 characters. The current backend contract does not accept line breaks.
- Provide one primary save button for nickname and profile changes.

## Data Flow

- On page load, initialize from `auth.getStoredUser()` only. Do not make an additional `/mini/api/user/me` request because the profile page has already refreshed the current user before navigation.
- When an avatar is selected, upload it immediately with `auth.uploadWithLogin('/mini/api/user/avatar', ...)`.
- After a successful avatar upload, update page state and persist the returned user through `auth.persistUser()`.
- When Save is tapped, submit nickname and profile with `auth.requestWithLogin()` to `POST /mini/api/user/profile`.
- Persist the returned user, show a success toast, and navigate back. The profile page's existing `onShow` flow reloads the displayed user and statistics.

## Validation And Failure Handling

- Reject an empty nickname.
- Reject nicknames longer than 50 characters or containing newline, carriage-return, or tab characters.
- Reject profiles longer than 225 characters or containing newline, carriage-return, or tab characters, matching the existing backend contract.
- Prevent duplicate avatar uploads and duplicate save requests.
- Restore the previous avatar when upload fails.
- Keep the user on the edit page when save fails and show the returned error message.
- If no stored user exists, allow the authenticated upload/save helpers to establish login when the user performs an action.

## Scope

- Move profile editing handlers and state out of `pages/profile/profile.js` where they no longer have a visible form.
- Keep profile statistics, logout, privacy, collections, and history behavior unchanged.
- Do not add or change backend endpoints.
- Match the existing profile page's light background, white surfaces, restrained radius, and teal action color.

## Verification

- Add focused tests for stored-user initialization, avatar upload success/failure, nickname/profile validation, save success, and duplicate-submit prevention.
- Run the mini-program build.
- Use the project's WeChat DevTools screenshot command to inspect the edit page on a common mobile viewport when DevTools login and service-port access are available.
