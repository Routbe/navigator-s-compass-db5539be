# QR saving, QR in Share, and live integrations as real profile components

## 1. QR code designer: real "Opslaan" button
- The QR designer saves the colors, corners and logo in the background with a short delay. The "Beheer profielhub" button next to it only changes which @handle the QR points to.
- First check: reproduce in the browser why a design doesn't stick (lost background save, draft overwriting it, or saving the wrong account) and fix that cause.
- Replace the "Beheer profielhub" button in the QR card with **Opslaan**. It saves right away and shows "Opgeslagen ✓" or a clear error. A small link to manage the profile hub stays available.

## 2. QR code in the Share menu (public profile)
- The Share menu now shows your styled QR (your saved colors, corners and logo) as a small preview.
- Tapping it opens it full screen on phone and computer, sized to the screen with a close button and a download button.
- The downloaded QR and the business card use the same style.

## 3. Adding a live integration = a real component
- Right now, picking "YouTube Kaart" only scrolls the page. Nothing is added, so it never shows in your profile hub. Fix: every live integration you add (YouTube, GitHub, chess.com, Lichess, Mastodon, Bluesky, WakaTime, RSS, Spotify) becomes a real component. It shows in your list of added components, can be moved up or down, and shows on your profile in that spot.
- Two-step connect:
  1. **Username first** (no reading your account). This is enough for chess.com, Lichess, GitHub, GitLab, Mastodon, Bluesky, RSS and WakaTime, because their public info, including online status, is open.
  2. **Officially connect / verify** afterwards, offered as a button. It's required only where the live part needs it: YouTube's subscriber count and video stats, and Spotify's "now playing". Until then the component shows the public info (YouTube: channel name and latest videos).

## 4. Compact, expandable component rows
- Added components use the same compact row as the integrations list: icon, name, account, status badge, and an arrow to expand.
- Expanded:
  - Show on profile: on/off
  - Verify / connect officially (only if just linked by username)
  - Disconnect (only if connected)
  - Trash can: remove the component
  - Stats to show, per platform (switches)

## 5. Stats you can choose per platform
```text
YouTube    subscribers*, total views*, video count*, latest videos (1-3)
GitHub     repos, followers, last push / commit, "active today"
GitLab     last commit, "active today"
chess.com  blitz rating, rapid rating, online status, challenge button
Lichess    blitz rating, rapid rating, online status, challenge button
Mastodon   followers, bio, latest activity
Bluesky    followers, bio, latest activity
WakaTime   top language, daily average
RSS        latest posts (1-3)
Spotify    now playing*
* only after connecting officially
```
Twitch stays "coming soon" because there's no data source for it yet.

## Technical details
- QR: fix the cause found in `ProfileQrCard` / `saveQrSettingsFn` (`display_prefs.qrSettings`); explicit save next to the existing debounce; `ProfileHubPicker` button becomes Opslaan.
- Share: `ShareMenu` renders a styled QR from public `qrSettings` (returned by the public profile function), thumbnail plus fullscreen dialog; exports reuse it.
- Components: new block kind `widget:integration` with `{ provider, integrationId, stats: string[], visible }` in `profile-section-blocks.ts`; `AddComponentModal` creates the block (plus the integration row via the existing `saveIntegration`) instead of `onWidget` scrolling; YouTube's OAuth card is attached to the block, and the unconditional `ProfileYouTubeCard` in `ProfileView` is removed so it renders only via its block (no duplicates).
- `ProfileLinksManager` renders integration blocks with the `LiveIntegrations` `Row` pattern; the stat allowlist per provider lives in `integrations-catalog.ts` and is filtered server-side in the public profile output (privacy rule).
- `youtube.server.ts` adds video count from the channel statistics; tests cover the per-provider stat allowlist, block add/remove, hidden blocks not rendering, and QR save.
