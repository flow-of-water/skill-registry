---
name: review-deeply-staged-changes
description: In-depth review of only the git staged changes (git diff --cached) before commit/push. Ignores unstaged and untracked files. Checks the new code against the repo's own conventions and architecture (DDD, layered, etc.), clean code, anti-patterns, security and test coverage, using detailed checklists, then ends with a clear verdict - READY or NOT READY to commit and push. Use when the user asks for a deep or thorough review of staged changes, or a full pre-commit/pre-push check. For a fast pass use review-staged-changes instead.
allowed-tools: Bash(git diff --cached:*), Bash(git diff --staged:*), Bash(git show:*), Bash(git status:*), Bash(git log:*), Bash(git branch:*), Bash(git rev-parse:*), Bash(git ls-files:*), Bash(git ls-tree:*), Read, Grep, Glob
---

# Review Deeply Staged Changes

Review ONLY what is in the git index, in depth. Answer one question: **is this staged change ready to commit and push?**

The detailed checklists live in `references/`. Load only the ones relevant to the files in the diff.

## Hard rules

1. **Scope = staged only.** Never run plain `git diff` or `git diff HEAD`; never review unstaged or untracked files.
2. **Read-only.** Never run `git add`, `commit`, `push`, `reset`, `stash`, `checkout`, and never edit files. Suggest fixes in text; the user applies them.
3. **Read the staged version of files, not the working tree.** Use `git show :<path>` for full-file context. Use Read/Grep on the working tree only for files with NO unstaged changes, or for unchanged context (neighbor files, configs).
4. **Nothing staged → stop.** Say "Nothing is staged" and do not fall back to unstaged changes.

## Workflow

### 1. Scope the change
```bash
git status --short
git diff --cached --stat
git diff --cached --name-status
git rev-parse --abbrev-ref HEAD
```
- Note files that are also modified in the working tree (`MM`); the review covers only the staged part.
- For a large diff, go file by file (`git diff --cached -- <path>`). Skim lockfiles, generated code, snapshots and binaries; just verify they are expected.

### 2. Learn the repo's conventions before judging
Derive the rules from the repo, not from personal taste:
- Read `CLAUDE.md`, `AGENTS.md`, `CONTRIBUTING.md`, and the README architecture section if present.
- Read lint/format config (`.eslintrc*`, `.prettierrc*`, `pyproject.toml`, `ruff.toml`, `.editorconfig`, `checkstyle.xml`, `.rubocop.yml`, `phpcs.xml`, ...).
- Look at the directory layout and 2-3 neighbor files of the same kind as each changed file.
- Detect the architecture (DDD / Clean / Hexagonal, layered MVC, plain scripts, ...).
- `git log --oneline -10` for commit message style.

New code must follow what the repo already does. Do not flag new code for matching a pre-existing local pattern, except for security or correctness issues; mention it as a note.

### 3. Review the staged diff against the checklists
Only report real findings tied to staged lines.

| Area | Load |
| --- | --- |
| Correctness (logic, contracts, migrations) | `references/correctness.md` |
| Architecture fit (DDD, layered, placement) | `references/architecture.md` |
| Clean code | `references/clean-code.md` |
| Anti-patterns, stack-specific pitfalls | `references/anti-patterns.md` |
| Security and hygiene | `references/security-hygiene.md` |
| Tests and docs | `references/tests-docs.md` |

Always check consistency with the repo's conventions (naming, placement, formatting, error handling, logging) as part of every pass.

### 4. Optional verification
Do not run tests, linters or builds if the working tree differs from the index (`MM` or other unstaged edits); say "Tests/lint were not run because unstaged changes would distort the result." If the tree matches the index, you may run the repo's fast lint/test commands.

### 5. Push readiness
```bash
git rev-parse --abbrev-ref HEAD
git rev-parse --abbrev-ref @{upstream}    # may fail if no upstream; that is fine
git log --oneline @{upstream}..HEAD       # unpushed commits, if upstream exists
```
Warn if the branch is `main`/`master`/`develop`/`release*`, and mention unpushed commits that would go out together.

## Output and verdict

Follow `references/output-format.md` for the report layout, severity levels (BLOCKER / MAJOR / MINOR / NIT) and the verdict rules. Always end with exactly one verdict line.

## Principles

- Be specific: every finding has `file:line` and a concrete fix.
- Be calibrated: do not inflate severity or invent problems. A clean diff gets a clean verdict.
- Judge against this repo's conventions first, general best practice second.
- If something is ambiguous (e.g. unclear architecture), state the assumption instead of guessing silently.
