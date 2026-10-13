# Review workflow

Use this to review a feature, branch, diff, or pull request, whether it was written by a person, by you, or by a delegated implementer. Report findings in chat; do not post comments to a remote or change files unless the user asks.

## 1. Scope

- Identify what is under review: PR number, branch, commit range, or working-tree diff. Read the diff stat first, then the spec or plan step it claims to deliver.
- If there is no spec, infer the intended behavior from the PR description and tests, and say that you inferred it.
- Check `git status` so unrelated local changes are not blamed on the change.

## 2. Check

Go through only what the diff touches.

- **Behavior:** does the diff implement the spec, including edge cases? Is anything out of scope included?
- **Security:** walk [the security checklist](security-checklist.md), especially object-level authorization on every ID-based read or write, DTO/Zod validation, secrets, and error leakage.
- **Data:** migration is forward-only and safe to deploy, indexes exist for new query paths, no N+1, transactions where writes must be atomic.
- **UI:** states (loading, empty, error, success), keyboard and focus, and RTL and LTR per [web UI](web-ui.md).
- **Tests:** do they fail without the change? Do they cover the denied case, not only the happy path?
- **Dependencies:** each new package is justified; lockfile changes match `package.json`.
- **Docs:** plan, spec, and README match the code.

Run the [verification ladder](step-loop.md#4-review-and-verify) yourself. Do not rely on CI or the author's report alone.

## 3. Report

Order by severity. Use this shape:

1. **Verdict** in one line: approve, approve with changes, or block.
2. **Findings** as a table: `Severity (high/med/low) | file:line | issue | fix`. High means exploitable, data-losing, or breaking; med means incorrect in some conditions; low means maintainability or polish.
3. **Checks run** with exact commands and results, and what could not run.
4. **Questions** for the author where intent is unclear.

Mark each finding as confirmed (reproduced or clearly read in the code) or suspected. Do not pad the report with style nits when there are real defects.
