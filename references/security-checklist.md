# Security checklist

Walk the sections the change touches, when implementing or reviewing. Each item is checkable: tick it, or state why it does not apply. Explain consequential omissions in the plan or PR.

## Authentication and authorization

- [ ] Routes are protected by default (global guard such as `APP_GUARD`); public routes opt out explicitly (for example `@Public()`).
- [ ] Every read or write by ID checks ownership or tenant in the query (`where: { id, ownerId }`), not just that the user is logged in. A test uses a second user to prove access is denied.
- [ ] Role guards cover each role; tests cover unauthenticated, wrong role, and right role.
- [ ] Request bodies are never spread into Prisma (`data: dto`) without a whitelist, so a client cannot set `role`, `ownerId`, or similar fields.
- [ ] Passwords use a slow hash (argon2id or bcrypt) with a sensible cost; recovery flows do not reveal whether an account exists; login and reset are throttled.
- [ ] JWT, if used: algorithm pinned, secret or key from configuration, short-lived access token, rotating refresh token, `exp` enforced, and a revocation path when credentials or privileges change.
- [ ] Tokens live in `HttpOnly`, `Secure`, `SameSite` cookies rather than `localStorage`. Cookie-authenticated writes have CSRF protection.
- [ ] Logout invalidates server-side state; sessions change on privilege change.

## Input and output

- [ ] NestJS: `ValidationPipe({ whitelist: true, forbidNonWhitelisted: true, transform: true })` and class-validator DTOs on every body, query, and param.
- [ ] Next.js: Zod (or equivalent) validates server actions, route handlers, and form data. Share schemas with the API where possible, but never trust client validation alone.
- [ ] Database constraints enforce durable invariants (unique, FK, check) in addition to validation.
- [ ] No raw SQL built from strings; `$queryRaw` uses tagged templates or parameters.
- [ ] User-supplied HTML is sanitized, and `dangerouslySetInnerHTML` is absent or reviewed.
- [ ] Uploads: size limit, extension and MIME allowlist, magic-byte check, random server-side filenames, stored outside the web root.
- [ ] Server-side fetches to user-supplied URLs block private and link-local addresses.

## Transport, headers, and abuse

- [ ] Rate limiting (for example `@nestjs/throttler`) on login, reset, OTP, signup, and upload, with a stricter limit on auth routes. Trusted-proxy settings match the real network path.
- [ ] CORS uses an explicit origin allowlist; `*` is never combined with credentials.
- [ ] `helmet` (or equivalent) is on in the API. The Next.js app sets CSP (test in report-only mode first), `X-Content-Type-Options`, `Referrer-Policy`, `frame-ancestors`, and HSTS in production.

## Secrets and configuration

- [ ] `.env*` files are gitignored; `.env.example` holds names and placeholders only.
- [ ] Environment variables are validated at startup (a schema in `@nestjs/config` or Zod) and the app fails fast when one is missing.
- [ ] No secret in source, client bundles (`NEXT_PUBLIC_*` holds public values only), logs, error responses, or CI output. A secret scan runs before commit or in CI.
- [ ] Stored third-party credentials are encrypted; read APIs return only set or masked status.
- [ ] Secrets can be rotated without code changes.

## Errors and logging

- [ ] A global exception filter returns one stable error shape with codes; stack traces and raw Prisma or driver errors never reach the client.
- [ ] Logs are structured, carry a correlation ID, and exclude passwords, tokens, personal content, and request bodies.
- [ ] 404 vs 403 follows the disclosure policy (do not reveal that a private resource exists).

## Prisma and PostgreSQL

- [ ] Migrations are forward-only and applied with `prisma migrate deploy` in deployments; an applied migration is never edited.
- [ ] Read the generated SQL for destructive operations (drop, rename, type change, new NOT NULL without default). Use expand/contract (add, backfill, switch, drop in a later step) for those.
- [ ] Back up before a risky migration, and write the rollback in the step spec.
- [ ] Related writes use `$transaction`; unique-violation and race conditions are handled explicitly.
- [ ] No N+1: use `include` or batched reads. Check query logging on list endpoints.
- [ ] Indexes exist for filters, sorts, joins, and foreign keys on real query paths.
- [ ] Pagination is bounded and done in the database; page size has a maximum.
- [ ] Responses use `select` (or DTO mapping) so sensitive columns such as password hashes are never returned.
- [ ] Soft delete is a recorded decision. If used, one place applies the `deletedAt` filter, and uniqueness and retention are defined. Personal data deletion requirements are met.
- [ ] The connection pool size fits the deployment's replica count and the database limit.

## Dependencies and supply chain

- [ ] The lockfile is committed; CI installs with `npm ci` (or the package manager's frozen equivalent).
- [ ] `npm audit --omit=dev` is reviewed for changed dependencies; findings are triaged, not blindly auto-fixed.
- [ ] Every new direct package is justified (need, maintenance, license, install scripts) and recorded in the plan's **Used packages**.
- [ ] Packages from unknown publishers or with `postinstall` scripts get a manual look before adoption.
- [ ] Third-party agent skills and CLIs (for example delegation tools) are reviewed and pinned before installation.
