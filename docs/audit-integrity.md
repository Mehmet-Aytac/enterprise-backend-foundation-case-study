# Audit and Integrity

This document summarizes the audit and integrity model explored in the private enterprise backend foundation prototype.

The design goal was not to claim absolute immutability. The goal was to make important application-level changes traceable and tamper-evident under normal application operation.

## Audit and Security Events

The private prototype separated two related concepts:

- audit logs: records of business or administrative actions
- security events: records of authentication, authorization, credential, token, and suspicious activity events

This separation allows operational audit history and security monitoring to evolve independently.

## Durable Outbox Pattern

Audit and security writes were designed to flow through a durable outbox.

The intended behavior was:

1. the API request performs a business or security-sensitive action
2. the application enqueues an outbox record
3. a worker process materializes that record into the durable audit or security-event table
4. failed processing is retried with bounded backoff
5. unrecoverable records can move to dead-letter state for investigation

This keeps the request path resilient while still preserving security and audit visibility.

## Tamper-Evident Hash Chain

Audit logs were designed with a per-tenant hash-chain strategy.

Conceptually, each new audit entry includes:

- the previous audit entry hash for that tenant
- the canonicalized payload of the new entry
- the computed hash for the new entry

This makes historical modification or deletion detectable when the chain is verified.

## Concurrency Consideration

A key challenge for audit hash chains is concurrent writes.

If two workers append audit entries for the same tenant at the same time, both could read the same previous hash and create a forked chain.

The private prototype handled this by using a tenant-scoped transaction lock during hash-chain append so that each tenant's audit chain is appended deterministically.

## What This Provides

The design provides:

- tamper evidence for audit-log ordering inside a tenant
- detection support if historical audit rows are changed or removed
- deterministic append behavior under concurrent worker execution
- a clear verification command in the private repository
- a better incident-investigation trail for security-sensitive actions

## What This Does Not Provide

This design does not make database storage immutable by itself.

It also does not prove integrity if an attacker controls the database, backups, application code, and every external copy of the audit trail.

For stronger production guarantees, the system would need additional operational controls such as:

- protected backups
- external log export
- SIEM integration
- object-lock storage
- external hash anchoring
- incident response procedures
- periodic verification jobs

## Operational Lessons

The most important audit lesson was to avoid overstating the guarantee.

The correct claim is tamper-evidence, not absolute immutability.

In a hosted SaaS model, the provider can protect audit integrity more strongly because customers do not have direct database access. In a self-hosted or root-access environment, audit guarantees must be described more carefully because infrastructure administrators can potentially modify both data and application behavior.