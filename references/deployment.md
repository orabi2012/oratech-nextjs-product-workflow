# Deployment and release decisions

Read the project's existing infrastructure and release policy first. The following is a checklist of decisions, not a prescribed branch or hosting setup.

Before changing Compose files, PostgreSQL settings, backups, or Prisma deployment commands, check the selected component versions and the relevant [official documentation](official-documentation.md).

## Build and runtime

- Build web and API images separately if they deploy separately. Generate the Prisma client during the build when package installation skips lifecycle scripts.
- Apply schema migrations in a controlled deployment step with a backup and rollback plan. Make bootstrap or first-admin creation idempotent and keep generated credentials out of logs.
- Keep secrets and environment-specific origins outside the repository. Commit an example environment file with placeholders and clear descriptions.
- Expose only the intended public service. Restrict database and API network access, and terminate TLS at a known proxy boundary.
- If rate limits, caches, or job state live in process memory, a single replica may be necessary. For multiple replicas, use shared state or an explicit invalidation strategy.

## Environments and releases

- Determine branch names, CI gates, image tags, staging behavior, and production approval from the repository. Do not assume a `development` to `staging` to `main` flow.
- Keep staging from being indexed when it is publicly reachable. Verify migrations and configuration there before production when a staging environment exists.
- Treat a push or merge that triggers deployment as a release action. Follow the user's authorization and the repository's controls.
- Provide backup and restore instructions and test a restore before operational handover when the app owns its database.

## Proxy and external access

- Configure trusted proxy hops from the real CDN, host proxy, and application path. Test the resulting client address used by logs and rate limits; an incorrect value can enable spoofing or group visitors together.
- Avoid editing control-panel-generated proxy files directly. Use the supported site or proxy configuration mechanism.
- Keep customer-specific hosts, credentials, and account details in deployment configuration, not committed docs. Examples use placeholders.
