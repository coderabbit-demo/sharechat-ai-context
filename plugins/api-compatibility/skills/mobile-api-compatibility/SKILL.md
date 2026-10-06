---
name: mobile-api-compatibility
description: Change or consume an HTTP API that mobile apps depend on without breaking app versions already installed on users' phones. Use when editing API handlers, response models, serializers, or OpenAPI contracts in a backend service, or when parsing API responses in an Android or iOS app. Triggers on "breaking change", "API contract", "openapi", "response field", "backward compatible", "deprecate field", or any change to a request/response shape.
---

# Mobile API compatibility

Old app versions stay installed for months. An API change ships to every user at once, while a
client change reaches users slowly, one app update at a time. So **every API change must keep working for
clients built before it.**

## Find the contract

Each service owns its contract and keeps it next to its code, usually at `api/openapi.yaml`.
The repo's `.coderabbit.yaml` or README names the contract path and the client repos that consume it.
Never copy a contract into another repo. Read it from the service that owns it.

## When changing an API (backend service)

1. Read the contract before touching any handler, model, or serializer it covers.
2. Classify the change with `references/compatibility-rules.md`:
   - **Additive** (new optional field, new optional query param): allowed in the current version.
   - **Breaking** (remove, rename, or retype a field; make a field required or nullable; change enum
     meaning or pagination): not allowed in the current version. Add a new version (e.g. `/v2`) and keep the old one serving.
3. Update the contract in the same PR as the code.
4. Give new fields a default value.
5. Deprecate before removing: mark the field `deprecated: true`, keep serving it for at least 2 app release
   cycles, and remove it only when the minimum supported app versions no longer read it.

## When consuming an API (Android / iOS app)

1. Parse leniently: ignore unknown fields, and map unknown enum values to an `unknown` case
   instead of crashing.
2. Never assume an optional field is present. Fall back to a safe default.
3. Treat pagination cursors and IDs as opaque strings.
4. Before reading a new field, check that it is in the service's contract and already served in production.

## Review checklist

- [ ] Contract updated in the same PR as the code
- [ ] No field removed, renamed, or retyped in an existing version
- [ ] No optional field made required or nullable
- [ ] New enum values are safe for old clients (they map to `unknown`)
- [ ] Pagination and error response shapes are unchanged
- [ ] Every consuming client repo checked if the response shape changed
