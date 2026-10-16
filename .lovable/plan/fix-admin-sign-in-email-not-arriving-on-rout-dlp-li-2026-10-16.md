# Fix: admin sign-in email not arriving on rout.dlp.li

## What I found in the code
- rout.dlp.li is already allowed as a trusted website for login, so that is not the blocker.
- On the admin portal, the email is checked against the owner list (`OWNER_EMAILS`, falls back to `hallo@rout.be`) or an admin role in the database **before** any mail is sent. If the database lookup fails on the admin deployment, it silently refuses.
- The mail itself is sent "fire-and-forget": if Brevo refuses it, sign-in still says "sent" and the error only lands in server logs. That's why you see nothing.
- I cannot read your Vercel logs from here (no Vercel connection). So the plan makes the real reason visible instead of guessing.

## Steps
1. Open the secure form for the Brevo key (as you asked), so the template upload can also run.
2. Add a protected admin diagnostic check (`/api/admin-mail-check`, owner-only) that reports, without showing secrets: is the email key present, does the database answer, is your email recognized as admin, which sender/web address is used, and does a real test mail to you get accepted by Brevo (with Brevo's exact error if not).
3. Make failures visible on the admin sign-in screen: when the admin check refuses or the database is unreachable, show a clear message ("not recognized as admin" / "database unreachable") instead of silence. Brevo errors get logged as `[Brevo API Error]` with status and reason, and on the admin portal only, the send is awaited so a refusal returns an error to the screen.
4. Log `[ALTCHA Error]` with reason when the bot check fails on the admin portal.
5. Add tests: refusal reasons, Brevo error is surfaced on admin mode, public mode stays non-blocking.
6. Typecheck; changes reach GitHub automatically via Git sync.

## What you do afterwards
- Redeploy rout.dlp.li, try to sign in, and read the message on screen (or open the diagnostic page). Most likely causes it will reveal: `OWNER_EMAILS`/database variable missing in the admin Vercel project, or the Brevo sender not allowed.

## Technical details
- Files: `src/routes/api_.auth.$.ts` (admin refusal reasons, ALTCHA logging), `src/lib/auth/owner-admin.server.ts` (return reason, not bool), `src/lib/email.server.ts` (await + rethrow in admin mode), `src/emails/send.server.ts` (`[Brevo API Error]` log with status/body), new `src/routes/api_.admin-mail-check.ts` guarded by `assertAdminRole`/owner email, admin sign-in UI error mapping, `ENVIRONMENT.md` note.
- Public mode keeps non-blocking dispatch (architecture rule preserved).
