---
name: n8n-workflow-reviewer
description: >
  Review n8n workflows through the n8n MCP when available, or from exported JSON as a fallback, for reliability, maintainability, security, and AI-specific risks.
  Trigger: When examining n8n workflows, webhook flows, Function/Code nodes, retries, error handling, agentic workflows, or deciding whether logic should stay in n8n or move to code.
license: Apache-2.0
metadata:
  author: gentleman-programming
  version: "1.0"
---

## When to Use

- Reviewing a live n8n workflow through the n8n MCP before shipping it to production
- Reviewing an exported n8n workflow when MCP access is unavailable
- Auditing an existing workflow that became fragile, slow, or hard to maintain
- Evaluating whether a Function/Code node should remain in n8n or move into code
- Checking webhook pipelines, AI agent flows, and external API automations for risk
- Preparing feedback for a teammate on naming, structure, retries, idempotency, and observability

## Critical Patterns

### 0. Use the n8n MCP first, JSON second

Preferred order:

| Source | When to use |
|--------|-------------|
| n8n MCP | Default when connected to a live n8n instance |
| Exported JSON | Fallback when MCP is unavailable |

If the n8n MCP is available, review the workflow from the live source of truth. That gives you:

- the current published workflow definition
- node configuration as it actually exists in the instance
- execution history and failure patterns when exposed by the MCP
- environment context that a stale export may hide

Only fall back to raw exported JSON when MCP access is not available.

### 0.1 MCP review sequence

When the MCP is available, try to review in this order:

1. Locate the workflow by name or ID
2. Fetch the workflow definition from MCP
3. Inspect recent executions if MCP exposes them
4. Identify failing nodes, retry loops, long-running branches, and brittle expressions
5. Then apply the five review passes below

If execution history is available, trust production evidence over static guesses.

### 1. Review in this order

Always review workflows in five passes:

| Pass | What to inspect |
|------|------------------|
| Data flow | Trigger, payload shape, field transformations, assumptions |
| Reliability | Retries, Continue On Fail, timeouts, fallback paths |
| Security | Credentials, secrets, signature verification, PII exposure |
| Maintainability | Node naming, branching complexity, Code node size, reuse |
| AI-specific | Prompt quality, schema validation, guardrails, HITL, cost/risk |

Do **not** start by judging style alone. First understand what the workflow is trying to do.

### 2. n8n is an orchestrator, not your entire application

Use this decision rule:

| Keep in n8n | Move to code |
|-------------|--------------|
| Routing and branching | Complex domain rules |
| Simple transforms | Heavy validation |
| Triggers and scheduling | Reusable adapters |
| Notifications | Multi-step business logic |
| Straightforward API calls | Large Code nodes / custom parsing |

If a workflow depends on multiple long Code nodes or custom logic repeated across workflows, move that part to FastAPI/Flask/Node and let n8n call it.

### 3. Reliability checks are mandatory

Every review should explicitly answer:

- What happens if the provider returns `429`?
- What happens if a webhook arrives twice?
- What happens if one downstream step fails after side effects already happened?
- What happens if the input payload changes shape?
- What happens if the AI output is malformed?
- What do recent executions show about retries, noisy nodes, and recurring failures?

If the workflow cannot answer those, it is not production-ready.

### 4. Red flags to call out immediately

| Red flag | Why it matters |
|----------|----------------|
| Node names like `HTTP Request1` / `Set2` | Makes debugging and ownership painful |
| Hardcoded IDs, tokens, emails, URLs | Breaks portability and security |
| More than one dense Code node | Usually means business logic escaped orchestration |
| No error branch or error workflow | Failures disappear silently |
| No idempotency strategy on webhook-triggered writes | Duplicate events create duplicate side effects |
| Deep brittle expressions against `$json` | Small upstream changes break the workflow |
| AI output used directly without validation | High risk of malformed or unsafe execution |

### 5. AI nodes require stricter review than regular nodes

For AI Agent / LLM / prompt-based flows, always check:

- Is the model output constrained to a schema or expected structure?
- Is there validation before downstream writes or side effects?
- Are prompts deterministic enough for the task?
- Is there a fallback or human review step for risky actions?
- Is memory actually needed, or is it just increasing complexity?

Never approve an AI workflow that can trigger external side effects from free-form output without validation.

### 6. Review output format

When using this skill, return findings in this order:

1. **Summary** — what the workflow does in one sentence
2. **What is working well** — 2-4 strengths
3. **Critical issues** — must-fix before production
4. **Warnings** — not blockers, but risky
5. **Suggested refactor path** — what stays in n8n vs what moves to code

This keeps feedback actionable instead of just nitpicky.

## Code Examples

### Bad node naming

```text
Webhook -> HTTP Request1 -> Set2 -> Code3 -> HTTP Request4
```

### Better node naming

```text
Incoming Candidate Webhook -> Fetch Candidate Record -> Normalize Payload -> Score Candidate -> Notify Recruiter
```

### Bad AI pattern

```text
LLM output -> directly create/update records in external system
```

### Better AI pattern

```text
LLM output -> validate schema -> confidence/risk gate -> side effect
```

### Refactor threshold example

```text
If a Code node parses PDFs, maps 12 fields, branches on client-specific rules,
and builds a payload for three systems, that logic belongs in code, not in n8n.
```

## Commands

```bash
# Pretty-print workflow JSON
python -m json.tool workflow.json

# Search for Code nodes quickly
rg '"type":\s*"n8n-nodes-base.code"|"type":\s*"n8n-nodes-base.function"' workflow.json

# Search for hardcoded webhook URLs or tokens
rg 'http|token|secret|bearer|api[_-]?key' workflow.json
```

## Resources

- **Templates**: See [assets/](assets/) for a reusable workflow review checklist
- **Templates**: See [assets/mcp-review-sequence.md](assets/mcp-review-sequence.md) for a live MCP-first review flow
