# Production Blueprint: AI Organization

This directory contains implementation-grade artifacts to build a production AI employee/AI organization platform on top of `deepagents`, plus a SOTA roadmap for `deep-agents-ui`.

## Contents

- `epics-and-tasks.md` — prioritized epics and task breakdown mapped to concrete code paths.
- `policy.yaml` — production governance policy contract (deny-by-default, risk tiers, HITL, release gates).
- `tool-registry.yaml` — canonical tool metadata schema used for authorization and approvals.
- `sota-gap-analysis.md` — feature gap analysis and market-leading roadmap derived from deepagents, mem0, Claude_Skills, deep-agents-ui, and external best practices.
- `ui-sota-roadmap.md` — dedicated UI architecture and delivery plan for procedure building and operator pages.

## Implementation Order

1. Build policy and tool registry enforcement in runtime.
2. Add governed memory boundaries and audit trail.
3. Add eval/release gates.
4. Ship operator + builder UI pages (procedures, memory, governance, traces).

## Source Anchors

- NIST AI RMF and NIST AI 600-1
- OWASP GenAI/Agent security guidance
- OpenAI production + agent safety + eval/trace guidance
- Anthropic agent/sandboxing and eval guidance
- LangChain/LangGraph multi-agent, context engineering, HITL/interrupt patterns
