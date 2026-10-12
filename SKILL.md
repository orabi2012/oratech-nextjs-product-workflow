---
name: oratech-nextjs-product-workflow
description: Discover requirements, write a technical plan, scaffold a project, derive development steps, and build or review Next.js and NestJS applications using Prisma and PostgreSQL, including bilingual RTL/LTR interfaces. Use for new projects, planning, setup, feature steps, architecture, web UI, testing, and deployment on this stack.
---

# Next.js product workflow

Use this skill to make a change understandable, testable, and ready for the project's own release process. First discover the repository's conventions and the user's preferences. The patterns in these files are starting points, not requirements to retrofit into an existing app.

## Start with the project

1. Read the repository instructions, current docs, package scripts, relevant code, and working tree. Compare plans with implemented behavior.
2. Identify the task's scope and any real product or policy decisions. Present options and a recommendation when the choice belongs to the user; keep working on independent parts while waiting.
3. Follow the existing architecture, locales, test setup, deployment flow, and contribution rules unless the user asks to change them. Do not infer a client-specific product, monorepo, or particular branch names from this skill.

## Plan before building a new project

For a new project or major rebuild, use [the technical planning interview](references/technical-planning.md). Inspect any existing brief or plan first, then ask the developer only for missing, consequential decisions. Ask a small group of clear questions at a time; offer options and a recommendation when the developer is choosing among approaches. Do not fill product-policy gaps with invented requirements.

Write the technical plan in the repository's preferred location, or use [the technical plan template](assets/technical-plan-template.md) at `docs/technical-plan.md`. Mark facts, assumptions, and open decisions distinctly. Record the security controls and direct packages the product needs, with reasons and versions. Derive an ordered list of small, verifiable development steps from the plan. Keep each step linked to a goal, dependency, and acceptance evidence. Reconcile the plan with the code as implementation progresses.

## Scaffold from the plan

When asked to start the project, make the first development step a [working base scaffold](references/scaffolding.md), unless the repository already has one. Create the directories, configuration, starter code, and baseline checks in the workspace; do not stop at a proposed tree. Use the plan to choose the app layout and initial bilingual setup. Preserve existing files and report anything that could not be generated or verified.

## Feature loop

For a substantial feature, use [the step workflow](references/step-loop.md): agree on observable behavior, record a brief spec when useful, implement in small increments, review the diff, run relevant checks, update affected docs, and report evidence. Use [the step spec template](assets/step-spec-template.md) if the repository has no preferred format. For a small fix, keep the process proportionate.

Implement directly unless the user or repository instructions request delegation. If delegating, use an available delegation tool and [the implementation brief template](assets/codex-brief-template.md), then independently review the result. A template does not authorize external actions, commits, pushes, or deployment.

## Engineering choices

- Read [architecture](references/architecture.md) when designing modules, shared rules, authentication, data, workflows, or integrations.
- Read [engineering standards](references/engineering-standards.md) when implementing or reviewing API, database, security, performance, and error behavior.
- Read [web UI](references/web-ui.md) for Next.js screens, URL state, accessibility, bilingual RTL/LTR behavior, and browser checks.
- Read [deployment](references/deployment.md) only for CI, containers, environment configuration, or release work.
- Use [official documentation checks](references/official-documentation.md) when scaffolding, changing dependencies, or using version-sensitive Next.js, NestJS, Prisma, Docker, or PostgreSQL behavior.

Apply a pattern only when its premise matches the project. Preserve established contracts and avoid adding infrastructure for a hypothetical future use.

## Use context efficiently

- Load only the reference for the current task. In an existing repository, locate the relevant plan section, nearby code, and tests instead of rereading every file.
- Keep one source of truth: the technical plan holds cross-step decisions; a step spec links to it; an implementation brief links to the spec. Do not repeat whole documents in each layer.
- Ask only questions that change the next decision. Once a plan is actionable, record later questions and proceed with independent steps.
- Start verification with the changed behavior, then run broader checks required by the repository or risk. Stop optional checks when the result is sufficiently supported.

## Completion

Run the checks that exist and matter for the change. State what passed, failed, or could not run; never call a change verified on the implementer's report alone. Summarize changed behavior, notable decisions, and any required follow-up. Follow the user's or repository's commit and release policy.
