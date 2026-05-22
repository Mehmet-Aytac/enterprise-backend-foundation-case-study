# Data Classification

This document summarizes the response-minimization and field-projection strategy explored in the private enterprise backend foundation prototype.

## Core Idea

The private prototype did not treat every response field as equally safe.

The design separated route-level authorization from response-level field projection.

A user may be allowed to access a resource, but still not be allowed to see every field on that resource.

## Example Classifications

The private prototype explored classifications such as:

- public: broadly safe fields
- internal: tenant-internal operational data
- performance: productivity, review, or manager-visible metrics
- confidential: sensitive internal attributes or business metadata
- PII: personally identifying or HR-like information
- security-sensitive: token, audit, incident, or security metadata

Restricted classifications were expected to fail closed unless the authenticated principal had explicit grants.

## Why This Matters

A common backend mistake is returning raw ORM objects from controllers.

That can accidentally expose fields such as:

- password hashes
- token hashes
- security metadata
- internal attributes
- employee codes
- PII
- performance data
- tenant governance details

The private prototype used response-shaping and field-projection concepts to avoid this pattern.

## Projection Strategy

The intended strategy was:

1. Route authorization decides whether the principal may access the resource.
2. Response projection decides which fields may be returned.
3. Restricted fields require explicit permissions or grants.
4. OpenAPI response schemas should match the projected response contract.
5. Tests should prove restricted fields are hidden without the grant and visible only with the grant.

## Review Checklist

Before exposing a response field, the private design expected these questions to be answered:

- What classification does this field have?
- Is this field safe by default?
- Which permission or role can read it?
- Can it leak tenant data, PII, HR data, financial data, or security details?
- Does the OpenAPI schema describe the possible shape?
- Is there a test that proves the field is hidden without the grant?

## Portfolio Takeaway

The key lesson is that authorization alone is not enough.

In business applications, especially internal tools and multi-tenant systems, the system also needs response minimization so that broad operational access does not automatically become broad sensitive-data access.