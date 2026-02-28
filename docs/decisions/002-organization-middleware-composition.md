# ADR-002: Organization Middleware Uses Composition

## Status

Accepted

## Context

Organization features require additional behavior around context loading, policy evaluation, and memory access. Extending `SubAgentMiddleware` directly increases coupling and blurs responsibilities.

## Decision

Use **composition**:

- `OrganizationContextMiddleware` wraps `SubAgentMiddleware`
- organization concerns execute before/after subagent delegation
- `SubAgentMiddleware` remains focused on delegation lifecycle

## Rationale

### Separation of Concerns

- Organization middleware: context, policy hooks, org-aware controls
- Subagent middleware: delegation mechanics and state handoff

### Safer Change Surface

Composition lets us evolve organization controls without destabilizing delegation internals.

### Better Policy Insertion Points

Pre/post wrappers provide clear points for:

- policy checks before tool access
- output validation before return
- audit emission around every delegated step

### Testability

Both middleware units can be tested independently using mocks and contract fixtures.

## Consequences

### Positive

- lower coupling
- clearer middleware responsibilities
- easier to add/reorder safety and observability middleware

### Negative

- slightly more wiring code

## Implementation Notes

- File: `libs/deepagents/middleware/organization.py`
- Keep explicit interfaces for `load_context`, `apply_policy`, and `emit_audit_events`.

## Alternatives Considered

- Inheritance (`class OrganizationMiddleware(SubAgentMiddleware)`): rejected for coupling and SRP violations.
- Merge everything into one middleware: rejected for maintainability and risk isolation concerns.

