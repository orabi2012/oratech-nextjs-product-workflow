# Technical plan: <project>

Status: draft | agreed | current
Last updated: <date>

## Product and first release
Problem, target users, success criteria, first-release outcomes, and explicit exclusions.

## Users and workflows
Key end-to-end journeys, roles, and permission boundaries. Identify decisions still needed from the developer.

## Languages and content
Supported locales, default locale, RTL/LTR mapping, translated interface and content, URL strategy, formatting, missing-translation behavior, and SEO if relevant.

## Architecture
App and service boundaries, major modules, shared rules, chosen technologies, and reasons for choices that are costly to reverse. Adapt to existing code.

## Scaffold decisions
Target repository layout, package manager, app generators or versions, required initial packages, test tools, and which optional directories are justified now. Note the first runnable bilingual flow and baseline commands.

## Used packages
Summarize decision-relevant direct packages or groups: app, chosen version or range, purpose, reason, and any maintenance or licensing constraint. Package manifests and the lockfile are the exhaustive inventory. Reconcile this summary with them when dependencies change; do not paste a transitive dependency tree.

## Documentation baseline
For a version-sensitive setup or command, record the selected version, relevant official documentation page, and the decision it supports. Keep this short; do not copy reference manuals into the plan.

## Data and integrations
Core entities and relationships, lifecycle, constraints, migration or import needs, external systems, source of truth, and failure or retry behavior.

## API
Main interfaces, inputs, outputs, errors, and versioning.

## Security measures
Data sensitivity and threat assumptions; authentication and role enforcement; session and CSRF approach if applicable; input validation; secret storage; CORS/CSP and network boundaries; rate limits; safe logging and audit; retention, backups, and recovery. For each required control, state where it is enforced, how it will be verified, and the step that implements it. Omit controls that do not apply and explain consequential omissions.

## Operations and verification
Environments, deployment ownership, configuration, backup and recovery, rollback approach for releases and migrations, observability, test strategy, and the quality-gate commands (lint, typecheck, test, build, migrate).

## Decisions and risks
Confirmed decisions with reasons; assumptions to verify; open questions with their owner and the development step they block; material risks and mitigations.

## Requirement coverage
For each first-release outcome or security control, record a short ID, the development step that delivers it, and the acceptance evidence. Keep this compact; link to step specs for details.

## Development steps
For each step (IDs like S1, S2, in dependency order): outcome; dependencies; included behavior and excluded work; affected data/API/UI; acceptance evidence; status. If starting from an empty repository, begin with a verified base scaffold, then add a usable end-to-end slice early.

## Current state
One short paragraph: last verified step, relevant commands or migration, next step, and blocking decisions. Update it after each step so a new session can orient without rereading the whole plan.
