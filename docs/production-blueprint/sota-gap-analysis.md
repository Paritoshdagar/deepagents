# SOTA Gap Analysis and Market-Leading Roadmap

## Scope Reviewed

- `deepagents` runtime and middleware stack
- `deep-agents-ui` current chat UI
- `mem0` capabilities and docs
- `Claude_Skills` patterns (hooks, continuous learning, security)
- Current external best-practice guidance

## What You Already Have (Strong Base)

- High-quality agent runtime primitives in `deepagents`:
  - planning/todos
  - filesystem + execute tools
  - subagent delegation
  - memory/skills middleware
  - existing HITL hooks via interrupts
- A functional operator chat UI in `deep-agents-ui`:
  - threads, interrupts, tool approval UI, files/tasks sidebars
- Memory ecosystem headroom from `mem0`:
  - graph memory option
  - metadata filtering
  - reranker support
  - ingestion controls and expiration

## Critical Gaps to Close for Market-Leading Product

## 1) Policy Runtime Is Not Yet First-Class

### Gap

No unified, explicit policy engine tying tool registry, risk tiers, and approval behavior.

### Why It Matters

Market leaders enforce deterministic decisions at runtime, not prompt-only controls.

### Add

- `policy.yaml` + `tool-registry.yaml` as runtime contracts.
- Deny-by-default tool authorization.
- Reason-coded allow/deny/interrupt decisions.

## 2) Governed Multi-Tenant Memory Is Under-Specified

### Gap

Memory governance is documented but not yet codified in runtime contracts and tests.

### Why It Matters

Memory poisoning and cross-tenant leakage are top enterprise blockers.

### Add

- Mandatory scope tuple (`org_id`, `subject_type`, `subject_id`, `source_trace_id`).
- Class-based memory permissions + retention.
- Optional mem0 graph retrieval and rerank for relationship-heavy tasks.

## 3) Eval Flywheel Needs Hard CI Gates

### Gap

Plans mention evals but no strict gate sequence tied to release decisions.

### Why It Matters

Without gates, regressions ship silently.

### Add

- Workflow, safety, and policy eval suites in CI.
- Trace grading thresholds for critical workflows.
- Canary/rollback criteria as mandatory promotion controls.

## 4) Operator UX Is Good But Not Yet SOTA

### Gap

Current UI is a strong chat shell but not a full builder/operations cockpit.

### Why It Matters

Market-leading products let teams design, inspect, govern, and optimize agents across dedicated pages.

### Add

- **Procedures** page: visual builder for multi-step agent workflows.
- **Agents** page: capability/risk matrix and employee lifecycle.
- **Governance** page: approvals inbox, policy simulation, incident controls.
- **Memory** page: scoped explorer with filters and retention controls.
- **Traces** page: timeline and root-cause drilldown.

## 5) Incident and Kill-Switch Operations Need Productization

### Gap

Kill-switch and incident response are in docs, not yet surfaced as platform controls.

### Why It Matters

Enterprises require fast containment for unsafe behavior.

### Add

- Per-org/per-tool kill-switch toggles.
- Approval timeout behavior and escalation.
- Incident timeline view linked to trace/audit records.

## High-Impact Features Borrowed from Reviewed Repos

## From `mem0`

- Memory expiration for short-lived context.
- Metadata-filtered retrieval for deterministic scoping.
- Reranker-enhanced retrieval for quality-critical answers.
- Graph-memory mode for multi-hop entity reasoning.
- Ingestion controls (ignore speculative/low-confidence memories).

## From `Claude_Skills`

- Hook-like event model around pre/post tool usage (adapted to your runtime events).
- Continuous learning pattern with confidence progression (0.3 -> 0.7 -> 0.9).
- Explicit security posture against prompt injection + memory poisoning.
- Progressive disclosure for large context/memory retrieval.

## From `deep-agents-ui` Current Capabilities

- Existing interrupt/approval UX is a strong foundation.
- Existing task/file visualizations can be promoted into richer operator pages.

## SOTA UI Blueprint (Depth Across Pages/Sections)

## Information Architecture

- `/chat` — conversational execution
- `/procedures` — create/edit/version/activate procedures
- `/agents` — employee directory, capabilities, scopes, risk tiers
- `/memory` — browse/filter memory and retention
- `/governance` — approval queue, policy simulation, kill-switches
- `/traces` — run timeline, tool decisions, eval annotations
- `/settings` — org config, model routing, budgets, environments

## Procedure Builder Requirements

- Node types: prompt step, tool step, delegation step, approval step, condition/router step.
- Versioning with draft -> staged -> active lifecycle.
- Side-by-side diff of procedure versions.
- Test-run sandbox with replayed traces.
- One-click promote with gate checks.

## Governance UX Requirements

- Live queue for pending high-risk actions.
- Approve/Edit/Reject with rationale.
- Policy impact simulator before activation.
- Bulk policy migration with dry-run.

## Memory UX Requirements

- Filter by org/user/employee/memory class/time range/confidence.
- Show source trace and memory lineage.
- Run expiration and deletion previews before applying changes.

## 90-Day Sequencing (Pragmatic)

1. Days 1-21: P0/P1 policy runtime + deny-by-default tool registry.
2. Days 22-45: P2 memory governance + mem0 quality options.
3. Days 46-63: P4/P5 audit + eval gates + canary automation.
4. Days 64-90: P6 operator/builder UI pages and workflow depth.

## External Best-Practice Sources

- NIST AI RMF: https://www.nist.gov/itl/ai-risk-management-framework
- NIST AI 600-1: https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-generative-artificial-intelligence
- OWASP Prompt Injection Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/LLM_Prompt_Injection_Prevention_Cheat_Sheet.html
- OWASP AI Agent Security Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/AI_Agent_Security_Cheat_Sheet.html
- OpenAI Production Best Practices: https://platform.openai.com/docs/guides/production-best-practices
- OpenAI Safety in Building Agents: https://platform.openai.com/docs/guides/agent-builder-safety
- OpenAI Agent Evals: https://platform.openai.com/docs/guides/agent-evals
- OpenAI Trace Grading: https://platform.openai.com/docs/guides/trace-grading
- Anthropic agent/sandboxing guidance: https://www.anthropic.com/engineering/claude-code-sandboxing
- Anthropic eval guidance: https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents
- LangChain multi-agent: https://docs.langchain.com/oss/python/langchain/multi-agent/index
- LangGraph interrupts/HITL: https://docs.langchain.com/oss/python/langgraph/human-in-the-loop
