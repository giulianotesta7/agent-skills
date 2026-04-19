# n8n MCP Review Sequence

Use this when the n8n MCP is connected and you can inspect live workflows.

## 1. Resolve the target workflow
- Find the workflow by name or ID
- Confirm you are reviewing the correct environment (dev / staging / prod)

## 2. Pull the live definition
- Inspect triggers, branches, Code nodes, HTTP nodes, AI nodes, and error paths
- Treat the live MCP definition as the source of truth over stale exports

## 3. Review recent executions
- Look for repeated failures
- Identify where retries happen most often
- Check if the same webhook/event appears multiple times
- Look for nodes with long durations or unstable outputs

## 4. Inspect reliability risks
- Missing error branches
- No deduplication / idempotency strategy
- Hidden side effects after partial failure
- Timeouts, throttling, and provider instability

## 5. Inspect AI-specific risks
- Free-form output used without validation
- Missing schema enforcement
- No human review on risky actions
- Memory/tool use that adds complexity without benefit

## 6. Make the architecture call
- Keep orchestration, routing, triggers, and simple transforms in n8n
- Move dense logic, parsing, validation, and reusable adapters into code

## 7. Write the review result
- Summary
- What is working well
- Critical issues
- Warnings
- Suggested refactor path
