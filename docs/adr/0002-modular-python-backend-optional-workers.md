# IA-ADR-002: Modular Python backend with optional independent workers

- **Status:** Accepted
- **Date:** 2026-10-16
- **Deciders:** Nawara IA Principal Architect; repository owner

> This ADR records an architectural decision only. It does not authorize implementation,
> dependencies, infrastructure, deployment or production integration; each requires separate
> owner approval.

## Context

Nawara IA will host several distinct capabilities (for example an AI gateway, knowledge
retrieval, document Q&A and scheduling optimization). Some work is request/response; some,
such as document ingestion or long-running solves, may be slow or resource-heavy. The team is
small, and the platform has no production load history yet.

## Options considered

1. **Microservices from the start**: one deployable per capability. Strong isolation, but high
   operational overhead and premature boundaries before the domain is understood.
2. **Unstructured monolith**: simplest start, but boundaries erode and later extraction is
   hard.
3. **Modular monolith with optional workers**: one backend with explicit internal module
   boundaries; slow or heavy work may run in separately deployable worker processes, when
   justified.

## Decision

We chose **Option 3**. Nawara IA is a modular Python backend. Each capability is a module with
an explicit public interface; modules do not reach into each other's internals or data.
Independent worker processes may be introduced for workloads that need them. Splitting any
module into a separate service requires its own ADR.

## Rationale

- Keeps operational cost proportional to current scale.
- Explicit module boundaries keep a later split possible without committing to it now.
- Workers address the real reason for separation (slow or heavy work) without a network split.

## Consequences

- Module boundaries must be enforced by code review and, where practical, tooling.
- Shared infrastructure code needs clear ownership to avoid becoming a dumping ground.
- One deployable means capabilities share a release cadence until a split is approved.

## Security and isolation

- Tenant and product context (IA-ADR-001, IA-ADR-006) must flow across module and worker
  boundaries unchanged. A worker never runs a job without the tenant scope that created it.

## Open questions and verification

- How module boundaries will be checked automatically (for example import rules).
- Which first workload, if any, justifies a worker process.
- Whether workers share the backend's codebase and packaging.
