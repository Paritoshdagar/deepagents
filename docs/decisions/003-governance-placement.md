# ADR-003: Governance Is Layered, Not Single-Point

## Status

Accepted

## Context

Earlier design discussions concentrated governance around memory writes and considered removing some runtime protections as "security theater." For production AI organizations, this is insufficient.

Prompt injection, excessive agency, and output-handling failures occur across multiple boundaries, not only memory.

## Decision

Adopt a **layered governance model** with enforcement at four boundaries:

1. input boundary (untrusted data handling)
2. tool boundary (authorization, risk tier, approvals)
3. output boundary (validation, redaction, policy checks)
4. memory boundary (write permissions, data class controls)

Governance remains centralized in policy definitions, but enforcement is distributed at runtime boundaries.

## Rationale

### Defense in Depth

No single control is sufficient against modern LLM attack patterns. Layered controls reduce blast radius when one layer fails.

### Tool-Centric Risk

The highest business risk usually comes from external side effects (writes, payments, deletes), so governance must wrap tool execution explicitly.

### Operational Reality

Residual risk is unavoidable; architecture must support approvals, containment, and incident response, not only prevention.

## Required Controls

- Structured outputs between agents/workflow nodes for safer data flow
- Least-privilege tool scopes per employee
- Human approval for high-risk operations
- Prompt-injection hardening rules for developer/system prompts
- Trace grading and evals tied to release gates
- Immutable audit logs for critical decisions/actions

## Consequences

### Positive

- stronger containment against injection and misuse
- clearer accountability and auditability
- better alignment with production security standards

### Negative

- more implementation complexity
- additional latency for high-risk paths with approval checks

## Implementation Notes

- `GovernanceConfig` stores policy definitions.
- Runtime middleware and tool gateway enforce policy decisions.
- Deny decisions must include machine- and human-readable reason codes.

## Alternatives Considered

- Governance only at memory write path: rejected as incomplete.
- Regex-only sanitizer module as primary defense: rejected as insufficient.
- Unrestricted trusted-employee model: rejected for production risk posture.

