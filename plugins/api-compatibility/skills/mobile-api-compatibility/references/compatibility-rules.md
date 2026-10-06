# API compatibility rules

| Change | Breaking? | What to do |
|---|---|---|
| Add an optional response field | No | Ship in the current version with a default |
| Add an optional query parameter | No | Ship in the current version; keep the old behaviour when it's absent |
| Add an enum value | Risky | OK only if clients map unknown values to `unknown` |
| Remove a response field | **Yes** | Deprecate first; remove only after 2 release cycles |
| Rename a field (e.g. `handle` → `username`) | **Yes** | Add the new field next to the old one, then deprecate the old one |
| Change a field's type (e.g. string ID → int) | **Yes** | New version (`/v2`) |
| Make an optional field required, or a non-null field nullable | **Yes** | New version |
| Change pagination defaults, limits, or cursor semantics | **Yes** | New version |
| Change the error response shape | **Yes** | New version |
| Change which results are returned or their order | No (contract) | Behaviour change: note it in the PR, no version bump |
