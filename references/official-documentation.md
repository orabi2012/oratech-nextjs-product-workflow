# Official documentation checks

Use this only for the component touched by the task. Start with versions in the project's manifests, lockfile, container images, and runtime. Read installed package documentation or generated repository instructions when available. Then consult the matching official documentation for a changed API, generator, migration command, Compose behavior, or PostgreSQL operation. Do not load every site for an ordinary UI edit.

| Component | Official starting point | Check when |
|---|---|---|
| Next.js | [App Router docs](https://nextjs.org/docs/app) and [internationalization](https://nextjs.org/docs/app/guides/internationalization) | Routing, server rendering, locale behavior, build or type generation |
| NestJS | [NestJS docs](https://docs.nestjs.com/) and [CLI workspaces](https://docs.nestjs.com/cli/monorepo) | Generator, modules, guards, testing, workspace mode |
| Prisma | [Prisma ORM docs](https://www.prisma.io/docs/orm) and [versioned CLI docs](https://www.prisma.io/docs/cli) | Client generation, schema, migration creation or deployment |
| Docker | [Docker Compose docs](https://docs.docker.com/compose/) and [startup order](https://docs.docker.com/compose/how-tos/startup-order/) | Images, services, health checks, networking, volumes, secrets |
| PostgreSQL | [Versioned PostgreSQL manuals](https://www.postgresql.org/docs/) and [backup and restore](https://www.postgresql.org/docs/current/backup.html) | Database version, constraints, indexing, connection security, backup or restore |

Select the documentation version that matches the project, not merely the site's current landing page. Prisma migration commands and formats differ across major versions; verify the installed version's procedure before writing a command or deployment script. Prefer a stable release over a prerelease unless the developer deliberately chooses otherwise.

Record only consequential decisions in the technical plan or step spec: package version, command or API selected, and why. If local behavior conflicts with documentation, reproduce the conflict and report it before adopting a workaround.
