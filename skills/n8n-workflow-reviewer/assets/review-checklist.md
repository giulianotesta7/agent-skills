# n8n Workflow Review Checklist

Use this checklist when reviewing a workflow through the n8n MCP or from an exported JSON.

## 0. Source of truth
- Is the review using the n8n MCP or an exported JSON?
- If MCP is available, was the live workflow definition inspected?
- If MCP exposes executions, were recent runs reviewed before giving recommendations?

## 1. Purpose and Trigger
- What business problem does this workflow solve?
- What starts it? (Webhook, Cron, Manual, Sub-workflow)
- Is the trigger idempotent or could it duplicate side effects?

## 2. Data Flow
- What payload enters the workflow?
- Where is data transformed?
- Are field mappings explicit or brittle?
- Are there client-specific branches that should move to code?

## 3. Reliability
- Are retries configured where transient failures can happen?
- Is there an error workflow or explicit failure path?
- Are downstream side effects safe if the workflow reruns?
- Are timeouts or throttling relevant?

## 4. Security
- Are credentials referenced properly instead of hardcoded?
- Is PII being logged or forwarded unnecessarily?
- Are webhook signatures verified if the source supports it?

## 5. Maintainability
- Are node names descriptive?
- Are there too many branches for one workflow?
- Are Code nodes small and focused?
- Should any logic move to an API/service layer?

## 6. AI-Specific Checks
- Is the prompt specific enough?
- Is output validated before use?
- Is a schema enforced?
- Is human review needed before side effects?
- Is memory/tool use justified?

## 7. Review Verdict
- Strengths:
- Critical issues:
- Warnings:
- Suggested refactor path:
