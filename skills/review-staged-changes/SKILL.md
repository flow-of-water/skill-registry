---
name: review-staged-changes
description: Review only the git staged changes (git diff --cached) before commit/push, sized for small and medium changes. Ignores unstaged and untracked files. Covers correctness, repo conventions, architecture (e.g. DDD), clean code, anti-patterns, security and tests in one compact pass, then ends with a clear verdict - READY or NOT READY to commit and push. Use when the user asks to review staged changes, check before commit, or "ready to commit/push?". For large or high-risk changes that need detailed checklists, use review-deeply-staged-changes instead.
allowed-tools: Bash(git diff --cached:*), Bash(git diff --staged:*), Bash(git show:*), Bash(git status:*), Bash(git log:*), Bash(git branch:*), Bash(git rev-parse:*), Bash(git ls-files:*), Bash(git ls-tree:*), Read, Grep, Glob
---

# Review Staged Changes

Review ONLY what is in the git index. Answer one question: **is this staged change ready to commit and push?**

This is the compact version: it covers the same aspects as `review-deeply-staged-changes`, but in one pass without loading detailed checklists. Keep effort proportional to the size of the diff.

## Hard rules

1. **Scope = staged only.** Never run plain `git diff` or `git diff HEAD`; never review unstaged or untracked files.
2. **Read-only.** Never run `git add`, `commit`, `push`, `reset`, `stash`, `checkout`, and never edit files. Suggest fixes in text.
3. **Read the index version.** For full-file context use `git show :<path>`, not the working tree (it may contain unstaged edits). Use Read/Grep on the working tree only for unchanged context (neighbor files, configs) or files with no unstaged changes.
4. **Nothing staged → stop.** Say "Nothing is staged" and do not fall back to unstaged changes.

## Workflow

### 1. Scope
```bash
git status --short
git diff --cached --stat
git rev-parse --abbrev-ref HEAD
```
Note files that are also modified in the working tree (`MM`); only the staged part is reviewed. Skim lockfiles, generated code and snapshots; just verify they are expected.

### 2. Learn the repo's conventions first
Derive the rules from the repo, not from personal taste:
- `CLAUDE.md` / `AGENTS.md` / `CONTRIBUTING.md` / README architecture section, and lint/format config if present.
- Directory layout plus 1-2 neighbor files of the same kind as each changed file (naming, structure, error handling, logging, test style).
- Detect the architecture (DDD / Clean / Hexagonal, layered MVC, plain scripts).

New code should follow what the repo already does. Do not flag matching a pre-existing local pattern, except for security or correctness issues; mention it as a note.

### 3. Review the staged diff (`git diff --cached`)
Only report real findings tied to staged lines.

- **Correctness:** logic bugs, null handling, off-by-one, missing `await`, unclosed resources, wrong transaction boundaries, changed contracts (API shape, schema, signatures) without updated callers/docs/migrations.
- **Conventions:** naming, file placement, formatting, error handling, logging versus neighbors and lint config.
- **Architecture (only the style the repo uses):**
  - DDD/Clean/Hexagonal: domain must not import infrastructure/framework/ORM/HTTP types; business rules in entities/aggregates/domain services, not controllers or repositories; value objects immutable and validated; repository interfaces inward, implementations in infrastructure; aggregates referenced by ID; application services orchestrate and own transactions.
  - Layered MVC: thin controllers, logic in services, no data access in controllers, no cross-layer leaks.
  - New files placed where the architecture says.
- **Clean code:** one job per function, sensible size and nesting, intention-revealing names, no duplication worth sharing, no dead or commented-out code, unused imports/variables, or magic values; comments only where truly needed (they go stale), with intention exposed by names, short functions and single-purpose classes instead.
- **Anti-patterns:** god class/function, long parameter lists, swallowed or over-broad exceptions, N+1 and unbounded queries, queries in loops, business logic in controllers/views, global mutable state, hardcoded environment values, missing timeouts. Also check stack pitfalls that apply to the changed files (e.g. blocking I/O in async routes, state derived in effects, fat controllers, entities exposed in API responses, unsafe string-built queries, VBA without `Option Explicit`).
- **Security & hygiene:** secrets/keys/`.env` (blocker), injection, XSS, missing authN/authZ on new endpoints, sensitive data in logs; leftovers such as `console.log`/`print`/`debugger`, `.only`/`.skip`, conflict markers, unrelated or generated files, TODOs without a ticket.
- **Tests & docs:** new or changed behavior has tests in the repo's style (asserting behavior, not implementation, no flaky patterns); docs/changelog/OpenAPI/migration notes updated if the repo keeps them.

If a category does not apply to this diff, skip it silently. If a finding needs deeper analysis than this pass allows, say so and suggest `review-deeply-staged-changes`.

### 4. Verification
Do not run tests, linters or builds if the working tree differs from the index (`MM`); say "Tests/lint were not run because unstaged changes would distort the result." If the tree matches the index, you may run the repo's fast lint/test commands.

### 5. Push readiness
```bash
git rev-parse --abbrev-ref @{upstream}    # may fail if no upstream; that is fine
git log --oneline @{upstream}..HEAD
```
Warn if the branch is `main`/`master`/`develop`/`release*`, and mention unpushed commits that would go out together.

## Severity

- **BLOCKER:** bug, security issue, secret leak, broken build, data-loss risk, hard-to-undo architecture violation, conflict markers.
- **MAJOR:** significant convention/architecture violation, anti-pattern, missing tests for important logic, performance problem (N+1, unbounded query).
- **MINOR:** clean-code issues, small deviations, naming, duplication.
- **NIT:** stylistic; never affects the verdict.

## Output

```
## Staged review: <branch>  (<N files, +A / -D>)

**Scope note:** <unstaged edits ignored, or "none">
**Conventions detected:** <architecture, lint, test style in 1 line>

### Findings
**BLOCKER** / **MAJOR** / **MINOR** / **NIT**   (omit empty levels; say so if nothing found)
- `path/file.ext:LINE` - <problem>. Fix: <concrete suggestion>.

### Verification
<what was run / not run and why>

### Verdict
<exactly one line below>
```

- `❌ NOT READY to commit/push` - at least one BLOCKER or MAJOR. List what to fix first.
- `⚠️ READY to commit, with follow-ups` - no BLOCKER/MAJOR, but MINOR items worth fixing.
- `✅ READY to commit and push` - no BLOCKER/MAJOR/MINOR (NITs allowed).

Add one line on push: branch, whether it is protected/unusual, extra unpushed commits.

## Principles

- Be specific: every finding has `file:line` and a concrete fix.
- Be calibrated: do not inflate severity or invent problems; a clean diff gets a clean verdict.
- Judge against this repo's conventions first, general best practice second.
- State assumptions when something is ambiguous instead of guessing silently.
