# ADR-001: Use Postgres (and its job queue) instead of DynamoDB + SQS

**Date**: 2025-06-10
**Status**: Accepted
**Deciders**: @backend-team, @jonny

## Context and Problem Statement
We need a data store for widgets and a queue for background jobs (digest emails, inventory sync). The team has limited ops capacity and wants to minimize the number of managed services in the stack.

## Considered Options
- **Option A** — Postgres only, using `SELECT ... FOR UPDATE SKIP LOCKED` as the job queue.
- **Option B** — DynamoDB for data + SQS for jobs.
- **Option C** — Postgres for data + Redis for jobs.

## Decision
Chosen option: **Option A**, because one managed service is cheaper to operate than two, and our expected job throughput (~10k/day) is well within what Postgres can handle as a queue.

We'll revisit this if throughput exceeds ~1M jobs/day or if job latency requirements drop below 1 second.

## Consequences
**Positive:**
- One database to back up, monitor, and reason about.
- Strong transactional consistency between writing a row and enqueuing a job.
- No new service-specific expertise required.

**Negative:**
- Postgres-as-queue is not as observable as SQS/Redis (no dead-letter visibility out of the box).
- Scaling the queue means scaling Postgres, which couples two concerns.
