# Architecture choices for Next.js + NestJS + Prisma

Use these as decision prompts. Match the existing repository before introducing a new package, layer, or service.

## Application shape

- A separate Next.js web app and NestJS API makes sense when mobile or other clients need the API, independent deployment is useful, or the repository already has that split. A smaller project may use a simpler structure.
- A shared package earns its place when web and API must use the same pure rules, types, or validators. Keep it independent of browser, HTTP, and database code. Avoid copying business rules across apps.
- Put tenant or client differences in configuration or data only when the product actually serves multiple clients. Decide whether each difference is build-time, deployment-time, or editable at runtime; never hard-code a client name throughout components.

## Authentication and authorization

- Start protected routes from a deny-by-default posture. Make public access explicit and test role boundaries.
- Define the role hierarchy and authorization source for this product. Do not assume a fixed set of roles or trust an identity-provider claim as an application role without a deliberate mapping.
- For cookie sessions, use secure, HttpOnly cookies and CSRF protection on writes. Revoke affected sessions when credentials or privileges change, according to the product's policy.
- Rate-limit abuse-prone public routes. Configure proxy trust to reflect the actual network path so client identity cannot be forged.

## Data and workflows

- Put persistent invariants in database constraints as well as boundary validation. Use transactions for writes that must succeed or fail together; choose an isolation level and retry strategy when races matter.
- If approved content must stay live while edits await review, use a published read model and immutable revisions. For simpler content, a single row may be enough.
- If values change without a deploy, store them as reference data. Use stable IDs in relationships; define rename, alias, and inactive-value behavior when imports or historical records depend on names.
- For stateful workflows, centralize allowed transitions and the service that performs them. Record an audit history when the domain requires it. Dispatch side effects after the transaction commits.
- Choose polling, server-sent events, or another update mechanism based on latency, scale, and replay needs; do not add live infrastructure solely because a screen changes over time.

## Public reads and search

- Centralize visibility rules when several queries must agree on what is public. Keep user-specific fields out of cacheable public responses. Return consistent responses for inaccessible resources when revealing their existence would be sensitive.
- Treat calendar dates in the product's configured time zone.
- For multilingual search, define one normalization function used by indexing and querying, bound input size and token count, and test representative language cases. Arabic letter folding is one possible rule, not a default for every product.

## Bilingual content

- Decide which data is translated: interface strings, taxonomy labels, user-authored content, and SEO metadata may have different ownership and fallback rules. Keep locale and direction separate from identity and authorization.
- Store translated fields in a shape that supports the product's editing and querying needs (for example, a translation table keyed by entity and locale). Define a stable fallback when one translation is missing; do not silently replace user-authored content if that would mislead readers.
- Pass an explicit locale through web rendering and API reads where localized content is returned. Keep canonical IDs and business rules locale-independent.

## Imports and external systems

- For consequential imports, preview and validate before committing. Revalidate the exact input on confirmation, make retries safe, and test with a pinned representative fixture.
- Keep vendor APIs behind an adapter with a narrow internal type. Respect authoritative source values, surface discrepancies, and use idempotency keys or stable external IDs where retries are possible.
- Validate administrator-supplied network destinations before connecting; define whether private addresses are permitted in the deployment.
