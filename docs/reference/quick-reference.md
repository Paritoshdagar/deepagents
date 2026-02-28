# AI Organization Platform - Production Quick Reference

## North Star

Build a reliable "AI organization" where specialized AI employees can act with bounded autonomy, measurable quality, and auditable governance.

## Non-Negotiables

- Every high-impact action is either policy-allowed or human-approved.
- Every tool call is attributable to `org_id`, `employee_id`, `user_id`, and `trace_id`.
- No production model or prompt change ships without passing eval gates.
- No tenant data crosses tenant boundaries.
- Every incident has: detection, containment, owner, timeline, and postmortem.

## Control Layers (Defense in Depth)

1. Identity: authenticated user + scoped employee identity.
2. Authorization: policy engine checks capability and risk tier.
3. Input controls: prompt-injection and policy guardrails.
4. Tool controls: parameter validation + allowlisted operations.
5. Output controls: schema validation + sensitive-data redaction.
6. Human controls: approval required for high-risk operations.
7. Observability: traces, decision logs, and immutable audit entries.

## Tool Risk Tiers

| Tier | Example Action | Default Policy |
|------|----------------|----------------|
| T0 | Read docs / summarize | Auto-allow |
| T1 | Create draft artifact | Auto-allow with logging |
| T2 | Write to internal systems | Policy allow + optional approval |
| T3 | Irreversible external action (payments, deletes, production deploy) | Human approval required |

## Approval Matrix

| Action Type | Auto | Human Reviewer |
|-------------|------|----------------|
| Read-only tools | Yes | No |
| Internal write (reversible) | Conditional | Optional |
| External write (customer impact) | No | Required |
| Financial/irreversible | No | Required + dual-control optional |

## Release Gates

- `Gate 0`: Unit/integration tests pass.
- `Gate 1`: Safety and policy eval suite passes.
- `Gate 2`: Trace grading meets threshold for critical workflows.
- `Gate 3`: Canary in staging + rollback verified.
- `Gate 4`: Production rollout with live monitors and alerting.

## SLO Targets (Initial)

| Metric | Target |
|--------|--------|
| P95 end-to-end latency | <= 8s |
| Policy decision latency | <= 75ms |
| Tool execution success rate | >= 99.0% |
| Critical workflow success (eval) | >= 95% |
| High-risk action false-allow rate | 0 |

## Incident Severity

| Severity | Definition | Response |
|----------|------------|----------|
| SEV-1 | Data leak, unauthorized action, tenant boundary breach | Immediate kill-switch + incident commander |
| SEV-2 | High-impact degradation or repeated policy bypass attempts | Contain within 1 hour |
| SEV-3 | Non-critical degradation or localized failures | Fix in normal sprint / hotfix if needed |

## Memory Governance Rules

- Scope all memory entries with `org_id` + `subject_type` + `subject_id`.
- Separate durable memory from session working memory.
- Apply retention and deletion policy by memory class.
- Never allow user prompts to overwrite immutable policy memory.
- Record memory writes with actor, reason, and source trace.

## Minimum Telemetry per Request

- `trace_id`, `org_id`, `employee_id`, `user_id`
- selected model + prompt/version ids
- tools considered / tools executed / arguments hash
- policy decisions + allow/deny rationale
- guardrail triggers + mitigation actions
- outcome label (`success`, `blocked`, `degraded`, `failed`)

## Weekly Operating Cadence

- Reliability review: SLOs, error budget, incident trends.
- Safety review: injection attempts, policy violations, false positives.
- Quality review: eval regressions by workflow and employee.
- Cost review: token/tool spend, outliers, optimization actions.

## Source Anchors

- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)
- [NIST AI 600-1 Generative AI Profile](https://airc.nist.gov/docs/AI_RMF-600-1.pdf)
- [OWASP Top 10 for LLM Applications 2025](https://genai.owasp.org/llm-top-10/)
- [OWASP Prompt Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/LLM_Prompt_Injection_Prevention_Cheat_Sheet.html)
- [OpenAI Production Best Practices](https://platform.openai.com/docs/guides/production-best-practices)
- [OpenAI Agent Safety: Building Safeguards for Agents](https://openai.com/index/building-agents/)
- [Anthropic: Building Effective Agents](https://www.anthropic.com/engineering/building-effective-agents)
- [LangChain Multi-Agent Guidance](https://docs.langchain.com/oss/python/langchain/multi-agent)
- [Google Secure AI Framework (SAIF)](https://cloud.google.com/security/ai-security-framework)
