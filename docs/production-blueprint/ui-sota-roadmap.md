# UI SOTA Roadmap (Procedure Builder + Operations Cockpit)

## Product Objective

Transform `deep-agents-ui` from a chat surface into a full AI-organization operating system where teams can:

- build and version agent procedures
- run and supervise workflows
- review approvals and incidents
- inspect traces, memory, and policy outcomes

## Target IA (Pages and Sections)

- `/chat`
  - Conversation
  - Task timeline
  - File/output panel
- `/procedures`
  - Procedure list
  - Visual builder canvas
  - Step inspector
  - Version history + diff
  - Test run panel
- `/agents`
  - Employee directory
  - Capability/risk matrix
  - Delegation graph
- `/governance`
  - Approval queue
  - Policy simulator
  - Kill-switch controls
- `/memory`
  - Entity-scoped browser
  - Class/retention controls
  - Ingestion quality and conflict review
- `/traces`
  - Run explorer
  - Decision timeline
  - Failure clustering
- `/settings`
  - Environment/model routing
  - Budget and quota settings
  - Integrations

## Procedure Builder (Core)

## Supported Step Types

- Prompt step
- Tool step
- Delegation step
- Approval gate step
- Condition/router step
- Memory read/write step
- Retry/backoff step

## Required Builder Features

- Canvas with node/edge editing
- Inspector panel with schema-aware forms
- Live validation against `policy.yaml` and `tool-registry.yaml`
- Draft/staged/active lifecycle
- Version snapshots and side-by-side diffs
- Replay/debug mode with trace overlays

## Data Model (Suggested)

```yaml
procedure:
  id: string
  name: string
  status: draft|staged|active|archived
  owner: string
  version: string
  triggers: []
  nodes: []
  edges: []
  risk_profile:
    highest_tier: T0|T1|T2|T3
    approval_gates: []
  tests: []
```

## Component Map (deep-agents-ui)

- New route files:
  - `src/app/procedures/page.tsx`
  - `src/app/agents/page.tsx`
  - `src/app/governance/page.tsx`
  - `src/app/memory/page.tsx`
  - `src/app/traces/page.tsx`
- New procedure components:
  - `src/app/components/procedures/ProcedureCanvas.tsx`
  - `src/app/components/procedures/ProcedureInspector.tsx`
  - `src/app/components/procedures/ProcedureVersionHistory.tsx`
  - `src/app/components/procedures/ProcedureTestRunner.tsx`
- Shared state/provider additions:
  - `src/providers/ProcedureProvider.tsx`
  - `src/lib/procedures.ts`

## API Surface (Recommended)

- `GET /procedures`
- `POST /procedures`
- `PATCH /procedures/{id}`
- `POST /procedures/{id}/validate`
- `POST /procedures/{id}/test-run`
- `POST /procedures/{id}/promote`
- `GET /procedures/{id}/versions`
- `POST /approvals/{id}/decision`
- `GET /traces?procedure_id=...`

## UX Behaviors That Make It SOTA

- Deep-link every run event to the exact policy decision and tool call.
- Show approval context inline (risk tier, blast radius, rollback option).
- Allow "Edit then Approve" with explicit diff preview.
- Surface memory lineage (which memory influenced this step).
- Show cost/time forecasts before activating a procedure.

## Mobile and Desktop Requirements

- Desktop: multi-pane layout (canvas + inspector + timeline).
- Mobile: section-first navigation with pinned action drawer.
- Preserve continuity: user can start in chat, jump to traces/procedure, then return.

## 3-Phase Delivery Plan

## Phase 1 (2-3 weeks)

- Add nav shell and new routes
- Add read-only procedures list and traces page
- Add approvals inbox page

## Phase 2 (3-4 weeks)

- Add editable procedure canvas
- Add inspector validation and versioning
- Add test-run + replay UX

## Phase 3 (3-4 weeks)

- Add policy simulation, rollout controls, and kill-switch UX
- Add memory lineage visualizations
- Add performance/cost optimization panels

## Exit Criteria

- Non-engineers can create and activate a safe procedure without code edits.
- High-risk actions can be approved/rejected from governance UI with full context.
- Every production action can be traced back to procedure version + policy decision.
