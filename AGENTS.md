# Server agent guide

This directory is the Express + TypeScript/Mongoose backend for the calisthenics course booking MVP. Read `README.md` for setup and the exact route list. `../client/` is a separate Git repository and contains the UI demo.

## Current contract

- `src/app.ts` mounts `/api/auth` and `/api/status`. Startup connects to MongoDB and runs the optional admin bootstrap before listening.
- Auth persists accounts only. There are no booking, plan purchase, payment, balance, trainer schedule, or skill-progress models/routes yet. Do not imply those client demos are backed by this API.
- `POST /api/auth/register` creates a `user`. `POST /api/auth/login` requires a matching `user`, `trainer`, or `admin` portal. `GET /api/auth/me` refreshes account information and a token. Admin-only trainer creation uses `protect` then `restrictTo("admin")`.
- Passwords are hashed by the user model's pre-save hook. Preserve that behavior for all account creation and password changes; never return password hashes.
- The database name is explicitly `Untitle` in `src/config/db.ts`. Coordinate a database migration before changing it.

## Data and secrets

- Never commit `.env`, real credentials, token values, production connection strings, or customer health/payment information. Document environment variable **names** only.
- Admin bootstrap can change an existing admin account and, with `ADMIN_ENFORCE_SINGLE_ADMIN=true`, demote other admins. Do not run it against a real database casually.
- The public status endpoints expose runtime and database metadata. Reassess access before production use.
- The current `cors()` call allows broad browser origins. Restrict it when deployment origins are known.

## Before finishing a change

Run `npm run build` from this directory. For route changes, verify authorization and response behavior with a disposable MongoDB database, including wrong-role and expired-token cases. Keep the server README route table and the client API expectations in sync. Use `npm ci` to reproduce the lockfile.
