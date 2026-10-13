# Base project scaffold

Use this when the user asks to start a new project from its technical plan. The deliverable is a runnable, verified foundation in the target workspace, not a directory diagram. If an app already exists, add only the missing foundation needed for the plan.

## Before creating files

- Read the technical plan, repository instructions, current tree, package manager and runtime versions. Resolve only choices that affect the scaffold: target directory, app boundaries, package manager, two locales and default direction, test tools, and deployment needs.
- Keep the first scaffold step narrow. Do not invent domain tables, roles, client branding, or integrations that the plan has not defined.
- Preserve existing source and configuration. Inspect conflicts before using a generator or writing a file that already exists. A generator may change many files; review its output before adding custom code.

## Create the planned foundation

Use the repository's existing tools or suitable installed generators, then adapt their output. Illustrative only (use it when the plan chooses an npm-workspaces monorepo with separate web and API apps; otherwise follow the plan):

```text
apps/web/                Next.js App Router, locale-aware starter page
apps/api/                NestJS app, configuration and a health endpoint
packages/shared/         Only if both apps already share a real contract
docs/technical-plan.md   The plan used to derive the scaffold
docs/dev-plans/          First step spec and subsequent steps, if this is the repo convention
package.json             Workspace scripts for available checks
<lockfile>                One lockfile for the chosen package manager
.gitignore
.env.example             Variable names and safe sample values, no secrets
README.md                Setup and verification commands
```

If the first scaffold step uses the database, place Prisma schema and migration tooling with the API or in the repository's established location and verify the connection. Otherwise, introduce Prisma in the first data step. Add `e2e/`, `deploy/`, client configuration, or shared design packages only when the plan calls for them. Do not create empty directories or placeholder modules solely to match this example.

For a bilingual RTL/LTR plan, scaffold the selected locale routing strategy, translation dictionaries or equivalent, server-rendered `lang` and `dir`, and a minimal language switch that preserves a meaningful route. Verify a starter screen in both directions. Use [the web UI rules](web-ui.md) for mixed-script content and navigation details.

## Make it usable

- Wire root scripts to the checks actually configured: development, lint, typecheck, build, and tests as applicable. Use one package manager and a reproducible lockfile.
- Reconcile generated dependencies with the plan's direct-package list. Prefer stable versions compatible with the selected runtime, record why any extra package is needed, and remove unused generator extras rather than carrying them into the project by default.
- Read instructions emitted by generators and check the installed framework's local docs before changing generated code; use [official documentation checks](official-documentation.md) when needed. A standalone Next.js typecheck may need route type generation before TypeScript runs; locale routes may need static parameters under the chosen build configuration. Verify both with the actual package versions rather than assuming a command from an older project.
- Add a minimal smoke check that proves the app starts and the chosen locale renders. Add an API health check and database connection check only when those services are part of the planned scaffold.
- Document required environment variables, local database setup, and exact startup commands. Keep real secrets out of the repository.
- Run the available checks and launch the starter app or use its test harness to verify the base flow. Report exact commands, passes, failures, and missing dependencies. Do not claim the scaffold is complete when it has never started.
- Update the first step spec's **As built** section and the technical plan if generated structure differs from the plan.
