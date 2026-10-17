# Architecture Decision Records (ADR)

An ADR captures one significant, hard-to-reverse architectural decision: the context that
forced it, the options considered, the choice made, and its consequences. Once accepted, an
ADR's substantive decision, rationale and historical context are **immutable** — if a
decision changes later, write a new ADR that **supersedes** the old one rather than editing
it. This keeps a truthful history of _why_ the system looks the way it does.

The superseding ADR records the changed decision. The original ADR may receive only controlled
administrative updates (its status and a cross-reference to its successor) so readers can find
the current decision. Its original decision, rationale and context are never rewritten.

## When to write one

Write an ADR for anything that would be expensive to reverse or confusing to a future
contributor without the reasoning, e.g.:

- Choosing a datastore, message broker, or framework
- Splitting or merging a service
- Adopting a monorepo tool, a testing strategy, an auth strategy
- Any decision recorded in this project's root architecture doc (if it has one) that needs
  the _why_, not just the _what_

Don't write one for reversible, low-stakes choices (e.g. a lint rule, a variable name) — use
a PR description or code comment instead.

## Naming

```
NNNN-short-kebab-title.md
```

Numbers are sequential and never reused, even if an ADR is later superseded or rejected.

Each ADR's canonical ID is `IA-ADR-NNN`, matching its file number (for example `IA-ADR-011` is
`0011-….md`). The ID starts the document heading, `# IA-ADR-NNN: Title`, and is the form used
in every cross-reference and supersession note.

## Workflow

1. Copy `template.md` (from the internal shared standard, when checked out) to `NNNN-short-kebab-title.md` (next sequential number).
2. Fill it in with status `Proposed`.
3. Get it reviewed in the PR that introduces the decision (or the PR that first acts on it).
4. On merge, set status to `Accepted`.
5. If a later decision replaces this one, write the new decision in a new ADR whose context
   names the ADR it supersedes. In the original, change only its status to
   `Superseded by IA-ADR-NNN` and add a link to the successor; leave its decision, rationale
   and context unchanged.

## Index

| ID | Title | Status |
| -- | ----- | ------ |
| [IA-ADR-001](./0001-internal-first-external-ready-multi-tenant-platform.md) | Internal-first, external-ready multi-tenant intelligence platform | Accepted |
| [IA-ADR-002](./0002-modular-python-backend-optional-workers.md) | Modular Python backend with optional independent workers | Accepted |
| [IA-ADR-003](./0003-python-fastapi-pydantic-openapi.md) | Python with FastAPI, typed Pydantic contracts and OpenAPI | Accepted |
| [IA-ADR-004](./0004-local-first-cloud-capable-inference.md) | Local-first, cloud-capable inference with strict provider data policies, controlled fallback and cost limits | Accepted |
| [IA-ADR-005](./0005-ia-owned-postgresql-pgvector-durable-jobs.md) | IA-owned PostgreSQL, pgvector for RAG when needed, durable jobs only when justified | Accepted |
| [IA-ADR-006](./0006-core-authoritative-authorization-tenant-isolation.md) | Core-authoritative hybrid authorization, tenant isolation and fail-closed security | Accepted |
| [IA-ADR-007](./0007-progressive-ai-security-no-autonomous-write-agents.md) | Progressive AI security, privacy, prompt-injection controls and no autonomous write-capable agents in V1 | Accepted |
| [IA-ADR-008](./0008-progressive-hybrid-deployment.md) | Progressive hybrid deployment, local container-based development and independent hosting when justified | Accepted |
| [IA-ADR-009](./0009-opentelemetry-privacy-safe-diagnostics-evaluations.md) | OpenTelemetry, privacy-safe diagnostics, usage accounting and focused evaluations | Accepted |
| [IA-ADR-010](./0010-document-qa-first-mvp.md) | Reusable Document Q&A as the first approved business MVP, built on the AI Gateway | Accepted |
| [IA-ADR-011](./0011-constraint-based-scheduling-optimization.md) | Reusable constraint-based scheduling optimization with product-specific adapters | Accepted |
