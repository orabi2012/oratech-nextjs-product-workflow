# Feature workflow

Use the depth this task needs. A copy fix may require a focused edit and check; a schema or permission change needs a written contract and broader verification.

## 1. Orient

- Read repository instructions, the plan's compact **Current state**, relevant plan sections and code, nearby tests, package scripts, and `git status`. Expand only where the task needs more context.
- Separate planned behavior from implemented behavior. Check nearby patterns before adding a new one.
- For a new project or major rebuild, follow [technical planning](technical-planning.md) before deriving feature steps. For an existing plan, check that the next step still matches the plan and current code.
- Identify external effects, data migrations, role changes, and decisions the user must make.

## 2. Define the change

For a substantial step, write a short spec in the repository's preferred location. [The template](../assets/step-spec-template.md) covers behavior, edge cases, data, API, UI, security, tests, and scope; omit irrelevant sections. Record rules in the spec rather than only in an implementer's brief.

For genuine policy trade-offs, give two or three options, consequences, and a recommendation. Do not silently choose on the user's behalf. Flag a hidden effect on an existing interaction before implementing it.

## 3. Implement

- Work in small, coherent increments. Write focused tests first when they clarify a rule or protect a regression; test business rules at the narrowest useful level.
- Reuse existing helpers and contracts. Keep migrations additive; do not rewrite applied migrations.
- If the user or repository calls for delegated implementation, use the matching installed `*-delegate` skill from [delegate-skills](https://github.com/amElnagdy/delegate-skills). Capture the current working tree state, provide the spec and relevant files in [a brief](../assets/codex-brief-template.md), and keep the delegated change distinguishable from pre-existing work.
- Coordinate servers, ports, and browser runs with other active work. Stop processes started for the task.

## 4. Review and verify

Review the complete diff against the intended behavior. Check permissions, validation, data access, errors, UI states, and duplicated rules. Correct defects and remove dead code.

Run relevant repository checks yourself: focused tests, then applicable lint, typecheck, build, integration, and browser checks. Use the project's documented commands and a safe test database. Treat a reported pass from another implementer as a lead, not verification. Record failures and skipped checks accurately.

For browser changes, test the affected flow and representative viewport and input modes. Check loading, empty, error, and success states where applicable. Use the existing browser harness; do not force a particular browser visibility mode or fixed port unless the project requires it.

## 5. Document and hand over

Update the documentation that the change makes stale: setup instructions, current state, API or data model, and the spec's actual result. Do not create documents solely to satisfy a checklist.

Report what changed, what was checked, open issues, and any migration or operational step. Commit, merge, push, or deploy only according to the user's request and repository policy; preparing a change does not imply release authorization.
