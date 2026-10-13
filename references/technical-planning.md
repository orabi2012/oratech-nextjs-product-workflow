# Technical planning interview

Use this for a new product or major rebuild. The plan is the source for development steps, so separate confirmed requirements from guesses. Read an existing brief, design, repository, or technical plan before asking questions; do not make the developer repeat known facts.

## Interview loop

1. Summarize what is already known and identify decisions that change architecture, scope, sequence, cost, or risk.
2. Ask the developer a small batch of high-impact questions, preferably three or fewer. Start in product language, then ask technical questions only where the answer changes the plan. Give concise options and a recommendation when useful.
3. Record each answer as a requirement, constraint, or decision. Record unanswered items as open questions with the step they block. State assumptions explicitly and keep them easy to revise.
4. Draft or update the plan, show the developer the consequential assumptions and trade-offs, and incorporate corrections. Continue useful planning while waiting for answers; do not implement a step that depends on an unresolved decision.
5. **Exit the interview** when no open question blocks the first step. Set the plan status to `agreed` only after the developer explicitly approves it; do not scaffold or implement before that.
6. Derive development steps from the plan and verify that every first-release outcome and required security control has a step and acceptance evidence. Revisit the plan when a decision or implementation changes.

## Start with these questions when the answer is missing

Ask adaptively, not as a fixed questionnaire. A useful opening is: **Who will use the first release, and what must each user be able to complete from start to finish?** Then cover the gaps that affect design:

- **Scope:** Which outcomes are required for the first release, and which can wait? What existing process or product is being replaced?
- **Users and permissions:** Which roles exist? Who may create, review, approve, publish, administer, or view each kind of data?
- **Data:** What are the core records, their lifecycle, source of truth, expected volume, retention, and migration needs?
- **Integrations:** Which external systems are required? Who owns credentials and retry or failure handling?
- **Languages:** Which two locales are supported, which is the default, and must both cover interface copy, content, email, and SEO? What should happen when a translation is missing? Confirm which locale uses RTL and which LTR.
- **Security and operations:** Which data is sensitive? What authentication, authorization, audit, retention, privacy or compliance controls are required? Who owns hosting, secrets, backups, and recovery?
- **Packages and constraints:** Which libraries or versions are required or forbidden? What existing stack, budget, deadline, team capacity, performance goal, code, or infrastructure must be preserved?

Do not ask for passwords, tokens, or other secrets in chat. Capture the required configuration and ownership in the plan without recording secret values.

## Plan quality check

Use the repository's format or [the template](../assets/technical-plan-template.md). A plan is ready to drive steps when it identifies the first-release outcomes and exclusions, major user flows and role boundaries, bilingual behavior, data and integration contracts, architecture and direct package choices, required security controls, operational constraints, verification approach, and consequential unknowns. It does not need speculative detail for distant features.

## Turn the plan into steps

- Order steps by dependency, with a thin end-to-end user flow early enough to test the architecture. Avoid a long sequence of infrastructure-only steps when a smaller vertical slice is possible.
- If the project has no foundation, make the first step the [base scaffold](scaffolding.md): runnable apps, agreed locale structure, configuration, and baseline checks. Do not include speculative domain features in that step.
- Give each step one observable outcome, explicit scope, prerequisites, affected surfaces, and acceptance evidence. Include schema changes, migration or data work, RTL and LTR behavior, security controls, package additions, and documentation when applicable.
- Flag a step as blocked only by a decision it genuinely needs. Do not hide open decisions inside implementation briefs.
- When implementation differs from the plan, update the plan and the affected step rather than letting them drift apart.
