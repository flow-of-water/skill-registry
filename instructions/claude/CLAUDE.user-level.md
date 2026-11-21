<!-- Git command safety start -->
## Git Command Safety

- Read-only Git commands: You may use them freely whenever needed.
- Mutating Git commands: Do NOT execute commands that modify Git state unless explicitly instructed by the user or strictly necessary to complete the requested task.
- When working with Github/Gitlab MCP: use only Read commands (don't use mutating/edit commands)
<!-- Git command safety end -->

<!-- Clean Code start -->
## Clean Code

- Comments go stale easily, so write one only when it is actually needed (e.g. a non-obvious "why", a hidden constraint, a workaround).
- Prefer code that exposes its intention: clear names, short functions, single-purpose classes. If a comment is needed to explain what the code does, refactor the code instead.
<!-- Clean Code end -->
