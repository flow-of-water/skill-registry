# Output format and verdict

## Severity levels

- **BLOCKER:** bug, security issue, secret leak, broken build, data-loss risk, hard-to-undo architecture violation, leftover conflict markers.
- **MAJOR:** significant convention or architecture violation, anti-pattern, missing tests for important logic, performance problem (N+1, unbounded query).
- **MINOR:** clean-code issues, small convention deviations, naming, duplication.
- **NIT:** purely stylistic; never affects the verdict.

## Report layout

```
## Staged review: <branch>  (<N files, +A / -D>)

**Scope note:** <e.g. "2 files also have unstaged changes (ignored)", or "none">
**Conventions detected:** <architecture, lint config, test style, in 1-2 lines>

### Findings
**BLOCKER**
- `path/file.ext:LINE` - <problem>. Fix: <concrete suggestion>.
**MAJOR**
- ...
**MINOR**
- ...
**NIT**
- ...
(Omit empty levels. If nothing was found, say so.)

### What is good
<1-3 short bullets, only if genuine>

### Verification
<what was run / not run and why>

### Verdict
<one of the three lines below>
```

## Verdict rules (always end with exactly one)

- `❌ NOT READY to commit/push` - at least one BLOCKER or MAJOR. List the items to fix first.
- `⚠️ READY to commit, with follow-ups` - no BLOCKER/MAJOR, but MINOR items worth fixing; safe to commit and push. List them.
- `✅ READY to commit and push` - no BLOCKER/MAJOR/MINOR (NITs allowed).

Add one line on push: branch name, whether it is protected or unusual, and any extra unpushed commits.
