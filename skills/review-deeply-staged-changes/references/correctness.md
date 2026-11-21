# Correctness checklist

- Logic bugs: off-by-one, wrong or inverted conditions, wrong operator precedence, incorrect boundary handling.
- Null/undefined/None/empty handling; unchecked `Optional`/map lookups.
- Async problems: missing `await`, unhandled promise rejections, race conditions, shared mutable state across requests or threads.
- Resource handling: unclosed files, connections, cursors, streams, locks; missing `finally`/`using`/context manager.
- Transactions: wrong boundaries, multi-step writes that are not atomic, side effects (emails, events) fired before commit.
- Time and locale: timezone assumptions, naive datetimes, DST, locale-dependent parsing/formatting.
- Changed behavior or public contracts (API response shape, DB schema, function signatures, event payloads) without matching updates to callers, docs, clients or migrations.
- Error paths: are failures handled, propagated or logged consistently? Is the happy path the only one tested?
- Migrations: reversible? safe on large tables (locks, backfills)? matching model changes? default values for existing rows?
- Config and feature flags: new settings documented, with safe defaults, and present in every environment file the repo keeps.
