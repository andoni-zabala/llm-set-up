# LLM Setup

A portable, multi-agent skill system for AI-assisted development. Drop it into any project and give Claude Code, Gemini CLI, Codex, or GitHub Copilot structured knowledge about your codebase.

## The Problem

AI coding assistants work better with context. But maintaining instructions across multiple tools (Claude, Gemini, Copilot, Codex) means duplicating effort and keeping things in sync manually.

## The Approach

One source of truth (`AGENTS.md`) + modular skills that any AI assistant can consume:

```
your-project/
├── AGENTS.md              ← single source of truth for all AI agents
├── CLAUDE.md              ← auto-generated copy for Claude Code
├── GEMINI.md              ← auto-generated copy for Gemini CLI
├── .github/
│   └── copilot-instructions.md  ← auto-generated copy for Copilot
├── .claude/
│   └── skills -> ../skills      ← symlink
├── .gemini/
│   └── skills -> ../skills      ← symlink
├── .codex/
│   └── skills -> ../skills      ← symlink
└── skills/
    ├── setup.sh           ← configures all AI assistants at once
    ├── skill-creator/     ← meta-skill: how to create new skills
    ├── skill-sync/        ← auto-syncs skill metadata to AGENTS.md
    ├── rails-8/           ← example: Rails 8 patterns
    ├── rspec/             ← example: RSpec testing patterns
    └── ruby-sorbet/       ← example: Sorbet typing patterns
```

## Quick Start

**1. Copy into your project**

```bash
cp -r skills/ your-project/skills/
cp AGENTS.md your-project/AGENTS.md
```

**2. Run the setup script**

```bash
cd your-project
./skills/setup.sh
```

This launches an interactive menu to configure whichever AI assistants you use:

```
Which AI assistants do you use?

  [x] 1. Claude Code
  [ ] 2. Gemini CLI
  [ ] 3. Codex (OpenAI)
  [ ] 4. GitHub Copilot

  a. Select all
  n. Select none

Toggle (1-4, a, n) or Enter to confirm:
```

Or skip the menu:

```bash
./skills/setup.sh --all              # configure everything
./skills/setup.sh --claude --copilot # just these two
```

**3. Customize `AGENTS.md` for your project**

Replace the `<!-- TODO -->` sections with your project's overview, architecture, rules, and commands.

**4. Create project-specific skills**

```bash
# the AI will use the skill-creator skill to guide you
# just ask: "create a skill for [your pattern]"
```

## How Skills Work

Each skill is a `SKILL.md` file with YAML frontmatter and structured Markdown:

```
skills/my-pattern/
├── SKILL.md        # instructions the AI loads on-demand
├── assets/         # templates, schemas, scripts
└── references/     # links to local docs
```

Skills are loaded contextually. The `auto_invoke` metadata tells the AI *when* to load each skill:

```yaml
---
name: my-pattern
description: >
  What this skill covers.
  Trigger: When the AI should load this.
metadata:
  auto_invoke: "Creating or modifying components"
---
```

When an AI agent performs a matching action, it loads the skill automatically instead of guessing.

## Included Skills

### Tooling (use in any project)

| Skill | Purpose |
|-------|---------|
| `skill-creator` | Step-by-step guide for creating new skills |
| `skill-sync` | Keeps `AGENTS.md` auto-invoke tables in sync with skill metadata |

### Ruby/Rails (example skills)

| Skill | Purpose |
|-------|---------|
| `rails-8` | Rails 8 API-only mode, Solid Queue/Cache/Cable, Kamal |
| `rspec` | RSpec + FactoryBot patterns, request specs, matchers |
| `ruby-sorbet` | Sorbet typing, T::Struct, sig blocks, generics |

These serve as both useful references and examples of how to structure your own skills.

## Supported AI Assistants

| Assistant | Instructions File | Skills Directory |
|-----------|------------------|-----------------|
| Claude Code | `CLAUDE.md` | `.claude/skills/` |
| Gemini CLI | `GEMINI.md` | `.gemini/skills/` |
| Codex (OpenAI) | `AGENTS.md` (native) | `.codex/skills/` |
| GitHub Copilot | `.github/copilot-instructions.md` | n/a |

The setup script handles all the symlinks and file copies. `AGENTS.md` is always the source of truth -- edit it, then re-run `setup.sh` to propagate changes.

## License

MIT
