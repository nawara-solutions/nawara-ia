# IA-ADR-005: IA-owned PostgreSQL, pgvector for RAG when needed, durable jobs only when justified

- **Status:** Proposed
- **Date:** 2026-10-16
- **Deciders:** Nawara IA Principal Architect; repository owner

> This ADR records an architectural decision only. It does not authorize implementation,
> dependencies, infrastructure, deployment or production integration; each requires separate
> owner approval.

## Context

Nawara IA needs to persist its own state: for example usage records, document indexes for
retrieval, and job state. Nawara Core and the products own their own databases. Nawara IA must
not read those databases directly.

## Options considered

1. **Use Core's or products' databases directly**: no new datastore, but couples lifecycles and
   breaks Core's and products' authority over their data.
2. **IA-owned PostgreSQL, with pgvector added when retrieval needs it**: one familiar relational
   store, with vector search inside the same database.
3. **IA-owned PostgreSQL plus a dedicated vector database and job broker from day one**: more
   specialised, but more infrastructure before there is proven need.

## Decision

We chose **Option 2**. Nawara IA owns its own PostgreSQL database and is its only writer. The
pgvector extension is used for retrieval-augmented generation (RAG) when a capability needs it.
A durable job mechanism is introduced only when a workload justifies it, not by default. Data
from Core and products reaches Nawara IA only through their APIs.

**Deletion covers derived data.** When source data is deleted, or a tenant is offboarded, the
data derived from it in Nawara IA (including embeddings, indexes and cached results) must be
deleted or invalidated too.

## Rationale

- Clear data ownership with independent lifecycle (IA-ADR-001).
- Fewer moving parts until scale or features require more.
- Vector search in PostgreSQL keeps tenant scoping in one query model.

## Consequences

- Retrieval at very large scale may later need a dedicated vector store (would need a new ADR).
- Schema migrations need tooling and a review process.
- Nawara IA must learn about source deletions and tenant offboarding through an agreed
  mechanism.
- Introducing durable jobs later requires a short justification and design.

## Security and isolation

- Every table holding tenant data carries the tenant identifier, and every query filters on it.
- Retrieval queries must scope to the caller's tenant before ranking, never after.
- Database credentials are secrets and are never committed.

## Open questions and verification

- Exact deletion workflows and retention periods are deferred.
- How Nawara IA is notified of source deletions and tenant offboarding.
- How tenant scoping is enforced: application layer, row-level security, or both.
- Verify that no code path issues an unscoped query against tenant data.
