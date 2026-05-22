This is the ReadMe Page of my Enterprize Backend Foundation project. Copied directly because it's very detailed and explains what I mean when I say Foundation.

# Enterprise Backend Foundation (Express 5 + TypeScript + Prisma)

Production-ready, multi-tenant backend template focused on security, auditability, and strict domain boundaries.

## Stack

- TypeScript (strict mode, NodeNext ESM)
- Node.js + Express 5
- Prisma ORM + PostgreSQL
- Zod validation
- Session auth (primary, DB-backed) + JWT access/refresh (secondary)
- RBAC + ABAC + PBAC + ReBAC authorization
- pino-http logging
- express-rate-limit
- Swagger UI + OpenAPI 3.0

## Runtime and Deployment (Cloud-portable)

- Server binds to `0.0.0.0`
- Port is `process.env.PORT` with fallback `3000`
- Stateless runtime (no in-memory session dependency)
- Logs to stdout only
- Runs as a container image for API and worker processes
- Railway, Fly.io, Render, Kubernetes, ECS, and similar platforms are deployment targets; Railway is optional, not hardwired

## Required Environment Variables

Copy `.env.example` to `.env` for local development.

Required:

- `DATABASE_URL`
- `PORT`
- `JWT_ACCESS_SECRET`
- `JWT_REFRESH_SECRET`
- `APP_ENCRYPTION_KEY`
- `NODE_ENV`
- `APP_ENV`

Recommended:

- `CORS_ORIGINS`
- `CORS_CREDENTIALS`
- `JWT_ISSUER`
- `JWT_AUDIENCE`
- `ACCESS_TOKEN_TTL_MINUTES`
- `REFRESH_TOKEN_TTL_DAYS`
- `SESSION_TTL_DAYS`
- `STEP_UP_TTL_MINUTES`
- `LOGIN_MAX_FAILED_ATTEMPTS`
- `LOGIN_LOCKOUT_MINUTES`
- `TRUST_PROXY_HOPS`
- `COOKIE_SECURE`
- `COOKIE_DOMAIN`
- `LOG_LEVEL`
- `OTEL_ENABLED`
- `OTEL_SERVICE_NAME`
- `OTEL_EXPORTER_OTLP_ENDPOINT`
- `API_DOCS_ENABLED`
- `REQUEST_BODY_LIMIT`
- `PASSWORD_RESET_DELIVERY_MODE`
- `PASSWORD_RESET_WEBHOOK_URL`
- `PASSWORD_RESET_WEBHOOK_SECRET`
- `PASSWORD_RESET_WEBHOOK_TIMEOUT_MS`

## Setup

1. Install dependencies:

```bash
npm install
```

2. Generate Prisma client:

```bash
npm run prisma:generate
```

3. Build:

```bash
npm run build
```

4. Run:

```bash
npm start
```

Development:

```bash
npm run dev
```

Worker:

```bash
npm run build
npm run start:worker
```

Container runtime:

```bash
docker compose up --build
```

The compose profile starts PostgreSQL, runs `prisma migrate deploy` for the API container, and starts a separate audit/security outbox worker from the same image.

Production deployment guides:

- [Deployment overview](./docs/deployment/index.md)
- [Railway](./docs/deployment/railway.md)
- [Render](./docs/deployment/render.md)
- [Fly.io](./docs/deployment/fly.md)
- [Kubernetes](./docs/deployment/kubernetes.md)
- [AWS ECS/Fargate](./docs/deployment/aws-ecs.md)
- [Container security and image scanning](./docs/deployment/container-security.md)

Deployment assets:

- `deploy/kubernetes/` contains Kubernetes API, worker, migration, service, autoscaling, and availability manifests.
- `deploy/aws-ecs/` contains ECS/Fargate task and service templates.
- `render.yaml` contains a Render Blueprint for API, worker, and PostgreSQL.
- `deploy/fly/fly.toml` contains a Fly.io process-group configuration for API, worker, and migration release command.

Tests:

```bash
npm test
```

Database-backed integration tests:

```bash
npm run test:integration
```

Performance and concurrency checks:

```bash
npm run perf:auth-hot-path
npm run perf:concurrent-api-smoke
```

Full local verification:

```bash
npm run ci:check
```

GitHub Actions CI now has two layers:

- `verify`: dependency install, Prisma generate/validate, typecheck, lint, format check, OpenAPI validation, tests, npm audit, and build.
- `platform-security`: Docker image build, Docker Compose config validation, Kubernetes manifest render, ECS JSON validation, runtime image contract smoke test, Trivy full report artifact, and Trivy blocking gates for fixable HIGH/CRITICAL image vulnerabilities.
- `integration`: fresh PostgreSQL service, committed migration deploy, seed, integration/security-abuse tests, audit hash-chain verification, hot-path query budgets, and concurrent API smoke.

## Database Provisioning (When DB Becomes Available)

### 1) Configure connection

Set `DATABASE_URL` with managed PostgreSQL. Most production providers require SSL:

```env
DATABASE_URL="postgresql://USER:PASSWORD@HOST:PORT/DBNAME?schema=public&sslmode=require"
```

### 2) Apply checked-in migrations

For an empty local or shared database:

```bash
node node_modules/prisma/build/index.js migrate deploy
```

### 3) Production migration

On production deployments use:

```bash
node node_modules/prisma/build/index.js migrate deploy
```

### 4) Seed data

```bash
npm run db:seed
```

## “Code To Change” For Future DB Connection

You only need env changes in most cases:

- Update `DATABASE_URL` in the target service environment.

If your platform provides separate pooled/direct URLs and you want CLI migrations on direct connection, update:

- [`prisma/schema.prisma`](./prisma/schema.prisma)
  - Add `directUrl = env("DIRECT_URL")` under datasource `db`
- Add `DIRECT_URL` in environment.

No application code change is required for normal managed PostgreSQL usage.

## Request Pipeline (Enforced)

1. Request initialization (`request-state.middleware.ts`)
2. Session authentication (`session.guard.ts`)
3. User resolution (in `session.guard.ts`)
4. Request context build (`request-context.builder.ts`)
5. Access scope build (`access-scope.builder.ts`)
6. Authorization evaluation (`require-permission.middleware.ts` + `permission.engine.ts`)
7. Controller execution (module controllers)
8. Audit/security events (core audit services invoked by modules/middleware)

## Enterprise Capability Overview

This template is intended to be the reusable backend foundation beneath management, monitoring, governance, HR-like, operations, analytics, and future domain-specific systems. It is not a single-purpose API starter; it provides the shared enterprise controls that future modules should consume instead of reimplementing.

Identity and session security:

- DB-backed, device-aware sessions with revocation, logout-all, expiry, suspicious-login metadata, and trust levels.
- Browser cookie auth with `httpOnly` session cookies, secure-cookie configuration, SameSite controls, and mandatory CSRF protection for mutating cookie-auth requests.
- Browser login (`POST /api/v1/identity/login`) is cookie-first and intentionally does not return refresh credentials in the JSON body.
- API/mobile login (`POST /api/v1/identity/token/login`) is token-first and returns access/refresh tokens without setting browser cookies.
- Bearer access tokens for API/mobile/service clients, with issuer, audience, token type, required claims, `jti`, and session binding.
- Refresh JWT rotation backed by hashed persisted token records, replacement chains, token families, adaptive refresh-reuse classification, and controlled revocation.
- Password change and password reset flows with session invalidation, hashed reset tokens, enumeration-safe public responses, and audit/security events.
- TOTP MFA, single-use hashed recovery codes, and time-bounded step-up authentication for sensitive operations.
- Service accounts as first-class machine principals with hashed one-time credentials, explicit permissions, revocation, last-used tracking, and separate audit identity.

Authorization and tenant governance:

- Deterministic deny-by-default authorization through one permission engine.
- RBAC for role-permission assignment.
- ABAC for server-derived subject, resource, request, and scope attributes.
- PBAC for tenant policy rules with deny-overrides and an allow gate when policies exist for a resource/action.
- ReBAC for exact resource-bound relationship checks plus bounded owner-relation constraints for manager/subordinate-style access.
- Tenant isolation is enforced before business access, with service-layer tenant guards so cross-tenant object IDs cannot be smuggled into valid-looking requests.
- Governance settings control enabled dimensions, custom dimensions, and sensitive-session trust enforcement.
- Organization modeling supports company, branch, department, team, region, location/site, and tenant-enabled custom dimensions without forcing every tenant to use every layer.

Data protection and response minimization:

- Zod validation is required before business logic.
- Central error handling returns stable public error codes and safe messages while internal details remain in logs.
- Field projection and data classification prevent accidental exposure of PII, confidential attributes, performance data, and security-sensitive fields.
- OpenAPI response schemas are tied to response-classification contracts so new response DTOs must be documented and classified.
- Logger redaction covers auth headers, cookies, tokens, MFA codes, recovery codes, TOTP enrollment secrets, reset-token material, and one-time service credentials.

Audit, resilience, and observability:

- Audit logs and security events are separate concepts.
- Public audit/security writes enqueue durable outbox records, allowing the request path to remain resilient when worker processing is delayed.
- The worker materializes outbox records with retries, capped exponential backoff, stale-lock recovery, idempotency, and dead-letter behavior.
- Audit logs use canonical payload hashing and per-tenant transactional hash-chain append for tamper evidence under concurrent writes.
- OpenTelemetry support provides traces and low-cardinality metrics for API and worker processes, with log correlation through trace/span IDs.

Platform, CI, and deployment:

- The API process is stateless and horizontally scalable; sessions, tokens, audit events, and outbox state live in PostgreSQL.
- Docker, Docker Compose, Kubernetes, AWS ECS/Fargate, Render, Fly.io, and Railway deployment assets/guides are included.
- Runtime image checks require non-root execution and remove npm/npx from the final image.
- CI validates code quality, OpenAPI drift, migrations, integration behavior, Docker/platform templates, and container vulnerability posture.

## How To Build Business Modules Safely On This Template

Use this rule set for every future domain module.

Long-term module planning lives in [`future-plans/`](./future-plans/). That folder separates shared/common modules, domain modules, schema/table strategy, reusable templates, product opportunities, and tradeoff notes.

1. Create modules under `src/modules/<domain>/` with the same boundary shape: `routes`, `controller`, `service`, `validators`, and `types`.
2. Do not call another module directly. Shared behavior belongs in `core` or `infrastructure`; module-to-module shortcuts create hidden authorization and tenant-isolation bypasses.
3. Validate every input with Zod in the controller layer before calling services.
4. Keep controllers limited to request parsing, response shaping, and delegating to services.
5. Keep business rules in services, but do not put permission decisions in services. Services may enforce tenant-object existence and data consistency; authorization stays in `requirePermission` and the permission engine.
6. Every tenant-owned query must use an explicit tenant filter, preferably through the tenant-scope helper pattern documented in [`docs/tenant-query-safety.md`](./docs/tenant-query-safety.md).
7. Optional module routes must declare `requireTenantModuleEnabled('<module-key>')` before `requirePermission`, so disabled modules fail closed per tenant.
8. Every protected route must declare `requirePermission` with resource type, action, permission key, and server-derived access facts through `resolveAccessContext` when object-level, owner-level, dimension-level, or ABAC checks are relevant.
9. Never trust resource ownership, tenant id, branch/team/department, classification, or relationship facts from the client. Load or derive those facts server-side before authorization.
10. Classify every response DTO. If a field can be PII, confidential, performance-related, security-sensitive, or tenant-governance-sensitive, add a projection rule and a specific permission grant before exposing it.
11. Write audit/security events through the core audit services or outbox-aware service helpers. High-impact mutations should enqueue audit records in the same database transaction as the business change where practical.
12. Add OpenAPI request and response schemas for every route. CI must fail if runtime routes and OpenAPI drift apart.
13. Add tests before marking the module production-ready: tenant-isolation, unauthorized/forbidden access, validation failure, disabled-module access, field projection, audit/outbox behavior, and hot-path query/performance checks.

For a module to be considered enterprise-ready, it must pass the final verification style used by this template: valid user behavior, attacker behavior, response-leak scanning, tenant-boundary attempts, and performance/concurrency smoke tests.

## Authentication Model

- Primary: DB-backed sessions (`Session` model)
- Cookie transport: `sp_session` (httpOnly, secure configurable)
- Cookie-auth mutating requests require CSRF protection (`sp_csrf` cookie + `x-csrf-token` header)
- Secondary: `Authorization: Bearer <access-token>`
- Machine clients: first-class service account bearer tokens with `sa_` prefix, hashed credential storage, and explicit tenant-scoped permissions
- JWT access/refresh tokens verify issuer, audience, token type, and required claims
- Refresh JWT rotation with revocation chain (`RefreshToken` model)
- Refresh token reuse is risk-classified: the first same-session/same-context short-window race is rejected without issuing credentials or revoking the active session, while repeated, post-revocation, cross-context, or delayed replay revokes the affected session family
- Device/IP/User-Agent tracking
- Suspicious login detection for unfamiliar device/location profile
- Password re-authentication step-up temporarily elevates session trust for sensitive operations
- Password changes and password-reset completions revoke every active session for the account
- Password reset requests use enumeration-safe `202 Accepted` responses and hashed, short-lived reset tokens; the HTTP API never returns the reset secret
- Production/staging password reset requires a configured trusted webhook delivery adapter through `PASSWORD_RESET_DELIVERY_MODE=webhook`, `PASSWORD_RESET_WEBHOOK_URL`, and `PASSWORD_RESET_WEBHOOK_SECRET`; local/test may use `development_capture`.
- Password reset webhooks are HMAC-SHA256 signed with `x-password-reset-timestamp` and `x-password-reset-signature`, and abort after `PASSWORD_RESET_WEBHOOK_TIMEOUT_MS`.
- Repeated failed credential checks create tenant-scoped account lockout state
- Logout and logout-all support

Refresh token strategy is single-path and consistent:

- Refresh JWT is issued by `token.service.ts`
- Only hashed refresh JWT values are persisted in `RefreshToken`
- Session rows do not store a parallel/dead refresh hash
- Old refresh tokens never produce new credentials after rotation
- Reuse attempts are recorded in `RefreshTokenReuseAttempt` with the classification `BENIGN_ROTATION_RACE`, `REPEATED_RACE`, `SUSPICIOUS_REPLAY`, or `REVOKED_TOKEN_REUSE`
- Benign rotation races return `REFRESH_TOKEN_ALREADY_ROTATED` so clients can continue with the successful concurrent refresh result
- Suspicious or repeated reuse emits security events and revokes the affected refresh-token family/session

## Authorization Model

- Deny-by-default engine (`permission.engine.ts`)
- RBAC: role-permission mapping
- ABAC: user/resource attributes
- PBAC: tenant policy rules (`PolicyRule` model)
- ReBAC: explicit subject-resource relations (`ResourceRelation` model)
- Tenant boundary checks enforced first
- Session trust-level checks for sensitive actions
- Service accounts are separate principals, not users, and human-only operations reject them explicitly
- Service account rules are documented in [`docs/service-accounts.md`](./docs/service-accounts.md).
- TOTP MFA enrollment and enforcement are documented in [`docs/mfa.md`](./docs/mfa.md).
- WebAuthn/passkey adapter rules are documented in [`docs/webauthn.md`](./docs/webauthn.md).
- Password reset and account recovery rules are documented in [`docs/account-recovery.md`](./docs/account-recovery.md).

ReBAC checks are resource-bound:

- Relation checks require `resourceId` when `requiredRelations` are configured.
- Authorization verifies relation + `resourceType` + `resourceId` exact match.
- Bounded owner-relation checks are grant-driven through `scopeConstraints.requiredOwnerRelations` and require routes to supply an explicit `resourceOwnerUserId`, supporting manager/subordinate access without broad graph traversal.
- ABAC condition rules are documented in [`docs/abac-policy.md`](./docs/abac-policy.md).

## Data Classification

- Field projection rules are documented in [`docs/data-classification.md`](./docs/data-classification.md).
- Restricted fields such as PII, confidential attributes, performance data, and security-sensitive metadata fail closed unless explicit grants exist.

## Audit and Security Logging

- `AuditLog` captures actor, entity, action, old/new values, request metadata
- Hash chaining (`hash-chain.service.ts`) provides tamper-evidence support
- `SecurityEvent` tracks authentication/authorization/security lifecycle events
- Public audit/security write APIs enqueue durable `AuditOutbox` records first.
- `npm run start:worker` dispatches outbox records into audit/security tables with retry and dead-letter handling.
- Outbox retry count, exponential retry delay, stale lock timeout, worker poll interval, and dispatch batch size are configurable with `OUTBOX_*` environment variables.
- Hash-chain guarantees and recovery guidance are documented in [`docs/audit-hash-chain.md`](./docs/audit-hash-chain.md).

## Observability

- OpenTelemetry can be enabled with `OTEL_ENABLED=true`.
- Production telemetry requires `OTEL_EXPORTER_OTLP_ENDPOINT`.
- API and worker bootstrap telemetry before loading instrumented internals.
- Pino logs include active `traceId` and `spanId` when a span is active.
- Logs remain stdout-only and platform friendly.
- Vendor-neutral collector guidance is documented in [`docs/observability.md`](./docs/observability.md).

## Seed Data Design

`prisma/seed.ts` creates three tenant patterns:

1. `simple-co`: branch enabled, no department/team
2. `full-corp`: full hierarchy (company + branch + department + team + region + location)
3. `advanced-enterprise`: full hierarchy + custom dimensions (`clearance-domain`, `project-cell`)

Seed includes:

- users, memberships, roles, permissions
- role-permission mappings with scope constraints
- organization units + user assignments
- relationship graph samples for ReBAC
- tenant policy rules for PBAC
- baseline audit/security events

Default seeded password for all users:

- `VeryStrongPassword!123`

## OpenAPI / Swagger

- JSON: `/openapi.json`
- Swagger UI: `/docs`
- Source file: `docs/openapi.json`
- `API_DOCS_ENABLED` defaults to `false` in production and `true` outside production.

## File Responsibilities and Relations

### Entry Layer

- `src/index.ts`: process startup, DB connect, graceful shutdown, signal handling.
- `src/app.ts`: Express app assembly, middleware order, route registration, docs/health.

### Infrastructure Layer

- `src/infrastructure/config/env.ts`: validated environment configuration.
- `src/infrastructure/database/prisma.ts`: Prisma client singleton and DB lifecycle helpers.
- `src/infrastructure/logging/logger.ts`: pino logger config with sensitive field redaction.
- `src/infrastructure/observability/telemetry.ts`: OpenTelemetry bootstrap and shutdown.
- `src/infrastructure/security/password-hash.ts`: bcrypt hash/verify adapter.
- `src/infrastructure/security/token-signing.ts`: JWT sign/verify isolation.
- `src/infrastructure/security/security-context.ts`: cryptographic token/jti helpers.

### Core Auth

- `src/core/auth/types/app-user.ts`: normalized authenticated principal shape.
- `src/core/auth/types/auth-snapshot.ts`: resolved per-request auth graph snapshot reused in pipeline.
- `src/core/auth/mappers/user.mapper.ts`: DB membership -> `AppUser` mapper.
- `src/core/auth/services/token.service.ts`: access/refresh token lifecycle.
- `src/core/auth/services/session.service.ts`: DB session lifecycle, suspicious detection, rotation, revocation.
- `src/core/auth/services/account-security.service.ts`: tenant-scoped failed credential tracking and lockout policy.
- `src/core/auth/services/service-account.service.ts`: service account bearer credential verification.
- `src/core/auth/guards/session.guard.ts`: session/token verification + user resolution.

### Core Access Control

- `src/core/access-control/models/access-context.ts`: permission check input model.
- `src/core/access-control/models/request-context.ts`: request metadata model for decisions.
- `src/core/access-control/models/access-scope.ts`: computed scope (roles, permissions, dimensions, policies, resource relations).
- `src/core/access-control/builders/request-context.builder.ts`: request -> context transform.
- `src/core/access-control/builders/access-scope.builder.ts`: user/auth snapshot -> scope transform.
- `src/core/access-control/rules/rbac.rules.ts`: RBAC checks and scope constraints.
- `src/core/access-control/rules/abac.rules.ts`: attribute checks.
- `src/core/access-control/rules/rebac.rules.ts`: relationship checks.
- `src/core/access-control/rules/deny.rules.ts`: tenant/session-trust deny guards.
- `src/core/access-control/policies/policy.types.ts`: policy interfaces.
- `src/core/access-control/policies/policy.registry.ts`: policy registration + condition evaluation.
- `src/core/access-control/engine/permission.engine.ts`: deterministic final authorization decision.

### Core Governance

- `src/core/governance/constants/governance.constants.ts`: defaults/dimension constants.
- `src/core/governance/models/dimension-config.ts`: dimension config contract.
- `src/core/governance/models/tenant-policy.ts`: tenant governance policy contract.
- `src/core/governance/services/tenant-policy.service.ts`: tenant policy resolution.
- `src/core/governance/services/dimension-config.service.ts`: dimension config operations.
- `src/core/governance/services/admin-governance.service.ts`: governance admin mutations.

### Core Audit/Security

- `src/core/audit/constants/audit.constants.ts`: canonical audit action keys.
- `src/core/audit/models/audit-log-entry.ts`: audit write contract.
- `src/core/audit/models/security-event.ts`: security event write contract.
- `src/core/audit/services/hash-chain.service.ts`: audit hash-chain compute.
- `src/core/audit/services/audit-log.service.ts`: persistent audit logging.
- `src/core/audit/services/security-event.service.ts`: persistent security events.
- `src/core/audit/services/audit-outbox.service.ts`: durable audit/security outbox enqueue.
- `src/core/audit/services/audit-outbox-dispatcher.service.ts`: outbox dispatch, retry, and dead-letter processing.

### Core Middleware

- `src/core/middleware/request-state.middleware.ts`: request init/context/scope pipeline middlewares.
- `src/core/middleware/rate-limit.middleware.ts`: rate limiter factory.
- `src/core/middleware/require-permission.middleware.ts`: authorization enforcement middleware.
- `src/core/middleware/not-found.middleware.ts`: 404 handling.
- `src/core/middleware/error.middleware.ts`: centralized typed error handling.

### Core Types/Utils

- `src/core/types/app-error.ts`: typed application error.
- `src/core/types/request-state.ts`: per-request state contract.
- `src/core/types/result.ts`: generic result type.
- `src/core/utils/async-handler.ts`: async route wrapper.
- `src/core/utils/crypto.ts`: hashing/safe-compare helpers.
- `src/core/utils/time.ts`: date/time helpers.
- `src/core/utils/ids.ts`: request ID generator.

### Modules (Domain Entry Points)

Each module follows: `routes -> controller -> service`, with `validators` and `types`.

- Identity: `src/modules/identity/*`
- Users: `src/modules/users/*`
- Roles: `src/modules/roles/*`
- Permissions: `src/modules/permissions/*`
- Tenants: `src/modules/tenants/*`
- Organizations: `src/modules/organizations/*`
- Sessions: `src/modules/sessions/*`
- Service Accounts: `src/modules/service-accounts/*`
- Audit Logs: `src/modules/audit-logs/*`
- Security Events: `src/modules/security-events/*`

Relation rule: modules depend on core + infrastructure; modules do not call each other directly.

Authorization fact rule: future modules must provide precise server-derived facts through
`resolveAccessContext` before calling `requirePermission`. Object operations should set
`resourceId`, owner/manager-style grants should set `resourceOwnerUserId`, org-scoped
data should set `resourceDimensionCodes`, and ABAC inputs should be supplied through
trusted `resourceAttributes`. Do not hard-code relationship requirements in routes; encode
them in permission scope constraints or tenant policies so broad administrative grants and
relationship-bound grants can coexist safely.

### Framework Augmentation

- `src/types/express.d.ts`: extends Express request with `user` and `state`.

### Database Layer

- `prisma/schema.prisma`: complete multi-tenant relational schema and enums.
- `prisma/seed.ts`: full deterministic seed for isolation/auth/governance tests.

### API Documentation

- `docs/openapi.json`: OpenAPI 3.0 document served via `/openapi.json` and `/docs`.

## Security Notes

- Baseline assumptions and enterprise profiles are documented in [`docs/threat-model.md`](./docs/threat-model.md).
- Secrets are env-only; no hardcoded credentials.
- Cookie transport is httpOnly and secure configurable.
- Credentialed production CORS requires an explicit origin allowlist.
- Security headers are enabled with Helmet, and mutating API requests with a body must use `application/json`.
- API docs are disabled by default in production unless `API_DOCS_ENABLED=true`.
- JSON request body limit is explicit through `REQUEST_BODY_LIMIT`.
- Future upload/import modules must not raise the global limit. Mount those routes with
  `createJsonBodyParser(<route-specific-limit>)` from `src/core/middleware/body-parser.middleware.ts` before the
  default parser, and protect them with stricter route-level rate limits and permissions.
- Proxy trust is explicit via `TRUST_PROXY_HOPS` (default `0`, no forwarded-header trust).
- `x-forwarded-for` is retained only as diagnostic metadata unless Express trusted proxy hops are configured.
- Request state records the trusted client IP source plus malformed forwarded-header entries for security review.
- Deployment proxy guidance is documented in [`docs/proxy-trust.md`](./docs/proxy-trust.md).
- Cookie-auth browser mutations require CSRF token and trusted Origin/Referer checks.
- Password-reset token delivery is intentionally out-of-band; wire a trusted notification adapter to the service-layer reset token, never log plaintext reset tokens.
- Credential and password-reset routes have stricter route-level throttles in addition to the global API rate limit.
- Access denied and failed auth actions are logged to security and audit streams.
- Authorization is explicit and centralized.
- Tenant query safety and optional PostgreSQL RLS guidance are documented in [`docs/tenant-query-safety.md`](./docs/tenant-query-safety.md).
- `package.json` uses a narrow dependency override to force `@opentelemetry/otlp-transformer` onto
  `protobufjs@8.2.0`, closing the current protobufjs advisory chain while keeping OpenTelemetry on the current
  runtime line. Keep this override until upstream OpenTelemetry no longer needs it, and verify removal with
  `npm audit --omit=dev` plus Trivy image scans.

## Validation Status

- `npm run build` passes (Prisma generate + TypeScript compile).
- `npm test` passes (authorization and security-focused unit tests).
- `npm run ci:check` runs Prisma validation, typecheck, lint, OpenAPI validation, tests, and production dependency audit.
- `npm run test:integration` runs database-backed integration and security-abuse tests.
- `npm run perf:auth-hot-path` verifies query-count budgets for authenticated hot paths.
- `npm run perf:concurrent-api-smoke` verifies a local concurrent read/write/auth-abuse baseline.
- Final acceptance methodology and latest result categories are documented in
  [`docs/final-enterprise-verification.md`](./docs/final-enterprise-verification.md).
