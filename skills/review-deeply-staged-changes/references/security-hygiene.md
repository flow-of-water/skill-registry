# Security and hygiene checklist

## Security (secrets are always a BLOCKER)

- Secrets, tokens, passwords, API keys, private keys, `.env` files or credentials in the diff.
- SQL/NoSQL/command injection, XSS, path traversal, SSRF, unsafe deserialization.
- Missing authentication or authorization checks on new endpoints, handlers or jobs; missing ownership checks (IDOR).
- Overly permissive CORS, disabled TLS verification, weak crypto or hand-rolled crypto.
- Sensitive data in logs, error messages or API responses.
- New dependencies: known-vulnerable versions, unmaintained or typosquat-looking packages.
- User input trusted without validation, size limits or rate limits.

## Hygiene and leftovers

- Debug leftovers: `console.log`, `print`, `debugger`, `binding.pry`, `dd()`, `var_dump`.
- `TODO` / `FIXME` without a ticket reference.
- `.only` / `.skip` in tests.
- Merge-conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`) (BLOCKER).
- Large, unrelated, generated or binary files; build artifacts; IDE or OS files (`.idea/`, `.DS_Store`).
- Unrelated changes mixed into one commit (suggest splitting).
- Accidental whitespace-only or formatting-only churn across untouched lines.
