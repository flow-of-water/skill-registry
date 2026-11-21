# skill-registry

A personal, public registry of reusable **agent skills** and **prompts** that I carry from project to project.

## Repository layout

```
skill-registry/
├── README.md
├── skills/
│   └── <skill-name>/
│       ├── SKILL.md          # frontmatter (name, description) + instructions
│       ├── scripts/          # optional helper scripts
│       └── references/       # optional long-form docs loaded on demand
├── instructions/
│   └── claude/CLAUDE.user-level.md  # instructions for Claude Code
├── prompts/
│   └── <topic>/<prompt-name>.md
├── templates/                # starting points for new skills and prompts
└── scripts/                  # helper scripts (install, validate)
```

| Directory       | Purpose                                                                  |
| --------------- | ------------------------------------------------------------------------ |
| `skills/`       | Agent skills. Each is a folder containing a `SKILL.md` and optional resources. |
| `instructions/` | User-level instruction files (e.g. `CLAUDE.md`), grouped by agent.        |
| `prompts/`      | Plain prompts you can copy and paste into any AI tool, grouped by topic.  |
| `templates/`    | Starting points for authoring a new skill or prompt.                     |
| `scripts/`      | Helper scripts to install and validate skills.                           |

## Using a skill

Skills follow the open `SKILL.md` format (a folder with a `SKILL.md` and optional resources), so they work with any agent that supports it. The examples below use Claude Code's directories; adjust the paths for your agent of choice.

**Available in every project (user level, e.g. Claude Code):**

```bash
ln -s "$(pwd)/skills/<skill-name>" ~/.claude/skills/<skill-name>
```

**Only in one project (e.g. Claude Code):**

```bash
mkdir -p <project>/.claude/skills
cp -r skills/<skill-name> <project>/.claude/skills/
```

Symlinking is recommended for personal use: edit a skill here and every project picks up the change immediately.

## Using user-level instructions

Files under `instructions/<agent>/` are the source of truth for global, per-user agent instructions. Symlink them to where the agent reads them, e.g. for Claude Code:

```bash
ln -s "$(pwd)/instructions/claude/CLAUDE.user-level.md" ~/.claude/CLAUDE.md
```

Back up any existing `~/.claude/CLAUDE.md` first. Keep these files free of personal or private details, since the repo is public.

## Using a prompt

Open the file under `prompts/`, fill in the `{{variables}}` described at the top, and paste it into your tool of choice.

## Conventions

### Skills

- Folder names use `kebab-case` and must match the `name` field in the frontmatter.
- `SKILL.md` starts with frontmatter:

  ```markdown
  ---
  name: my-skill
  description: One or two sentences on what it does AND when it should be triggered.
  ---
  ```

- The `description` is what the agent uses to decide whether to load the skill, so be specific about the triggering situation.
- One skill, one job. Move long reference material into `references/` so it is only loaded when needed.
- When a skill comes in a quick and a thorough variant, make both cover the same aspects and differ only in depth (see [`review-staged-changes`](skills/review-staged-changes/SKILL.md) and [`review-deeply-staged-changes`](skills/review-deeply-staged-changes/SKILL.md)).

### Prompts

Each prompt file should begin with:

1. **Purpose**: what the prompt achieves.
2. **Variables**: any `{{placeholders}}` and what to put in them.
3. **Example**: a short sample of input and expected output.

## Roadmap

- [ ] Skill and prompt templates in `templates/`
- [ ] `scripts/install.sh` to link or copy skills into an agent's skills directory
- [ ] `scripts/validate.sh` to lint frontmatter, naming and required files
- [ ] More skills and prompts
- [ ] Generated index of all skills with descriptions and tags
