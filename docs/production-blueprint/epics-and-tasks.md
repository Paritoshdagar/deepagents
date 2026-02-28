# Epics and Tasks (Implementation-Ready)

## Goal

Deliver a production AI organization platform with bounded autonomy, strong governance, and a SOTA operator/builder UI.

## Dependency Flow

1. Policy + Tool Registry Foundations
2. Runtime Enforcement and Memory Governance
3. Auditability and Incident Controls
4. Eval Gates and Rollout Controls
5. Operator + Procedure Builder UI

## Epic P0: Policy and Registry Foundations

### Outcome

A single source of truth for what AI employees are allowed to do and when humans must approve actions.

### Tasks

- Add policy loader and validator.
  - Path: `libs/deepagents/deepagents/organization/policy.py` (new)
- Add tool registry loader and validator.
  - Path: `libs/deepagents/deepagents/organization/tool_registry.py` (new)
- Add typed policy/runtime context models.
  - Path: `libs/deepagents/deepagents/organization/types.py` (new)
- Wire policy + registry into agent creation.
  - Path: `libs/deepagents/deepagents/graph.py`
- Add config fixtures for tests.
  - Path: `libs/deepagents/tests/fixtures/organization/` (new)

### Acceptance Criteria

- Policy and registry fail fast on invalid schema.
- Agent startup fails closed if policy files cannot load.
- Runtime exposes `org_id`, `employee_id`, risk tier, and approval mode.

## Epic P1: Runtime Tool Authorization and Approval Gates

### Outcome

Every tool call is deterministic: allow, reject, or interrupt-for-review.

### Tasks

- Create governance middleware (pre-tool policy decision point).
  - Path: `libs/deepagents/deepagents/middleware/organization.py` (new)
- Add tool decision engine.
  - Path: `libs/deepagents/deepagents/organization/decision_engine.py` (new)
- Enforce deny-by-default for unregistered tools.
  - Path: `libs/deepagents/deepagents/middleware/filesystem.py`
  - Path: `libs/deepagents/deepagents/middleware/subagents.py`
- Extend `interrupt_on` generation from policy/risk tier.
  - Path: `libs/deepagents/deepagents/graph.py`
- Include reason codes in deny/interrupt payloads.
  - Path: `libs/deepagents/deepagents/organization/decision_engine.py`

### Acceptance Criteria

- T3 tools always interrupt before execution.
- Unregistered tool names are always rejected.
- Decision outputs include `decision`, `reason_code`, `policy_ref`, `trace_id`.

## Epic P2: Governed Memory Backend (Mem0-Ready)

### Outcome

Memory is scoped, auditable, and resilient to memory poisoning and stale context.

### Tasks

- Implement `Mem0Backend` adapter with strict scope keys.
  - Path: `libs/deepagents/deepagents/backends/mem0.py` (new)
- Add memory class model (`policy_memory`, `org_memory`, `user_memory`, `session_state`).
  - Path: `libs/deepagents/deepagents/organization/memory_governance.py` (new)
- Add write guard checks (class mutability, actor rights, data screening).
  - Path: `libs/deepagents/deepagents/organization/memory_governance.py`
- Add retention/expiration support and metadata tagging.
  - Path: `libs/deepagents/deepagents/backends/mem0.py`
- Add optional graph-memory mode and rerank retrieval toggle.
  - Path: `libs/deepagents/deepagents/backends/mem0.py`

### Acceptance Criteria

- Cross-tenant memory access blocked by default.
- Memory writes require `org_id`, `subject_type`, `subject_id`, `source_trace_id`.
- Expiration and metadata filters work for retrieval quality.

## Epic P3: Organization Context and Employee Registry

### Outcome

Employees are defined declaratively and can be governed by role/risk profile.

### Tasks

- Add `EmployeeRegistry` for YAML-backed employee contracts.
  - Path: `libs/deepagents/deepagents/organization/registry.py` (new)
- Add employee schema fields: `capabilities`, `risk_profile`, `memory_scope`, `handoff_rules`.
  - Path: `configs/employees/*.yaml` (new)
- Add org context injection middleware.
  - Path: `libs/deepagents/deepagents/middleware/organization.py`
- Extend subagent state exclusions for org-sensitive keys.
  - Path: `libs/deepagents/deepagents/middleware/subagents.py`

### Acceptance Criteria

- Registry validation rejects employees without risk profile/capability declarations.
- Subagents cannot inherit restricted org context fields.
- Manager only delegates to allowed employees.

## Epic P4: Audit, Traceability, and Incident Controls

### Outcome

Every critical decision and side effect is reconstructable and incident-ready.

### Tasks

- Add normalized audit event model.
  - Path: `libs/deepagents/deepagents/organization/audit.py` (new)
- Emit audit events around tool and memory boundaries.
  - Path: `libs/deepagents/deepagents/middleware/organization.py`
- Add incident kill-switch hooks (global + per-org + per-tool).
  - Path: `libs/deepagents/deepagents/organization/kill_switch.py` (new)
- Add redaction pipeline for sensitive values in logs.
  - Path: `libs/deepagents/deepagents/organization/redaction.py` (new)

### Acceptance Criteria

- High-risk tool actions produce immutable audit records.
- Kill-switch can disable selected tools without redeploy.
- Logs never persist raw credential material.

## Epic P5: Eval and Release Gates

### Outcome

Prompt/model/workflow changes cannot ship without measurable quality and safety checks.

### Tasks

- Add eval harness skeleton for workflow, policy, and safety tests.
  - Path: `libs/deepagents/tests/evals/` (new)
- Add trace grading pipeline config.
  - Path: `docs/production-blueprint/eval-config.md` (new)
- Add CI gate checks with pass/fail thresholds.
  - Path: `.github/workflows/` (update/add)
- Add canary + rollback runbook docs.
  - Path: `docs/production-blueprint/release-runbook.md` (new)

### Acceptance Criteria

- CI fails on critical eval regression.
- Canary promotion requires safety and SLO checks.
- Rollback path is exercised in staging.

## Epic P6: SOTA UI - Operator Console and Procedure Builder

### Outcome

`deep-agents-ui` evolves from chat shell to full AI organization cockpit with deep pages/sections and procedure authoring.

### Tasks (deep-agents-ui)

- Add app-level navigation and route structure.
  - Path: `src/app/layout.tsx`
  - New paths: `src/app/agents/page.tsx`, `src/app/procedures/page.tsx`, `src/app/memory/page.tsx`, `src/app/governance/page.tsx`, `src/app/traces/page.tsx`
- Build procedure editor with step graph + sectioned detail panes.
  - New paths: `src/app/components/procedures/ProcedureCanvas.tsx`, `ProcedureInspector.tsx`, `ProcedureVersions.tsx`
- Add agent directory and capability/risk matrix UI.
  - New paths: `src/app/components/agents/AgentDirectory.tsx`, `AgentDetailsPanel.tsx`
- Add memory browser with filters (entity, class, confidence, expiration, source trace).
  - New paths: `src/app/components/memory/MemoryExplorer.tsx`
- Add governance approvals inbox.
  - New paths: `src/app/components/governance/ApprovalQueue.tsx`
- Add trace timeline for tool calls, interrupts, and decisions.
  - New paths: `src/app/components/traces/TraceTimeline.tsx`
- Preserve current chat while integrating page-level context links.
  - Path: `src/app/components/ChatInterface.tsx`

### Acceptance Criteria

- Users can create, edit, version, and activate procedures from UI.
- Users can review/approve/reject high-risk tool actions from dedicated governance page.
- Users can drill from a chat action to the exact trace and policy decision.

## Epic P7: End-to-End Quality and Security Tests

### Outcome

High-confidence release readiness with behavioral, safety, and UX validation.

### Tasks

- Add policy decision tests.
  - Path: `libs/deepagents/tests/unit_tests/organization/test_policy_engine.py` (new)
- Add HITL flow tests.
  - Path: `libs/deepagents/tests/integration_tests/test_hitl_policy.py` (new)
- Add memory isolation tests.
  - Path: `libs/deepagents/tests/integration_tests/test_memory_isolation.py` (new)
- Add UI e2e tests for procedure builder and approvals.
  - Path: `deep-agents-ui/tests/e2e/` (new)

### Acceptance Criteria

- No high-severity safety test failures.
- E2E covers approve/edit/reject lifecycle and resume behavior.
- Cross-tenant memory and tool access negative tests all pass.

## Epic P8: Pilot Rollout and Operations

### Outcome

Controlled pilot with clear ownership, SLOs, and incident response.

### Tasks

- Create pilot onboarding checklist.
  - Path: `docs/production-blueprint/pilot-checklist.md` (new)
- Define SLO dashboard metrics and alert thresholds.
  - Path: `docs/production-blueprint/slo-metrics.md` (new)
- Create incident response playbook with severity levels.
  - Path: `docs/production-blueprint/incident-playbook.md` (new)

### Acceptance Criteria

- Pilot runs two weeks with no unauthorized T3 actions.
- Incident drill completed for one simulated SEV-1 and one SEV-2 case.
- Weekly reliability/safety review cadence established.

## Fastest Path to Value (Recommended)

1. P0 + P1 + P2 in first release.
2. P4 + P5 before external pilot.
3. P6 in parallel after policy runtime is stable.
