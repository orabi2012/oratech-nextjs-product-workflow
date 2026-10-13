# Engineering standards

Apply the sections relevant to the change and the repository's established conventions. The goal is clear boundaries and evidence, not a fixed class hierarchy.

## Code structure

- Keep HTTP parsing and response concerns at the boundary, domain rules in services or pure functions, and persistence in data-access code.
- Give a rule one clear home. Introduce shared utilities or interfaces when real callers need them; remove unused generality.
- Use names that describe the domain. Comments explain constraints or non-obvious reasons, not lines the code already explains.
- Keep tests close to behavior: focused rule tests, integration tests for database or API contracts, and browser tests for user flows.

## Security and privacy

The checkable list lives in [the security checklist](security-checklist.md); walk it for auth, input, secrets, uploads, headers, Prisma migrations, and dependencies. Principles:

- Authorize by default; check object-level access, not only login state.
- Keep secrets out of source, responses, and logs.
- Validate at every boundary and enforce invariants in the database too.
- Fail closed, and never leak internals in errors.

## Data and performance

- Add forward-only Prisma migrations. Never edit an applied migration or run a development reset against valuable data.
- Use transactions for related writes. Handle unique conflicts and concurrency explicitly where a race is plausible.
- Push filtering, sorting, and pagination to the database. Batch related reads to avoid N+1 queries and add indexes for important query paths.
- Bound concurrency for large independent batches; make jobs resumable or retryable when failure has lasting effects.
- Consider server rendering and bundle size for public pages, while keeping interactive code where the interaction needs it.

## API and errors

- Use HTTP status codes consistently: 400 invalid input, 401 unauthenticated, 403 forbidden, 404 unavailable or undisclosed, 409 conflict, 429 limited, and 5xx unexpected failure.
- Keep one stable error shape and machine-readable codes. Map expected errors at one boundary; never send stack traces to clients.
- Make retryable writes idempotent when possible. For jobs, persist status and enough safe error detail for operators to diagnose and retry.

## Observability and external systems

- Use structured logs with correlation IDs and stable event or error codes. Exclude credentials, tokens, personal content, and request bodies unless a specific, reviewed need requires them.
- Alert on failures that require action. Preserve evidence of discrepancies with external systems instead of silently adjusting authoritative values.
- Reproduce defects with representative data and report the observed result, the cause, and the verification after the fix.
