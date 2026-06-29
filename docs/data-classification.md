# Data Classification

This document summarizes the response-minimization and field-projection strategy explored in the private enterprise backend foundation.

## Core Idea

The private prototype did not treat every response field as equally safe.

Route authorization answers this question:

> May this principal access this resource?

Response projection answers a second question:

> Which fields on this resource may this principal see?

Those are different problems.

A user may be allowed to access a record but still not be allowed to see PII, confidential business data, financial details, performance metrics, security metadata, or internal policy state.

## Example Classifications

The private design explored classifications such as:

| Classification | Meaning | Default direction |
|---|---|---|
| Public | Safe for broad authenticated or public display | Usually visible |
| Internal | Tenant-internal operational data | Visible when the route itself is allowed |
| Performance | Productivity, review, or manager-visible metrics | Hidden without explicit grant |
| Confidential | Sensitive internal attributes or business metadata | Hidden without explicit grant |
| PII | Personally identifying or HR-like information | Hidden without explicit grant |
| Security-sensitive | Audit, incident, session, or policy metadata | Hidden without explicit grant |

Restricted classifications are expected to fail closed unless the authenticated principal has explicit grants.

## Why This Matters

A common backend mistake is returning raw ORM objects from controllers.

That can accidentally expose fields such as:

- internal security metadata
- confidential attributes
- employee identifiers
- PII
- financial details
- performance data
- tenant governance settings
- implementation-specific error details

The private prototype used response-shaping and field-projection concepts to avoid this pattern.

## Projection Strategy

The intended strategy was:

1. Route authorization decides whether the principal may access the resource.
2. Response projection decides which fields may be returned.
3. Restricted fields require explicit permissions or grants.
4. Include groups for sensitive data are authorized request-by-request.
5. OpenAPI response schemas should match the projected response contract.
6. Tests should prove restricted fields are hidden without the grant and visible only with the grant.

## Example

A tenant user may have permission to list users:

```text
users.read
```

That permission may expose safe operational fields such as display name, status, and role summary.

The same user should not automatically receive personal, confidential, security-sensitive, or internal governance fields.

Those fields should require narrower permissions such as:

```text
users.read.pii
users.read.attributes
security-events.read.details
```

The exact permission names are implementation details; the important pattern is that broad route access should not become broad sensitive-field access.

## Review Checklist

Before exposing a response field, the private design expected these questions to be answered:

- What classification does this field have?
- Is this field safe by default?
- Which permission or role can read it?
- Can it leak tenant data, PII, HR data, financial data, security details, or internal governance state?
- Does the OpenAPI schema describe the possible response shape?
- Does a test prove the field is hidden without the grant?
- Does error handling avoid exposing internal implementation details?

## Portfolio Takeaway

The key lesson is that authorization alone is not enough.

In business applications, especially internal tools and multi-tenant systems, the backend also needs response minimization so that broad operational access does not automatically become broad sensitive-data access.
