# ADR-001: Memory Integration Naming and Contract

## Status

Accepted

## Context

The codebase convention uses `*Backend` for infrastructure adapters. The memory integration was previously discussed as `Mem0Provider` vs `Mem0Backend`.

Beyond naming, production operation needs explicit behavioral guarantees for memory adapters.

## Decision

Use **`Mem0Backend`** and define a backend contract with production requirements:

- strict tenant/user/employee scoping
- policy-gated writes
- deterministic serialization and schema versioning
- audit metadata on writes (`actor`, `reason`, `trace_id`)
- retention/deletion compatibility

## Rationale

### Consistency

`*Backend` aligns with existing integration naming, improving discoverability and predictability.

### Operational Clarity

A backend is not just a transport wrapper. It enforces lifecycle and governance requirements at the persistence boundary.

### Future Flexibility

Using a backend contract allows swapping storage implementations without changing governance semantics.

## Consequences

### Positive

- unified naming and mental model
- easier cross-backend testing and contract checks
- cleaner separation of policy vs storage implementation

### Negative

- requires stronger tests for backend conformance

## Implementation Notes

- File: `libs/deepagents/backends/mem0.py`
- Keep adapter concerns in backend; keep policy decisions in governance layer invoked by backend boundaries.

## Alternatives Considered

- `Mem0Provider`: rejected due to convention drift and weaker operational semantics.
- Direct SDK calls in middleware: rejected due to coupling and poor testability.

