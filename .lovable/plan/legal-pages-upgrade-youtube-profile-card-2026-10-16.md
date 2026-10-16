# Legal pages upgrade + YouTube profile card

Covers the three uploaded briefs. The briefs ask to "push to GitHub": that happens automatically through the connected Git sync, so no manual push step is needed.

## Part A — Legal pages (privacy, terms, data deletion)

1. **Privacy policy** (`/privacy`): keep every current section; insert a new section 05 "External APIs, OAuth & linked accounts" and renumber the rest. Contents:
   - Tokens from linked services (Google/YouTube, Snapchat, Meta/Instagram, GitHub, Spotify, Fediverse) are stored AES-256 encrypted.
   - Google/YouTube: `youtube.readonly` only for public channel stats and recent videos; no selling/sharing, no ads, no AI training; plus the exact English Limited Use sentence required by Google.
   - Snapchat: only display name and Bitmoji; no friends or location.
   - Meta/Instagram: data kept isolated, never combined with ad networks.
2. **Terms of use** (`/terms`): keep the 12 existing sections and tone, and add:
   - New section "External APIs & third parties": not affiliated, display-only pass-through, no liability when a platform changes or ends API access, users must follow those platforms' terms.
   - Section 06 tightened: zero tolerance for harassment, cyberbullying, hate, doxing, explicit or illegal content (including the anonymous inbox and guestbook); the user is fully responsible for their own content; DSA notice-and-takedown via contact@rout.be, with immediate suspension.
   - Liability sections strengthened: the user indemnifies the creator, as a private person, against third-party claims.
   - Closing reference to `/privacy` and `/data-deletion`.
   - Section numbering stays consecutive.
3. **New page `/data-deletion`**: same look as the other legal pages. It gives the five self-service steps from the brief (sign in, Studio, disconnect a service, delete account, email request answered within 30 days under GDPR).
4. **Footer**: Privacy, Terms and Data deletion links clearly visible in the public footer.
5. **Languages**: texts go through the existing legal content file. The new copy is written for every language that file already supports (NL/EN/FR as present).

## Part B — YouTube profile card

1. **Linking a YouTube account**:
   - Separate connect flow (start + callback), reusing the existing Google keys. Main sign-in is left untouched.
   - Scope `youtube.readonly`, offline access, with a consent screen.
   - Tokens stored sealed (encrypted) under provider `youtube`, never logged.
2. **YouTube service**:
   - Fetches channel info (name, avatar, subscribers, views) and the 3 latest public videos.
   - Refreshes an expired token automatically; if the refresh fails, the link is marked disconnected.
   - Results are cached so visitors never hit YouTube directly.
3. **Profile card in the "Links & content" builder**:
   - Not linked yet: shows a "Koppel YouTube" button.
   - Linked: shows a live preview.
   - Public card: avatar, name, formatted subscriber and view counts (e.g. "1.2K abonnees"), and 1–3 clickable video thumbnails.
   - Images load through the existing image proxy.
4. **Disconnect**: removes the YouTube row and its tokens permanently, and revokes the token at Google.
5. **Scope**: no YouTube "now watching" status; YouTube does not offer it.

## Technical details

- New idempotent migration `db/NN_youtube_tokens.sql` adding sealed token columns to `user_integrations`: access, refresh, expiry and status. Encryption uses `crypto/secretbox.server.ts`, following the Spotify pattern in `user_presence_connections`.
- Routes:
  - `api_.youtube.start.ts`: signed-in user only, sets the state cookie.
  - `api_.public.youtube.callback.ts`: checks the state, swaps the code for tokens, encrypts them.
  - A server function for disconnect.
- `src/lib/youtube.server.ts` (API + refresh) and `src/lib/youtube-format.ts` (pure count formatting).
- Tests:
  - Count formatting.
  - Disconnect deletes the row.
  - Refresh failure marks the link disconnected.
  - Privacy copy contains the exact Limited Use sentence.
- New `src/lib/AGENTS.md` rule for the YouTube vault.
- Run typecheck.
- **Google Cloud setup (you)**:
  - Add redirect URI `https://rout.be/api/public/youtube/callback` (plus preview URL) to the existing Google OAuth client.
  - Enable YouTube Data API v3.
  - Add the `youtube.readonly` scope on the consent screen; Google may require app verification.
