# <Feature or step title>

Status: draft | agreed | blocked | built
Plan link: <technical plan section or step ID>
Depends on: <earlier step IDs, if any>

## Goal
What user-visible or operational result should this change produce?

## Decisions
List only unresolved product or policy choices, with options, consequences, and a recommendation.

## Scope
In scope: <what this step delivers>.
Out of scope: <explicit exclusions and deferred work that might otherwise be assumed included>.

## Behavior
Rules and edge cases, including permissions, empty data, duplicates, failures, and concurrent actions where relevant.

## Acceptance criteria
- [ ] <Observable, testable criterion, including the denied or error case>

## Data and API
Schema or migration changes; routes, roles, input, output, and stable errors. Omit sections that do not apply.

## Security notes
Controls this step implements or changes (see the security checklist), how each is verified, and any direct packages added or upgraded with reason and version. Write "none" if nothing applies; do not leave it blank.

## UI
Screens and states (loading, empty, error, success), navigation, accessibility, responsive behavior, and copy or localization requirements.

## Languages and direction
If multilingual: supported locales; translated content and fallback rules; URL and locale-switch behavior; RTL/LTR layout and mixed-direction fields; checks in each language. Omit for a single-language feature with no localization impact.

## Test plan
Focused rule tests, integration or API tests, browser flows (per locale and direction), and the repository checks that demonstrate the result. Name the commands.

## Rollback
How to undo this change safely: revert and redeploy, feature flag, or a reverse migration plan. For a data or schema change, state the backup step, whether the migration is reversible, and what happens to data written after release.

## As built
Fill in after implementation: actual files or interfaces, decisions and deviations, checks run, and remaining follow-up.
