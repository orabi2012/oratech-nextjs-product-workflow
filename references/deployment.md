# Deployment and release decisions

Read the project's existing infrastructure and release policy first. The first sections are a checklist of decisions, not a prescribed branch or hosting setup. The later sections (developer-operated server, client-owned production, the owner walk-through and the workflow shape) describe a proven setup to adapt: branch names there are roles (staging, production), not required names, and anything the project's own policy contradicts wins.

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

## Developer-operated server (staging, or production the developer runs)

Use this when the developer or owner has SSH access to the server and deploys there. Staging is almost always this case, and it is also the rehearsal ground for the client package below, so build it first and keep it free of anything specific to that one server.

- **One compose stack per environment**, in its own folder on the server with its own `.env` (never committed, generated secrets, mode 600) and its own Compose project name and loopback port (the exposure rules of Build and runtime apply).
- **Deploy from the pipeline, not by hand:** after the images are published, a job copies the compose file over SSH and runs `docker compose pull` and `up -d` (server details live in a GitHub environment, not in the repository). The server logs in to the registry once with a read-only token stored in its Docker configuration.
- **Start-up does the controlled steps:** the API entrypoint applies migrations, then the idempotent first-admin seed, then starts; a failed migration stops the container instead of serving a broken schema. Check `docker compose logs api` after every deploy.
- **Hosting facts to check once and write down:** the trusted proxy hops and the real client address (see Proxy and external access), TLS at the proxy, and the staging site marked not indexable.
- **Operations:** a scheduled backup with a tested restore, Docker starting on boot, the origin locked to the CDN's addresses when the origin address is exposed, and a note of who renews certificates, tokens and mail credentials.
- **Updating by hand when the pipeline is not available:** `cd` to the folder, `docker compose pull`, `docker compose up -d`, then the readiness route and a sign-in. A rollback is the previous image reference and, only when a migration was incompatible, the verified backup.
- **Keep the two cases apart:** a change that only the developer's server needs (a host path, an SSH key, a panel setting) never goes into the client package, and nothing in the client package may depend on the developer's server or accounts.

## Client-owned production and handover

Use this when the product is delivered to a client who runs it on their own server, so the developer has no access to production. Everything below was proven by rehearsing on a separate server before the first real release; keep to the same order.

- **Release by tag, deploy by digest.** A version tag on a commit of the production branch publishes the release images; there is no moving `latest` or `production` image tag. A verify job gates the release: tag shape (`vX.Y.Z` or `vX.Y.Z-rc.N`), the commit is an ancestor of the production branch, CI is green on that exact commit, and no image, record or GitHub Release exists for the tag. Clients deploy the image **digests**, never a tag.
- **Release record.** The pipeline writes `release.json` (commit, digests, previous release, added migrations) and `release.env` (the two image references) and opens a **draft** release. The owner completes three notes (migration compatibility, `Upgrade from: <stable tag>|none`, `Rollback needs database restore: yes|no`); the record is approved only after the owner publishes. Release candidates (`-rc.N`) are rehearsals: they say `Upgrade from: none` and a client never upgrades from one.
- **Client package** (zip from the pipeline): an image-only `compose.yaml` taking the two image variables, `.env.example`, `setup.sh`, `update.sh`, `backup.sh`, `restore-check.sh`, the record and a README written for the client's operator. A test must fail when this compose and the staging compose drift apart. Two stacks on one host collide when they share a Compose project name: the package carries `COMPOSE_PROJECT_NAME` and a different `WEB_PORT` is needed too.
- **Setup script** (POSIX `sh`, no bashisms, tested under `dash`): ask only what only the client knows, generate every secret on the client's server, refuse to overwrite an existing `.env`, write `.env` through a temporary file with mode 600, **single-quote every written value** (Compose interpolates `$` and treats ` #` as a comment), print the first administrator's one-time password once and never log it, and run `docker compose config --quiet` **after** the summary so a failed check cannot hide the password. Never document or script a bare `docker compose config`: it prints every secret; allow only `--quiet` or `--images`, and test the docs for it.
- **Update script:** read the approved record and refuse an unapproved one, a candidate, an installed version below `upgradeFrom`, and a downgrade; take and **verify** a backup (a restore check into a temporary database) before touching anything; pull the new images first with the two variables exported for that single command (a shell variable overrides the env file); keep a private copy of `.env`, rewrite only the two image lines, run `up -d`, wait for the **readiness** route (database check) with a timeout; on failure print the rollback commands and never run them; use a lock directory against two runs. A hidden `--rehearsal` flag may waive only the candidate rules so the whole path can be rehearsed before a stable tag exists; keep it out of the client's README.
- **Test shell scripts without a server:** put stub `docker` and `curl` scripts first in `PATH`, log every call, and assert the order of calls, that the env file is byte-identical on every refusal, and that no secret reaches the output. Prove on Windows that the stub (not the real binary) ran. Windows OpenSSL prints a trailing carriage return: strip it. Build test records with the pipeline's own functions so a format change breaks a test.
- **Image labels and readiness:** put the version in an image label (`org.opencontainers.image.version`) so an operator can read what runs; expose a readiness route that checks the database, separate from the plain liveness route. An intentionally public route must be added to the project's public-route allowlist test or CI fails.
- **Registry access for a private registry:** give the client's deploy team an account of their own with **Read** on every package (images and release record) and a classic token with only `read:packages`; the developer's token never leaves the developer. On a package linked to a repository, first set the repository's role under Actions access to **Write**, then clear "inherit access from source repository", then invite the account, otherwise the release workflow loses its write access. Prove it from a separate server (login, pull both images, pull the record) and confirm the next staging push still publishes. Record the token's expiry and renewal date with the client.
- **Rehearse before the client does:** install the package from a candidate on a server you control in a fresh folder with its own project name and port, upgrade it to a second candidate with the update script, check that data written before the upgrade survived, run the printed rollback, take a backup and run the restore check, then remove the rehearsal stack. Two candidate tags on the same commit work, because the version label differs.
- **Ownership at handover:** monitoring, secret rotation, SSO/IdP registration and the client's own legal text are the client's team's; document what they need, do not build it.

## Walking the owner through a server step by step

When the owner runs the commands (the agent has no access to the server), for either kind of server above, guide them like this; it is what made the rehearsal safe.

- **One step per message.** Number it, give the exact commands in one block, say what output to expect, and stop. Do not continue until the owner reports the result; if the result differs, diagnose before the next step.
- **Say what each command changes**, and for anything destructive (`down -v`, `rm -rf`, deleting a package role) name exactly what is removed and what is not, and make sure the owner confirms first. Prefer a check command (`--check`, `ps`, `diff`) before the command that changes things.
- **Secrets stay with the owner.** Never ask for `.env`, a token or a password; tell the owner to keep generated passwords private and not to paste them or screenshots showing them into the chat. If one does appear, say so once, treat it as burned if it matters, and carry on.
- **Fix a misleading earlier instruction openly** (for example a permission page that looks different from what was described) and re-state the safe order before the owner clicks anything else.
- **Order of a rehearsal:** merge locally and let the owner push; wait for each pipeline run to be green before the next push; tag; owner fills the release notes and publishes; install from the package in a fresh folder; health check and version label; sign in and create a marker record; upgrade with the update script (check mode first); confirm the marker survived; run the printed rollback; backup and restore check; remove the rehearsal stack; confirm only the real stacks remain.
- **Match the owner's language and pace:** short sentences in the owner's own language, the commands in code blocks, no long background unless asked. After each green result say what was just proven.

## Shape of the GitHub Actions image workflow

One `images` workflow serves staging and production releases; keep its jobs and triggers in this shape.

- **Triggers:** `pull_request` (build only, no push), `push` to the staging branch, `push` of a `v*` tag, `release: published`, and a manual `workflow_dispatch`. A manual run publishes only a `sha-...` image and can never produce a version tag.
- **Staging:** `publish` on a push to the staging branch builds and pushes the staging and `sha-` tags, then `deploy-staging` (needs `publish`, runs in a GitHub **environment** that holds the server host, user, key and port as secrets and variables) copies the compose file over SSH and runs `docker compose pull` and `up -d`.
- **Production release, in order:** `verify-release` (tag push only: tag shape, commit is an ancestor of the production branch, polls up to 30 minutes for a green CI run on that exact commit, refuses an existing image, record tag or GitHub Release) → `publish-release` (needs verify; builds and pushes the release images through one shared composite action that the staging job also uses) → `release` (needs publish; reads both image **digests**, builds `release.json` and `release.env` with a tested script, zips the client package, creates a **draft** GitHub Release with the generated notes and pushes the unapproved record to the registry as `release:pending-<tag>`) → `promote-release` (runs on `release: published` only; validates the three owner notes, completes the record and the zip, writes the approved `release:<tag>` **once**, and moves a `current` pointer for stable releases only).
- **Why the record lives in the registry:** the repository is private, so a client cannot read a GitHub Release; the approved record is pulled with `oras` using the same registry account that pulls the images. `pending-*` tags are for the owner only.
- **Controls:** least-privilege `permissions:` per job, a `concurrency` group per tag (a release is never built twice at once) and one for promotion, pinned major versions of the official actions, and a repository tag ruleset limiting who may create, update or delete `v*` tags. Nobody commits to the staging or production branches directly.
- **The workflow cannot be run offline.** Its first real run is a rehearsal with a candidate tag; expect to fix it once and keep every shell step in a script with its own unit tests rather than inline YAML where possible.

## Proxy and external access

- Configure trusted proxy hops from the real CDN, host proxy, and application path. Test the resulting client address used by logs and rate limits; an incorrect value can enable spoofing or group visitors together.
- Avoid editing control-panel-generated proxy files directly. Use the supported site or proxy configuration mechanism.
- Keep customer-specific hosts, credentials, and account details in deployment configuration, not committed docs. Examples use placeholders.
