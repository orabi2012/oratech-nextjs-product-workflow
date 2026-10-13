# Implementation brief: <feature>

Use this template only when the user or repository workflow calls for delegation. It must be self-contained: the implementer sees only this brief and the repository, so put every rule it needs here or in a committed file named below. Adapt it to the actual implementer (Codex, Claude Code, Copilot, or another).

## Goal
<One or two sentences: the observable result.>

## Contract
- Spec: <committed path, or paste the behavior rules here>
- Repository instructions: <paths, for example AGENTS.md or CLAUDE.md>
- Relevant existing code and tests: <paths>
- Existing uncommitted changes to preserve: <summary or snapshot reference>

## Acceptance criteria
- [ ] <Observable, testable criterion>
- [ ] <Include the denied or error case, not only the happy path>

## Files in scope
- <Paths or globs the implementer may change>

## Out of scope
- <Paths, behaviors, or refactors it must not touch>

## Constraints
- No new dependencies, and no edits to applied migrations, unless listed here: <exceptions>
- Do not read, print, or modify `.env*` files or any secret.
- Follow existing patterns; do not add speculative features.
- If the spec is ambiguous or the task needs something outside scope, stop and report the question instead of guessing.

## Environment
- Package manager and installed dependencies: <facts>
- Commands and test database: <exact commands>
- Server or browser constraints: <facts, if any>
- Supported locales, direction, and translation conventions: <facts, if applicable>

## Security notes
<Controls this change must respect, for example object-level authorization, validation, rate limits. Write "none" if truly not applicable.>

## Verification
Run these and report exact commands and results: <focused tests>, <typecheck>, <lint>, <build>, and any Prisma checks. Report failures and checks that could not run. Stop processes you started.

## Deliverables
Code, meaningful tests, and documentation affected by the change. Report changed files, deviations from the spec, and unresolved issues. Do not commit, push, or deploy.

---
Orchestrator review (not for the implementer): compare `git diff --stat` with Files in scope; look for unexpected files, lockfile, migration, or config changes and any secret; read the full diff against the acceptance criteria; re-run the verification commands yourself.
