# Technical plan: Bookshelf (sample)

Status: agreed
Last updated: 2026-01-15

> A filled-in sample of [the plan template](../assets/technical-plan-template.md) for a small invented product. It shows the level of detail expected; it is not a requirement for your project. Versions shown are placeholders: record the real ones from your manifests.

## Product and first release
**Problem:** A small community library tracks loans on paper and loses books.
**Users:** Librarians (staff) and members.
**First release:** (O1) members browse and search the catalogue; (O2) librarians add, edit, and retire books; (O3) librarians check a book out to a member and check it back in; (O4) members see their own loans.
**Excluded:** online payments, fines, email reminders, mobile app, imports from the old paper records.

## Users and workflows
Roles: `MEMBER`, `LIBRARIAN`. Visitors can browse the catalogue only.
- Librarian checks out: find member, find book, confirm; the book becomes unavailable.
- Member views "My loans" (own records only).
- Open decision: loan period length. Owner: library manager. Blocks: S5 (due dates). Assumption: 14 days, configurable.

## Languages and content
Locales: Arabic (`ar`, RTL, default) and English (`en`, LTR). URL strategy: `/ar/...` and `/en/...`. Interface strings in dictionaries; book titles and authors are stored once as entered (not translated). Missing interface translation falls back to English and is logged in development. Arabic digits: Western digits in both locales. Gregorian calendar. SEO: catalogue pages localized with `hreflang`.

## Architecture
Separate apps: Next.js web (App Router) and NestJS API, in an npm-workspaces monorepo, because a mobile client is likely later. No shared package yet; the only shared rules are DTO shapes, generated from the API's OpenAPI spec if needed later.

## Scaffold decisions
Layout: `apps/web`, `apps/api`, `docs/`. npm, Node LTS. Test tools: Jest (API), Vitest + Playwright (web). First runnable bilingual flow: `/ar` and `/en` starter pages with a language switch preserving the route. Baseline commands: `npm run lint`, `npm run typecheck`, `npm test`, `npm run build`.

## Used packages
| Package | Version | Purpose | Reason |
| --- | --- | --- | --- |
| next, react | per generator | web app | App Router, server rendering |
| @nestjs/* | per generator | API | modular structure, guards |
| prisma, @prisma/client | latest stable at scaffold | ORM | migrations, typed queries |
| class-validator, class-transformer | latest stable | DTO validation | NestJS `ValidationPipe` |
| zod | latest stable | web form validation | server actions and forms |
| @nestjs/throttler, helmet | latest stable | abuse and headers | security checklist |
| argon2 | latest stable | password hashing | slow hash |

## Documentation baseline
Prisma: use the migration procedure for the installed major version (CLI docs). Next.js: locale routing per the internationalization guide for the installed version.

## Data and integrations
Entities: `User(id, email, passwordHash, role)`, `Book(id, title, author, isbn?, status)`, `Loan(id, bookId, memberId, checkedOutAt, dueAt, returnedAt?)`.
Constraints: one open loan per book (partial unique index on `bookId` where `returnedAt` is null). Indexes: `Loan(memberId)`, `Book(title)`. Books are retired with `status = RETIRED`, not deleted (loan history must survive). No external systems.

## API
REST under `/api/v1`: `GET /books`, `POST /books` (librarian), `PATCH /books/:id` (librarian), `POST /loans` (librarian), `POST /loans/:id/return` (librarian), `GET /me/loans` (member). Errors: `{ code, message }` with stable codes such as `BOOK_UNAVAILABLE`; the web app localizes by code.

## Security measures
- Data sensitivity: member emails and loan history (personal data).
- AuthN: email + password; argon2id; short-lived JWT in an HttpOnly, Secure, SameSite=Lax cookie with refresh rotation. Enforced in `AuthModule`; verified by auth e2e tests. Step: S2.
- AuthZ: global guard, `@Public()` on catalogue reads; role guard for librarian routes; `GET /me/loans` filters by the caller's ID. Verified with a second-member denial test. Step: S2, S5.
- Validation: `ValidationPipe` with whitelist; Zod on web forms. Step: every step.
- Secrets: `.env` gitignored, validated at startup. Step: S1.
- CORS allowlist, helmet, CSP in report-only first. Step: S1.
- Rate limits: login and signup (stricter). Step: S2.
- Logging: structured, no tokens or bodies. Step: S1.
- Backups: nightly `pg_dump`, restore tested before launch. Step: S7.

## Operations and verification
Environments: local, production (no staging; accepted risk, revisit if traffic grows). Single VPS with Docker Compose, one API replica (in-memory throttler is acceptable). Rollback: redeploy the previous image; migrations are additive (expand/contract for destructive changes). Gates: `npm run lint`, `npx tsc --noEmit`, `npm test`, `npm run build`, `npx prisma migrate status`, `npm audit --omit=dev`.

## Decisions and risks
- D1: Separate API and web, because a mobile app is planned. Reversible at moderate cost.
- D2: Retire rather than delete books, to keep loan history.
- A1 (assumption): 14-day loan period. Owner: library manager. Blocks S5.
- R1: Arabic copy not yet reviewed by a fluent speaker. Mitigation: review before S7.

## Requirement coverage
| ID | Outcome or control | Step | Acceptance evidence |
| --- | --- | --- | --- |
| O1 | Browse and search catalogue | S3 | e2e in both locales; search test |
| O2 | Manage books | S4 | role-guard tests; Playwright flow |
| O3 | Check out and return | S5 | one-open-loan constraint test; race test |
| O4 | Member sees own loans | S5 | second-member denial test |
| SEC1 | Auth, roles, rate limits | S2 | e2e auth and denial tests |
| SEC2 | Backups and restore | S7 | restore log |

## Development steps
| ID | Outcome | Depends on | Excluded | Evidence | Status |
| --- | --- | --- | --- | --- | --- |
| S1 | Base scaffold: both apps run, `/ar` and `/en` render with correct `lang`/`dir`, config and headers in place | none | domain tables | commands in "Scaffold decisions" pass; both pages load | done |
| S2 | Authentication, roles, rate limits | S1 | password reset email | SEC1 tests | in progress |
| S3 | Catalogue browse and search (read-only) | S1 | editing | O1 evidence | todo |
| S4 | Librarian manages books | S2, S3 | bulk import | O2 evidence | todo |
| S5 | Loans: check out, return, my loans | S2, S4, A1 | fines, reminders | O3, O4 evidence | todo |
| S6 | Polish: empty and error states, a11y pass | S3-S5 | new features | axe clean; keyboard pass | todo |
| S7 | Deploy and backups | S6 | autoscaling | deployed smoke test; restore log | todo |

## Current state
S1 verified on 2026-01-14 (lint, typecheck, build, and a Playwright smoke check pass in `ar` and `en`). S2 is in progress (login done, role guards pending). Next: finish S2. Blocking decision: loan period (A1) before S5.
