# Clean code checklist

- **Functions** do one thing, have a reasonable size and nesting depth, and few parameters. Prefer early returns over deep nesting.
- **Names** are intention-revealing, consistent with the repo's vocabulary, and not misleading (no `data`, `tmp`, `handle` where a real name exists).
- **Duplication** that should be shared is extracted; duplication that is coincidental is left alone.
- **Dead code**: no commented-out code, unused imports, variables, parameters or unreachable branches.
- **Magic values**: numbers and strings with meaning become named constants or config.
- **Comments** go stale easily, so they are written only when actually needed (a non-obvious "why", a hidden constraint, a workaround). Intention should be exposed by the code itself: clear names, short functions, single-purpose classes. Flag a comment that explains *what* the code does as a sign the code should be refactored instead; flag comments that restate the code or have gone stale.
- **Error handling** is consistent with the repo, with useful messages that include context (what failed, with which input).
- **Boolean flags** that switch behavior inside a function usually mean two functions.
- **Abstractions** are earned: no speculative generality, no wrapper that only forwards calls, no premature configurability.
- **Side effects** are visible from the name and signature; query functions do not mutate.
- **Readability**: can a teammate understand the change without the author? If not, say what is unclear.
- **Size of change**: a diff that does several unrelated things should be split into separate commits.
