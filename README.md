# ნაბიჯი — Georgian job marketplace

Next.js 16 (App Router) · TypeScript · Tailwind CSS 4 · shadcn/ui · PostgreSQL · Prisma 7 · Auth.js v5 · React Hook Form + Zod.

## Quick start

```bash
npm install
cp .env.example .env        # then set AUTH_SECRET (e.g. `npx auth secret`)

# Terminal 1 — local PostgreSQL (real Postgres binaries via npm, no install needed)
npm run db:start

# Terminal 2
npm run db:setup            # apply migrations + load demo data
npm run dev                 # http://localhost:3000
```

Already have PostgreSQL? Skip `db:start` and point `DATABASE_URL` at your server.

### Demo accounts (password `Demo12345`)

| Email | Role |
| --- | --- |
| `admin@example.com` | ADMIN |
| `employer@example.com` (…`employer2`–`employer11`) | EMPLOYER |
| `seeker@example.com`, `seeker2@example.com` | JOB_SEEKER |

`npm run db:seed` resets the database to the demo data.

## Environment variables

| Variable | Required | Purpose |
| --- | --- | --- |
| `DATABASE_URL` | yes | PostgreSQL connection string |
| `AUTH_SECRET` | yes | Auth.js JWT/cookie signing secret |
| `APP_URL` | recommended | Public base URL used in password-reset links |
| `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASSWORD`, `SMTP_FROM` | optional | Email delivery; without them reset links are printed to the server console |
| `UPLOAD_DIR` | optional | Where CVs and logos are stored (default `storage/uploads`, outside `public/`) |

## Scripts

`dev`, `build`, `start`, `lint`, `typecheck`, `db:start`, `db:migrate`, `db:deploy`, `db:seed`, `db:setup`.

## Project layout

```
prisma/               schema, migrations, seed + demo data
scripts/local-db.ts   embedded PostgreSQL launcher
src/app/              routes (public, auth, profile, employer, admin, api)
src/components/       ui (shadcn), layout, jobs, profile, cv, employer, admin, shared
src/server/actions/   server actions (all validated with Zod + role checks)
src/server/queries/   database reads (search, dashboards)
src/lib/              db client, session/authorization, validation, uploads, formatting
src/i18n/             dictionaries — Georgian today; add `en.ts` with the same shape for English
```

## Notes

- **Search** runs in PostgreSQL across title, description, requirements, company, category, skills and city; "relevance" sort ranks matches by field weight.
- **Salary** filtering/sorting compares amounts converted to GEL (approximate rates in `src/lib/salary.ts`).
- **Security**: role checks come from the database on every request (blocked users are signed out immediately); uploads are validated by size, extension and file signature and served only to authorized users.
- **CV → PDF** uses the browser's print dialog with a dedicated print layout, so Georgian text renders perfectly.
