# IA-ADR-010: Reusable Document Q&A as the first approved business MVP, built on the AI Gateway

- **Status:** Accepted
- **Date:** 2026-10-16
- **Deciders:** Nawara IA Principal Architect; repository owner

> This ADR records an architectural decision only. It does not authorize implementation,
> dependencies, infrastructure, deployment or production integration; each requires separate
> owner approval.

## Context

The platform needs a first capability that delivers value to more than one product and
exercises its core foundations: tenant isolation, the AI gateway, retrieval, privacy-safe
diagnostics and evaluations. Answering questions over a tenant's own documents is useful to
several Nawara products.

## Options considered

1. **Product-specific feature first**: fast value for one product, but does not prove
   reusability.
2. **Scheduling optimization first**: high value, but does not exercise the language-model and
   retrieval path (see IA-ADR-011).
3. **Reusable Document Q&A first, built on the AI gateway**: reusable across products and
   exercises most foundations.

## Decision

We chose **Option 3**. The first approved business MVP is reusable Document Q&A: answering
questions over documents the caller is allowed to access.

Document Q&A is **built on reusable capabilities, not a self-contained feature**:

- a reusable **knowledge/retrieval** capability for indexing and searching documents
  (IA-ADR-005), and
- **inference through the AI gateway** (IA-ADR-004).

Both stay usable by later capabilities. Document Q&A is product-agnostic; products supply
documents and present answers.

**Answers are grounded and cited.** A factual answer based on documents must cite the source
documents it is grounded in. How citations are represented, how the capability behaves when no
supported answer exists, and the detailed evaluation criteria are deferred to the Document Q&A
ADD/SDD.

**Authorization uses both layers of IA-ADR-006.** Core authorization and the product's own
document and resource permissions must both be respected. Access to a product's documents
cannot be assumed to follow from Core credentials alone.

**The ingestion mechanism is not decided** (for example whether products push documents or
Nawara IA retrieves them through product APIs).

## Rationale

- Proves the shared foundations with one capability.
- Reusable by several products from the start.

## Consequences

- The AI gateway and the knowledge/retrieval capability are prerequisites.
- Detailed scope (formats, citations, limits) belongs in an ADD/SDD, not in this ADR.

## Security and isolation

- Retrieval is restricted to the caller's tenant and to documents the caller may access under
  both Core and product permissions.
- Document content is untrusted input for prompt-injection purposes (IA-ADR-007).
- Answers must not include content from documents the caller cannot access.
- Changes to document permissions must take effect for retrieval (see IA-ADR-006 on
  revocation-sensitive decisions).

## Open questions and verification

- Ingestion mechanism and how document permissions reach Nawara IA.
- How permission changes and document deletions propagate to indexes (IA-ADR-005).
- Supported document types and size limits.
- Citation representation, unsupported-answer behaviour and evaluation criteria for answer
  quality and citation accuracy (Document Q&A ADD/SDD).
