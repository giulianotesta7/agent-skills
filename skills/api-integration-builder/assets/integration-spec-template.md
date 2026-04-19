# Integration Spec Template

Use this before implementing any new integration.

## 1. Source System
- Service name:
- Purpose of integration:
- Owner / team:

## 2. Contract Summary
- Base URL:
- Auth model:
- Primary endpoints:
- Pagination model:
- Rate limits:
- Timeout expectations:
- Error schema:

## 3. Data Mapping
- External payload fields:
- Internal DTO / model:
- Transformations required:
- Required validations:

## 4. Reliability Rules
- Retry policy:
- Backoff strategy:
- Idempotency strategy:
- Deduplication key:
- Failure / dead-letter path:

## 5. Observability
- What to log:
- What metrics to capture:
- Correlation / request ID:
- Alert conditions:

## 6. Security
- Secret storage location:
- Signature verification required:
- Sensitive fields to redact:

## 7. Test Plan
- Happy path:
- Auth failure:
- Validation failure:
- Timeout / retry case:
- Duplicate webhook case:
