# Feature workflow

Use the depth this task needs. A copy fix may need one focused edit and check; a schema, permission, or API change needs a written spec and broader verification.

| Stage | Entry | Exit | Artifact |
| --- | --- | --- | --- |
| 1. Orient | A task or next plan step exists | Scope, affected surfaces, and blocking decisions are known | Notes (no file) |
| 2. Define | Orient exit; no blocking decision | User has agreed the behavior (spec status `agreed`) | Step spec |
| 3. Implement | Spec agreed (or task is trivial) | Behavior works locally; focused tests written | Code, tests, migration |
| 4. Review and verify | Implementation done | Diff reviewed; verification ladder passed or gaps stated | Check results |
| 5. Document and hand over | Verified | Plan, spec "As built", and docs match the code | Updated docs, report |

**Stop and ask** when a product or policy decision blocks the next stage, or when two fix attempts at the same failing check have not worked. Do not proceed on assumptions that change behavior.

## 1. Orient

- Read repository instructions, the plan's **Current state**, the relevant plan section and code, nearby tests, package scripts, and `git status`.
- Separate planned from implemented behavior. Check nearby patterns before adding a new one.
- For a new project or major rebuild, follow [technical planning](technical-planning.md) first. For an existing plan, confirm the next step still matches the plan and the code.
- Identify external effects, data migrations, role changes, and decisions the user must make.

## 2. Define the change

**A spec is required** for any schema or migration change, permission or role change, new or changed API contract, new dependency, or change touching more than one app. Otherwise a short note in the PR or report is enough. Use [the step spec template](../assets/step-spec-template.md) and omit irrelevant sections. Record rules in the spec, not only in an implementer's brief.

For genuine policy trade-offs give two or three options, consequences, and a recommendation. Do not silently choose for the user. Flag hidden effects on existing interactions before implementing.

## 3. Implement

- Work in small, coherent increments. Write focused tests first when they clarify a rule or protect a regression.
- Reuse existing helpers and contracts. Keep migrations additive; never rewrite an applied migration.
- For delegated implementation (optional), use the matching installed `*-delegate` skill from [delegate-skills](https://github.com/amElnagdy/delegate-skills). Record the working-tree state first, give the spec and relevant files in [a brief](../assets/implementer-brief-template.md), and keep the delegated change distinguishable from earlier work. If delegation is unavailable, implement directly.
- Coordinate servers, ports, and browser runs with other active work; stop processes you started.

## 4. Review and verify

Review the complete diff against the spec. Check permissions, validation, data access, errors, UI states, and duplicated rules; remove dead code. For anything touching auth, input, secrets, uploads, or migrations, walk the [security checklist](security-checklist.md).

Run the project's own scripts first (`package.json`), in this order, stopping to fix at the first failure:

1. Focused tests for the change: `npm test -- <path>` (or the project's runner).
2. Types and lint: `npx tsc --noEmit` per app; `npm run lint`.
3. Prisma, when the schema changed: `npx prisma validate`; `npx prisma migrate dev` on a disposable local database to create the migration; read the generated SQL; `npx prisma migrate status`; `npx prisma generate`. Never run `migrate reset` or `migrate dev` against valuable data; production uses `migrate deploy`.
4. Build: `npm run build` (`nest build`, `next build`).
5. Integration and E2E: API tests against a test database; browser tests (for example Playwright) in each locale and direction when the UI changed.
6. Dependencies, when `package.json` or the lockfile changed: `npm audit --omit=dev`. Report findings; do not auto-fix.

Use a safe test database. Treat a reported pass from another implementer as a lead, not verification. Record failures and skipped checks accurately. Skip a rung only when it is unaffected (for example, no Prisma step when the schema did not change), and say so.

For browser changes, test the affected flow at representative viewports and input modes, and check loading, empty, error, and success states. Use the existing browser harness.

## 5. Document and hand over

Update what the change makes stale: setup instructions, API or data model, the spec's **As built**, the plan's step status and requirement coverage, and the plan's **Current state** paragraph. Do not create documents just to satisfy a checklist.

Report what changed, what was checked, open issues, and any migration or operational step. Commit, merge, push, or deploy only as the user or repository policy allows.
