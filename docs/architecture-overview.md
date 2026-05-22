# Architecture Overview

This document summarizes the conceptual architecture of the private enterprise backend foundation prototype.

The public repository does not include the source code. The goal of this document is to describe the structure, boundaries, and design intent behind the private implementation.

## Design Goal

The private prototype was designed as a shared backend foundation for future business/domain modules.

The foundation focused on platform-level concerns that should not be reimplemented separately by every module:

- authentication
- authorization
- tenant isolation
- audit and security logging
- data classification
- request validation
- error handling
- observability
- deployment readiness
- security regression testing

The goal was to keep future domain modules relatively thin while forcing them to reuse centralized security, tenancy, and audit controls.

## Conceptual Layers

The private implementation was organized around a small number of conceptual layers.

### Core Layer

The core layer contained cross-cutting application behavior:

- authentication and session logic
- access-control models and permission evaluation
- audit and security-event services
- tenant and governance helpers
- middleware for request state, authorization, CSRF, rate limiting, error handling, and content-type enforcement
- data-classification and response-projection helpers
- shared utility functions

This layer represented the security and policy foundation of the application.

### Infrastructure Layer

The infrastructure layer contained runtime adapters and environment-facing concerns:

- environment validation
- database client setup
- logging
- telemetry
- password hashing
- token signing
- cryptographic helpers

The goal was to isolate framework and platform concerns from business modules.

### Module Layer

The module layer exposed API-facing features such as identity, users, roles, permissions, tenants, organizations, sessions, service accounts, audit logs, and security events.

Each module followed the same general shape:

- routes
- controller
- service
- validators
- types

The intended rule was that modules should depend on core and infrastructure, but avoid direct module-to-module shortcuts that could bypass authorization or tenant-boundary controls.

### Worker and Tooling Layer

The private prototype also included non-request-path processes and tools:

- outbox worker for audit/security event materialization
- audit hash-chain verification
- service-account bootstrap tooling
- authentication hot-path benchmark
- concurrent API smoke testing
- OpenAPI contract validation

## Request Pipeline

The request pipeline was designed so that security context is established before business logic executes.

The conceptual order was:

1. initialize request state
2. authenticate session, bearer token, or service-account token
3. resolve authenticated principal
4. build request context
5. build access scope
6. enforce route permission
7. execute controller and service logic
8. write audit/security events when required
9. return projected and classified response data

A key principle was that controllers should not make authorization decisions themselves. Routes declare the required permission, and the permission engine evaluates the request using server-derived facts.

## Domain Module Contract

Future domain modules were expected to follow a strict safety contract:

- validate request input before business logic
- derive tenant ID from authenticated request state, not from request body input
- load resource ownership and dimension facts server-side
- declare explicit route permissions
- use field projection for sensitive response fields
- write audit/security events for high-impact actions
- add OpenAPI schemas for new routes
- add tenant-boundary, authorization, validation, and audit tests before considering a module production-ready

## Why This Structure Matters

The project was intentionally designed around shared enforcement points.

For a multi-tenant business system, the most dangerous failures usually come from inconsistent local decisions:

- one module forgetting tenant scope
- one endpoint trusting client-supplied ownership
- one response leaking sensitive fields
- one route bypassing audit logging
- one service implementing a weaker permission check than the rest of the system

The architecture attempted to reduce those risks by making authentication, authorization, projection, auditing, and validation reusable defaults rather than optional module-specific habits.