# Anti-patterns checklist

Flag these when they appear in the new code.

## General

- God class / god function, long parameter lists, feature envy, primitive obsession, shotgun surgery.
- Swallowed exceptions (`catch {}`, bare `except:`), catching too broadly, exceptions used for control flow.
- N+1 queries, unbounded queries without pagination, missing indexes for new query patterns, queries inside loops.
- Business logic in controllers, views or templates; direct DB access from the UI or route layer.
- Global mutable state, hidden side effects, hardcoded environment values (URLs, hosts, paths).
- Retries without backoff or limits, missing timeouts on network calls.

## Stack-specific

- **JS / Express:** missing async error handling, callback hell, unvalidated `req.body`.
- **Python FastAPI / Flask:** blocking I/O in async routes, mutable default arguments, missing Pydantic validation, session leaks.
- **React / MUI:** state derived in effects, missing hook deps or list keys, inline heavy objects causing re-renders, prop drilling where the repo uses context/store.
- **Rails:** fat controllers/models, callbacks with side effects, N+1 without `includes`, `update_all` skipping validations.
- **Laravel:** logic in controllers, missing form requests, mass-assignment holes, N+1 without `with()`.
- **Spring Boot / Java:** field injection when the repo uses constructor injection, entities exposed directly in API responses, `@Transactional` misuse, `Optional.get()` without a check.
- **MongoDB / Postgres / MySQL:** missing indexes, string-built queries, non-atomic multi-step writes without a transaction.
- **VBA:** missing `Option Explicit`, unqualified `Range`/`Cells`, `Select`/`Activate`, no error handler, magic cell addresses.
