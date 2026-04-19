---
name: api-integration-builder
description: >
  Build robust API integrations from docs, curl examples, webhook payloads, or existing endpoints.
  Trigger: When creating API clients, webhook handlers, adapter layers, or integrating third-party/internal services into workflows.
license: Apache-2.0
metadata:
  author: gentleman-programming
  version: "1.0"
---

## When to Use

- Building a new integration against a third-party or internal API
- Turning curl examples or API docs into production-ready client code
- Implementing webhook receivers, signature verification, and idempotent processing
- Mapping external payloads into internal DTOs or domain models
- Connecting APIs into n8n, FastAPI, Flask, or Node.js workflows

## Critical Patterns

### 1. Start from the contract, not the code

Always identify first:

| Item | Questions to answer |
|------|---------------------|
| Auth | API key, bearer token, OAuth, HMAC signature? |
| Rate limits | Is there a `429` policy? Burst vs sustained? |
| Timeouts | What is the max expected response time? |
| Retries | Which errors are transient vs terminal? |
| Idempotency | Can the same request/webhook arrive twice? |
| Pagination | Offset, cursor, page-based? |
| Errors | What is the error schema and status code model? |

Never skip this step. Most integration bugs come from implicit assumptions about the contract.

### 2. Keep transport separate from business logic

Use this layering:

```text
API client / webhook adapter
        ↓
DTO validation + mapping
        ↓
Application service
        ↓
Persistence / side effects
```

Do **not** mix HTTP calls, JSON parsing, business rules, and persistence in one function.

### 3. Validate everything at the boundary

- Validate incoming payloads with schemas/types
- Normalize external field names into internal naming conventions
- Reject malformed input early with explicit errors
- Log context, never secrets

### 4. Retries must be selective

Retry only when it makes sense:

| Status / Failure | Retry? |
|------------------|--------|
| Network timeout | Yes |
| 429 rate limit | Yes, with backoff |
| 5xx server error | Usually yes |
| 400 bad request | No |
| 401/403 auth error | No, fix credentials/config |
| Validation error | No |

Use exponential backoff and a max attempt limit.

### 5. Webhooks require extra discipline

For webhook receivers always check:

- Signature verification when available
- Idempotency key or deduplication strategy
- Safe reprocessing path
- Structured logging of event ID, source, and status
- Clear dead-letter or failure path for unprocessed events

### 6. n8n-specific rule

Use n8n for orchestration, not for dense business logic.

- Good in n8n: routing, branching, triggers, retries, simple transforms
- Move to code: complex mappings, heavy validation, multi-step domain rules, reusable adapters

If a Function node starts looking like an app, pull that logic into code.

## Code Examples

### Python: typed integration client

```python
from dataclasses import dataclass
import httpx


@dataclass
class CustomerDTO:
    id: str
    email: str


class BillingClient:
    def __init__(self, base_url: str, api_key: str):
        self._client = httpx.Client(
            base_url=base_url,
            timeout=10.0,
            headers={"Authorization": f"Bearer {api_key}"},
        )

    def get_customer(self, customer_id: str) -> CustomerDTO:
        response = self._client.get(f"/customers/{customer_id}")
        response.raise_for_status()
        data = response.json()
        return CustomerDTO(id=data["id"], email=data["email"])
```

### FastAPI: webhook receiver with idempotency stub

```python
from fastapi import FastAPI, Header, HTTPException, Request

app = FastAPI()


@app.post("/webhooks/provider")
async def provider_webhook(
    request: Request,
    x_signature: str | None = Header(default=None),
):
    payload = await request.json()

    if not x_signature:
        raise HTTPException(status_code=401, detail="Missing signature")

    event_id = payload.get("id")
    if not event_id:
        raise HTTPException(status_code=400, detail="Missing event id")

    # verify signature here
    # if already_processed(event_id): return {"status": "duplicate"}

    # map payload -> internal DTO -> service call
    return {"status": "accepted", "event_id": event_id}
```

### TypeScript: adapter layer before business logic

```ts
type ExternalOrder = { order_id: string; customer_email: string }
type InternalOrder = { id: string; customerEmail: string }

export function mapOrder(input: ExternalOrder): InternalOrder {
  return {
    id: input.order_id,
    customerEmail: input.customer_email,
  }
}
```

## Commands

```bash
# Inspect a JSON payload quickly
python -m json.tool response.json

# Test an API manually
curl -i "https://api.example.com/resource" -H "Authorization: Bearer $API_KEY"

# Expose a local webhook receiver
ngrok http 8000

# Run focused integration tests
pytest tests/integrations -q
```

## Resources

- **Templates**: See [assets/](assets/) for a reusable integration specification template
