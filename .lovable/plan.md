# New email design for every email + no more "Promotions"

## 1. Stop login emails being treated as newsletters
- Every email sent to Brevo is explicitly marked as a transactional email:
  - `tags: ["transactional"]` on every email.
  - Login, password reset and security-code emails also get the `"security"` tag.
  - Existing tags such as `mailer-fallback-alert` are kept alongside these.
- Add `"X-Mailin-Tag": "transactional"` next to the existing headers that switch tracking off.
- Check the shared design, every email text and the stored templates for unsubscribe links or placeholders. The first scan found none; a test will keep it that way.
- **For you, in the Brevo dashboard:** Brevo can also add an unsubscribe header through an account setting. If the "Unsubscribe" notice still shows after this change, check that setting in Brevo; I'll point you to it.

## 2. Your example design becomes the one design for all emails
Your 9 example emails become the only design. Every email gets:
- The same header: bunny logo, "ROUT · Sovereign Infrastructure", and a colored status label (e.g. RENEWAL, CANCELED, PAID, SECURITY).
- The same title, greeting and summary box.
- A "desk" signature that fits the email (Billing Desk, Security Desk, Support Desk, …).
- The same footer: social icons, rout.be · Privacy · Terms · Manifesto, and © 2026 ROUT.

## 3. A matching email for every email type
- Each of the 22 email types gets its own complete email, with the right label, color, subject, preview text and full content. Examples:
  - Login code, magic link, password reset, email change
  - Payment received, payment issue, renewal, cancellation, refund
  - Contact form received, booking, admin alerts, and the rest
- Written for all 9 languages the app supports (NL, EN, FR, DE, ES, IT, PT, PL, ZH), starting from your example texts.
- Mail clients that hide images still show the content correctly. The emails also hold up in Outlook and on mobile.

## 4. Getting them into Brevo
- The ready-made template files are regenerated from this one design, so files and code always match.
- I'll add an upload step that pushes these templates to Brevo under the existing template numbers. It needs your Brevo key, which I'll ask for securely if it isn't stored yet.
- After that, a test login email can show whether the "Unsubscribe" notice is gone.

## Technical details
- Files:
  - `src/emails/design/layout.ts`: rebuilt to match the reference markup.
  - `src/emails/design/mails.ts` and `copy.ts`: a kind per catalog category, with accent, badge and desk.
  - `src/emails/send.server.ts`: tags and header.
  - New `scripts/sync-brevo-templates.ts` (already referenced in comments, file missing). It sends a PUT to `/v3/smtp/templates/{id}` for each category × language ID from `template-ids.ts`.
- Tests:
  - Every catalog category renders in every language without leftover `{name}`-style keys.
  - No rendered HTML contains `unsubscribe`, `[UNSUBSCRIBE]` or `{{ unsubscribe }}`.
  - The send payload always carries the `transactional` tag and the `X-Mailin-Tag` header.
- Run typecheck. Changes reach GitHub automatically through the sync.
