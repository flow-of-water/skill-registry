# Tests and docs checklist

## Tests

- New or changed behavior has tests, in the repo's test style and location.
- Bug fixes include a test that fails without the fix.
- Tests assert behavior, not implementation details; names describe the scenario and expectation.
- Edge cases covered: empty/null input, boundaries, error paths, permissions.
- No flaky patterns: `sleep`, real network or clock, shared state between tests, order dependence.
- No `.only` / `.skip` / commented-out tests left behind.
- Test data and fixtures are minimal and do not contain real personal or production data.
- Mocks are used at boundaries only; over-mocking that makes the test meaningless is a finding.

## Docs

- README, docs, changelog, OpenAPI/Swagger specs or migration notes are updated if the repo maintains them.
- Public APIs, config options and environment variables introduced by the change are documented.
- Code comments and docstrings touched by the change are still accurate.
