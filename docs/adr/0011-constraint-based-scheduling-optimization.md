# IA-ADR-011: Reusable constraint-based scheduling optimization with product-specific adapters

- **Status:** Proposed
- **Date:** 2026-10-16
- **Deciders:** Nawara IA Principal Architect; repository owner

> This ADR records an architectural decision only. It does not authorize implementation,
> dependencies, infrastructure, deployment or production integration; each requires separate
> owner approval.

## Context

Several Nawara products need to build schedules under constraints. For example, Nawara Drive
(a driving-school product) assigns lessons to instructors and vehicles, and a daycare assigns
staff to groups and shifts. Each product has its own vocabulary and rules, but the underlying
problem (assign resources to time slots under hard constraints while optimising preferences)
is shared. Schedules affect real people, so results must be explainable and must not be
applied without human approval.

## Options considered

1. **Each product builds its own scheduler**: full product control, but duplicated solver work
   and inconsistent quality.
2. **Language-model-generated schedules**: flexible, but cannot guarantee hard constraints.
3. **Shared constraint-based optimization with product adapters**: a common problem contract
   and bounded solving in Nawara IA; product-specific translation in adapters; approval and
   commit stay in the product.

## Decision

We chose **Option 3**. Nawara IA provides a reusable scheduling optimization capability. The
following are architectural invariants.

1. **Product-neutral contract.** A reusable contract describes resources, tasks to schedule,
   time slots, hard constraints and soft objectives. Product terms do not appear in it.
2. **Product-owned adapters.** Each product owns the adapter that translates its domain data
   into the contract and translates proposals back. Adapter logic belongs in that product's
   repository, not in the shared solver.
3. **Hard constraints are never violated.** Soft objectives are preferences to optimise. A
   proposal that breaks a hard constraint is never returned.
4. **Optional and unscheduled tasks.** Where the contract allows a task to be optional, a
   proposal may leave it unscheduled, and must report it as unscheduled rather than hide it.
5. **Bounded execution and cancellation.** Every solve runs within explicit limits and can be
   cancelled.
6. **Accurate result status.** Results distinguish at least **optimal** (proven optimal),
   **feasible** (satisfies all hard constraints, optimality not proven), **infeasible**
   (proven that no proposal satisfies the hard constraints) and **unresolved** (no conclusion
   within the limits, or cancelled). A feasible result is never presented as optimal.
7. **Structured, evidence-based explanations.** Results include structured explanations
   grounded only in reliable evidence from the problem, constraint validation or solver
   diagnostics: for example, constraints shown to conflict, tasks left unscheduled and why, and
   soft objectives that were traded off. A complete diagnosis of the conflicting constraints is
   not guaranteed for every infeasible problem. When no reliable explanation can be
   established, the result says so and does not invent causes.
8. **Snapshot versioning and stale-proposal rejection.** Each problem is built from a versioned
   snapshot of the product's source data. A proposal records its snapshot version, and a
   proposal based on an outdated snapshot is rejected, not committed.
9. **Product-side approval and validated commits.** Nawara IA only proposes. A person approves
   the proposal in the product. The product independently re-validates it against current data
   and commits it in its own transaction. Nawara IA never writes to product data (consistent
   with IA-ADR-007).
10. **Tenant isolation.** Each problem belongs to exactly one tenant (IA-ADR-006).
11. **Replaceable solver.** The contract does not depend on any one solver's features.
    **OR-Tools CP-SAT is an initial candidate that requires benchmarks**, not a chosen or
    universally certified solver.
12. **Synthetic cross-product validation.** The common contract is validated with synthetic
    examples from at least Nawara Drive and Daycare before it is declared stable. No real
    customer data is used.

**No LLM dependency.** Scheduling optimization does not depend on a language model. Optional
future natural-language assistance (for example helping a user express preferences) may use
the AI gateway (IA-ADR-004), but it cannot override validated constraints and cannot commit
bookings directly.

### Deferred to Scheduling SDD

The following are deliberately not decided here:

- exact request and response schemas, including the explanation schema
- the solve state machine and its transitions
- time and resource limits
- solver choice confirmation and solver parameter settings
- algorithms for weighting soft objectives
- snapshot version format and the stale-check protocol
- whether solves run in workers (IA-ADR-002) or durable jobs (IA-ADR-005)

## Rationale

- Constraint solvers can guarantee hard constraints, which language models cannot.
- Bounded solving with accurate status keeps results predictable and honest.
- Product-owned approval and commit keep products responsible for their own data.

## Consequences

- Products must build and maintain adapters.
- Modelling real rules as constraints takes work and domain input from each product.
- Large problems may only reach "feasible" or "unresolved" within limits, and users must
  understand the difference.
- A Scheduling SDD is required before implementation.

## Security and isolation

- Snapshots, proposals and explanations are never shared across tenants.
- Solver limits protect the platform from resource exhaustion by oversized problems.
- Explanations contain only data from the caller's own problem.

## Open questions and verification

- Benchmark CP-SAT (and alternatives if needed) for correctness and performance on the
  synthetic examples.
- Solvers are not assumed to be deterministic for identical inputs. If reproducibility becomes
  a requirement, establish how to achieve it and verify it.
- How snapshot versions are agreed between adapters and products.
- Adding a solver dependency needs separate approval.
