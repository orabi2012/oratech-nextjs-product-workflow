# Oratech Next.js Product Workflow

A reusable agent skill for planning, scaffolding, building, and reviewing products that use Next.js, NestJS, Prisma, and PostgreSQL. It also covers bilingual interfaces with RTL and LTR layouts.

## Installation

With Node.js/npm available and access to [this repository](https://github.com/orabi2012/oratech-nextjs-product-workflow), install the skill using the [Skills CLI](https://github.com/vercel-labs/skills):

```bash
npx skills add orabi2012/oratech-nextjs-product-workflow -g
```

Choose Claude Code, Codex, or both when prompted. `-g` makes the skill available across projects. To install only in the current project, run the command from that project's root without `-g`:

```bash
npx skills add orabi2012/oratech-nextjs-product-workflow
```

The repository contains one skill at its root; the installer should copy `SKILL.md` together with `references/` and `assets/`. If the repository is private, the developer needs GitHub access configured for Git, GitHub CLI, or SSH.

### Use and update

| Host | Invoke with |
| --- | --- |
| Claude Code | `/oratech-nextjs-product-workflow` |
| Codex | `$oratech-nextjs-product-workflow` |

To update an installation managed by the Skills CLI, run `npx skills update oratech-nextjs-product-workflow`. If a new installation does not appear, restart the host application.

### Manual installation

If the Skills CLI is unavailable, copy the whole skill folder, including `SKILL.md`, `references/`, and `assets/`, to the appropriate location:

| Host | All projects | One project |
| --- | --- | --- |
| Claude Code | `~/.claude/skills/oratech-nextjs-product-workflow/` | `<project>/.claude/skills/oratech-nextjs-product-workflow/` |
| Codex | `~/.agents/skills/oratech-nextjs-product-workflow/` | `<project>/.agents/skills/oratech-nextjs-product-workflow/` |

Keep the folder name and the `name` in `SKILL.md` identical. For a project installation, commit the copied files so teammates receive the skill with the project.

## When to use it

Use this skill when starting a product on this stack, turning a product idea into a technical plan, creating the base project scaffold, implementing a development step, or reviewing an existing feature. The skill starts with the project's own requirements and conventions; it does not impose a fixed product or repository layout.

Example requests:

- "Use `oratech-nextjs-product-workflow` to interview me and prepare a technical plan for this product."
- "Create the base scaffold from the approved technical plan."
- "Implement the next development step, including Arabic RTL and English LTR behavior."
- "Review this feature against the plan and run the relevant checks."

## How it works

1. Inspect the repository and any existing brief or plan.
2. Ask focused questions about decisions that affect the architecture or next step.
3. Record the technical plan, including security controls, chosen packages, language behavior, and verifiable development steps.
4. When asked to start implementation, create a working scaffold or implement the selected step.
5. Check the changed behavior and update the plan to reflect what was built.

For a new project, the suggested plan location is `docs/technical-plan.md` in that project's repository. The templates here are starting points and can be adapted to its conventions.

## Contents

| Path | Purpose |
| --- | --- |
| [SKILL.md](SKILL.md) | Skill entry point and workflow routing |
| [references/technical-planning.md](references/technical-planning.md) | Planning interview and development-step guidance |
| [references/scaffolding.md](references/scaffolding.md) | Base scaffold guidance |
| [references/architecture.md](references/architecture.md) | Application, data, and integration decisions |
| [references/engineering-standards.md](references/engineering-standards.md) | Security, API, data, and operational standards |
| [references/web-ui.md](references/web-ui.md) | Next.js UI and bilingual RTL/LTR behavior |
| [references/step-loop.md](references/step-loop.md) | Feature implementation and verification workflow |
| [references/deployment.md](references/deployment.md) | Container, environment, and release decisions |
| [references/official-documentation.md](references/official-documentation.md) | Official documentation to check for version-sensitive choices |
| [assets/technical-plan-template.md](assets/technical-plan-template.md) | Technical plan template |
| [assets/step-spec-template.md](assets/step-spec-template.md) | Development-step specification template |
| [assets/codex-brief-template.md](assets/codex-brief-template.md) | Optional implementation brief template |

## Maintenance

Keep the folder name and the `name` in `SKILL.md` identical: `oratech-nextjs-product-workflow`. Update guidance when stack versions or project practices change, and verify version-sensitive decisions against the linked official documentation. Keep project-specific requirements and package versions in the project's technical plan rather than in this reusable skill.
