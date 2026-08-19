# Limitations

This case study documents a private, active-development full-stack ERP/SaaS project and its engineering/validation process.

It should not be read as a claim that the private project is a finished commercial product, a certified enterprise platform, an externally audited production system, or a publicly reproducible application.

## Source Code Is Private

The full backend and frontend implementation is not included in this public repository.

Readers therefore cannot independently run the complete application, execute the private test suite, inspect every implementation detail, or reproduce the full validation process from this repository alone.

The public repository summarizes architecture, implementation evidence, review findings, tradeoffs, and limitations. It is not a runnable open-source framework or product release.

## Frontend Is Active Development

The private project now contains a real React frontend workspace and real browser/API integration work, but the frontend is not complete.

Current evidence includes routing, server-state patterns, localization, accessible UI primitives, a project-owned Fetch client, CSRF-aware browser requests, session/bootstrap integration smoke paths, and multiple ERP feature boundaries.

However, the case study does **not** claim:

- complete frontend coverage for every backend module
- complete API-backed behavior for every visible or planned screen
- complete OpenAPI-derived frontend type-generation coverage
- complete Zod-based local validation coverage across all forms
- complete production-grade frontend test coverage
- external accessibility certification

Some surfaces remain adapter-backed, staged, deferred, or under active hardening.

## Validation Is Internal, Not External Certification

The private project has repository-level and local validation evidence, including unit/integration checks, abuse-case scenarios, response-leak checks, concurrency-sensitive validation, audit hash-chain verification, performance smoke checks, dependency review, CI controls, and container/platform-oriented checks.

These checks are meaningful engineering evidence, but they do not replace:

- independent external code review
- external architecture/security review
- compliance certification
- production incident exercises
- long-running operational validation
- realistic production traffic validation
- external frontend accessibility/usability review

## No Real Customer Production Usage Yet

The project is being developed as a reusable ERP/SaaS foundation and a base for future domain or vertical products.

It does not yet prove:

- live customer adoption
- production support maturity
- product-market fit
- real production behavior under customer load
- long-term operational reliability

## Not A Complete End-User ERP Product

The project now has both backend and frontend implementation, so it is no longer accurate to describe it as "backend only." It is still not a complete commercial ERP product.

Broader production readiness would require additional work such as:

- completion and hardening of selected end-user workflows
- broader end-to-end test coverage
- reporting/analytics appropriate to the target product
- onboarding and operational support processes
- deployment-specific runbooks and observability
- real-user feedback loops
- product-specific security and compliance review

The active development plan intentionally defers some broad ERP areas instead of presenting placeholder coverage as finished product functionality.

## Audit Integrity Has Boundaries

The audit model focuses on tamper evidence at the application level.

It does not make database storage immutable by itself. Stronger guarantees would require operational controls such as external anchoring, protected backups, third-party log export, object-lock storage, or equivalent infrastructure controls.

This distinction is especially important in self-hosted environments where infrastructure administrators may have direct access to the database or runtime.

## AI-Assisted Development

This is an AI-assisted engineering case study.

AI tools have been used during generation, implementation support, review, hardening, and documentation. The ownership claim is not that every line was manually authored without assistance.

The ownership claim is that requirements, architecture direction, implementation review, validation execution/interpretation, edge-case analysis, tradeoff decisions, documentation, and hardening direction were actively guided and evaluated rather than accepted blindly.

## Future Work Before Production Claims

Before making stronger production claims, the project would need additional work such as:

- independent external review
- backup and restore drills
- credential/configuration rotation procedures
- production observability dashboards
- incident runbooks
- deployment-specific hardening
- realistic load and endurance testing
- end-to-end browser workflow testing
- accessibility review
- domain-specific threat modeling
- legal/compliance review depending on target industry
- production support and ownership model

## Correct Portfolio Interpretation

The most accurate interpretation is:

> Private-source, active-development full-stack ERP/SaaS platform documented through a public architecture, validation, and engineering case study. The backend foundation is comparatively mature and security-focused; the operational React frontend is actively evolving and increasingly exercises the real backend contracts.

The repository should not be presented as a finished commercial product, externally certified platform, live customer deployment, or public starter framework.
