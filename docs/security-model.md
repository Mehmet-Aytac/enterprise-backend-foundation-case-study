# Security Model

This document summarizes the security model explored in the private enterprise backend foundation prototype.

It is a case-study summary, not a third-party security audit or certification.

## Security Goals

The private prototype was designed around the following goals:

- prevent cross-tenant data access unless explicitly authorized
- separate authentication from authorization
- make authorization deterministic and deny-by-default
- protect browser cookie sessions from CSRF
- keep sessions revocable and persisted outside the application process
- make token reuse, suspicious login, failed credentials, and security-sensitive lifecycle events visible
- preserve audit durability through an outbox worker
- make audit history tamper-evident at the application level
- avoid trusting client-supplied proxy or IP headers unless a trusted proxy chain is explicitly configured
- minimize sensitive data exposure in responses and logs

## Protected Assets

The private prototype treated the following as protected assets:

- user credentials and password hashes
- session, access, and refresh tokens
- MFA secrets and recovery codes
- tenant data and governance settings
- organization hierarchy and membership assignments
- role, permission, policy, and relationship grants
- audit logs, security events, and outbox records
- PII, confidential attributes, performance-related fields, and security-sensitive metadata

## Primary Actor Types

The design considered several actor types:

- anonymous internet clients
- authenticated tenant users
- tenant administrators
- system administrators
- browser clients using cookie transport
- API/mobile clients using bearer tokens
- service accounts using machine credentials
- outbox worker processes
- database migration processes

## Trust Boundaries

Important trust boundaries included:

- client to Express API
- reverse proxy to application process
- API request path to database
- API request path to audit/security outbox
- outbox worker to durable audit/security tables
- browser cookie transport to CSRF-protected mutation routes
- bearer-token transport to token/session verification

## Major Threats and Controls

### Cross-tenant access

Tenant boundaries were intended to be enforced before business-level permission checks. Tenant-owned queries were expected to use server-derived tenant context rather than tenant IDs supplied by clients.

### CSRF against browser sessions

Browser cookie flows used CSRF protection for mutating requests. Bearer-only API clients were not forced through browser CSRF checks.

### Token replay and refresh-token reuse

Refresh tokens were persisted only as hashes and rotated on use. Reuse attempts were classified so that benign parallel races could be rejected without issuing credentials, while suspicious or repeated reuse could revoke the affected session family.

### Weak or stale sessions

Sessions were DB-backed and revocable. Sensitive operations could require higher session trust through step-up authentication.

### Machine identity confusion

Service accounts were modeled as machine principals, not human users. Human-only routes were expected to reject service-account principals explicitly.

### Relationship overreach

Relationship-based access was designed to be resource-bound. Relation checks needed tenant, subject, relation type, resource type, and resource ID context.

### PII and sensitive-field leakage

Responses were expected to use field projection and classification so that restricted fields fail closed unless explicit grants exist.

### Audit loss or audit inconsistency

Audit and security writes were routed through durable outbox processing. Audit logs used a per-tenant hash-chain append strategy to provide tamper evidence under normal application operation.

## Security Review Rule

A change should be treated as security-breaking if it:

- weakens tenant isolation
- exposes more data by default
- trusts more client input
- weakens credential handling
- bypasses centralized authorization
- removes audit/security visibility
- makes failure modes less explicit

Such changes should require a compensating control and targeted regression tests.