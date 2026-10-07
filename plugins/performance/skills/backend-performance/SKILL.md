---
name: backend-performance
description: Keep high-traffic backend endpoints fast and stable under load. Use when writing or reviewing request handlers, service code that calls other services, caches, or anything on the path of a user-facing request (for example the home feed). Triggers on "N+1", "latency", "p99", "cache", "HTTP client", "timeout", "hot path", or code that loops over items and calls a remote service.
---

# Backend performance

ShareChat's user-facing endpoints serve millions of requests a minute. Code on the request path must
do a small, predictable amount of work per request, whatever the page size or traffic.

## 1. No remote calls inside loops (N+1)
- Never call another service, database or cache once per item in a loop. A 20-item page must not
  become 20 network round trips.
- Collect the IDs first and use the batch endpoint (for example `POST /v1/profiles:batchGet`) or a
  single query with `IN (...)`.

## 2. Every outbound call has a timeout
- Set both a connect timeout and a request timeout on every HTTP client and database call.
- The whole request path has a 200 ms budget. A dependency without a timeout can hang the request
  thread and take the endpoint down with it.

## 3. Create expensive objects once
- HTTP clients, JSON mappers (Gson, ObjectMapper), compiled regexes and date formatters are created
  once (as Spring beans or constants) and reused.
- Never create them per request or per item; each one allocates pools, buffers or caches.

## 4. Caches are bounded and thread-safe
- In-process caches must have a maximum size and an expiry (use Caffeine).
- Never use a plain `HashMap`/`mutableMapOf` as a cache in a singleton: it grows without limit and is
  not safe for concurrent requests.

## 5. Work in proportion to the page
- Do work for the items you return, not for the whole dataset: page first, then enrich.
- Avoid nested loops over the same collection (`list.contains` inside a loop); use a `Set` or `Map`.

## 6. Keep logging out of the hot path
- At most one INFO log line per request. Per-item details go to DEBUG.
- Use SLF4J placeholders so disabled log levels cost nothing.

## Review checklist
- [ ] No remote call per item; batch endpoint used
- [ ] Connect and request timeouts on every outbound call
- [ ] No HTTP client, mapper, regex or formatter created per request or per item
- [ ] Caches bounded (size + expiry) and thread-safe
- [ ] Work scales with page size, not dataset size
- [ ] No per-item INFO logging on the request path
