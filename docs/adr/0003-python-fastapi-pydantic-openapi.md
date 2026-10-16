# IA-ADR-003: Python with FastAPI, typed Pydantic contracts and OpenAPI

- **Status:** Proposed
- **Date:** 2026-10-16
- **Deciders:** Nawara IA Principal Architect; repository owner

> This ADR records an architectural decision only. It does not authorize implementation,
> dependencies, infrastructure, deployment or production integration; each requires separate
> owner approval.

## Context

Nawara products consume Nawara IA over the network only. They need stable, documented, typed
contracts. The AI and optimization ecosystem (model SDKs, retrieval libraries, solvers) is
strongest in Python.

## Options considered

1. **Python + FastAPI + Pydantic + OpenAPI**: native async, request and response validation,
   generated OpenAPI specification, and direct access to the Python AI ecosystem.
2. **Python with another web framework**: workable, but contract generation and validation need
   more manual effort.
3. **A non-Python stack** (for example TypeScript or JVM) calling Python only for AI work: matches
   some product stacks, but splits the platform across two runtimes.

## Decision

We chose **Option 1**. The backend is Python using FastAPI. Requests and responses are typed
Pydantic models, and the OpenAPI specification generated from them is the published API
contract. Code is type-annotated, uses a `src/` package layout, and is tested with pytest.

## Rationale

- Contract-first integration without hand-written schemas.
- Validation at the boundary rejects malformed input before it reaches capability code.
- One runtime for the API and the AI and optimization libraries.

## Consequences

- Contract changes are API changes and need versioning discipline.
- Consumers can generate clients from the OpenAPI specification.
- Python performance limits may push heavy work to workers (IA-ADR-002).

## Security and isolation

- Strict schema validation is the first line of defence against malformed or oversized input.
- Contracts must not expose internal identifiers or data from other tenants.

## Open questions and verification

- API versioning and deprecation policy.
- Whether the OpenAPI specification is checked in and verified in CI.
- Exact Python and library versions. Pinning or installing dependencies needs separate
  approval.
