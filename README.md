# project-rounds

A **Claude Code skill** that gives any project session continuity across multiple Claude sessions.

> _Don't lose the thread when the session resets._

## What it does

After every significant intervention, the skill ensures:

1. A per-project **work log** (`work/<item>.md`) gets a new **Round** entry with what was done.
2. A central **`work/CHANGELOG.md`** gets a dated bullet that indexes the per-project entry.

When a new Claude session starts, reading those two files restores full context in 30 seconds.

## Why

If you've ever closed Claude Code, opened it the next day, and lost the thread of where you were on a long-running project — this skill prevents that.

Especially useful when:
- Managing **multiple projects / clients / sites in parallel**
- Working on **long initiatives** (refactors, migrations, SEO campaigns, content production, infrastructure builds)
- Collaborating where another session (yours, tomorrow's, or a colleague's Claude) needs to pick up where you left off

## Install

### Globally (all your Claude Code projects)

```bash
git clone https://github.com/Bunz80/claude-project-rounds.git ~/.claude/skills/project-rounds
```

The skill becomes available in any session on your machine.

### Per-project only

```bash
cd <your-project>
git clone https://github.com/Bunz80/claude-project-rounds.git .claude/skills/project-rounds
```

## How it works

The skill defines a convention:

```
<project-root>/
├── work/
│   ├── CHANGELOG.md          ← cross-project index, reverse-chrono
│   ├── itemA.md              ← per-item log with numbered Rounds
│   ├── itemB.md
│   └── ...
```

When Claude detects a tracked item by name, it reads `work/<item>.md` to set context. When work is done, it appends to both files before closing the session.

## Templates

`templates/work-log-template.md` — boilerplate for a new `work/<item>.md`

`templates/changelog-entry-template.md` — examples of well-formed CHANGELOG entries

`templates/examples/seo-example/` — real-world example of using the system for SEO across multiple sites (with schema snippets, IndexNow patterns)

## Domain-agnostic

The skill is intentionally generic. Works equally for:
- Software development projects (feature branches, refactors, migrations)
- SEO / content / digital marketing (multi-site management)
- Operations / infrastructure (deploys, server changes, incidents)
- Research / writing (drafts, revisions, fact-checks)
- Client work (multi-client billing-tracked work)

Anything where you want a Claude-readable history.

## License

MIT — fork it, ship it.

## Contributing

PR welcome. If you've evolved the pattern (team-shared CHANGELOG, CI integration, anti-patterns), open an issue or PR.
