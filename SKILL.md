---
name: oratech-nextjs-product-workflow
description: Use when planning, scaffolding, building a step of, or reviewing a product on Next.js + NestJS + Prisma/PostgreSQL, especially bilingual RTL/LTR (Arabic/English). Triggers include "plan a product", "technical plan", "scaffold the project", "implement the next step", "review this feature/PR", "add Arabic/RTL support". Not for isolated Next.js/React questions or other stacks (Express, Angular, Drizzle, TypeORM, non-web).
---

# Next.js product workflow

Make a change understandable, testable, and ready for the project's own release process. Discover the repository's conventions first; the patterns here are starting points, not requirements to retrofit into an existing app.

## Start with the project

1. Read repository instructions, current docs, package scripts, relevant code, and the working tree. Compare plans with implemented behavior.
2. Identify the scope and any real product or policy decisions. Offer options with a recommendation when the choice is the user's.
3. Follow the existing architecture, locales, tests, deployment flow, and contribution rules. Do not infer a client-specific product, monorepo, or branch names from this skill.

## Route by task

| Task | Do this |
| --- | --- |
| New project or major rebuild | Run [technical planning](references/technical-planning.md); write the plan from [the template](assets/technical-plan-template.md) |
| "Start the project" | Only after the plan is approved: [base scaffold](references/scaffolding.md) |
| Feature or fix | [Step workflow](references/step-loop.md); spec from [the template](assets/step-spec-template.md) when the rules there require one |
| Review a feature, diff, or PR | [Review workflow](references/review.md) |
| Delegated implementation (optional) | [Delegation](#delegation-optional) |

**Approval gate:** do not scaffold or implement until the user has approved the plan (status `agreed`) or the step spec. Keep working only on independent planning questions while waiting.

## Read when

- [Architecture](references/architecture.md): designing modules, shared rules, auth, data, workflows, integrations.
- [Engineering standards](references/engineering-standards.md): code structure, data, API, and error behavior.
- [Security checklist](references/security-checklist.md): implementing or reviewing anything that touches auth, input, secrets, uploads, headers, dependencies, or Prisma migrations.
- [Web UI](references/web-ui.md): Next.js screens, URL state, accessibility, bilingual RTL/LTR, browser checks.
- [Deployment](references/deployment.md): CI, containers, environment configuration, release and client-handover work only.
- [Official documentation](references/official-documentation.md): scaffolding, dependency changes, or version-sensitive Next.js, NestJS, Prisma, Docker, PostgreSQL behavior.

Apply a pattern only when its premise matches the project. Preserve established contracts; add no infrastructure for a hypothetical future use.

## Delegation (optional)

Implement directly unless the user or repository instructions ask for delegation. If delegating, use the matching installed `*-delegate` skill from [delegate-skills](https://github.com/amElnagdy/delegate-skills) and adapt [the implementer brief](assets/implementer-brief-template.md). If that skill or CLI is unavailable, say so and offer to implement directly. Always review the diff and run the checks yourself ([review step](references/step-loop.md#4-review-and-verify)). A template never authorizes external actions, commits, pushes, or deployment.

## Use context efficiently

- Load only the reference for the current task; in an existing repo, find the relevant plan section, code, and tests rather than rereading everything.
- One source of truth per layer: plan holds cross-step decisions, a spec links to it, a brief links to the spec.
- Ask only questions that change the next decision; record the rest and proceed with independent work.
- Verify the changed behavior first, then the broader checks the repository or risk requires.

## Completion

Run the checks that exist and matter. State what passed, failed, or could not run; never call a change verified on an implementer's report alone. Summarize changed behavior, decisions, and follow-up. Follow the user's or repository's commit and release policy.
