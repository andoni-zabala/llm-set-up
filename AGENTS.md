# Repository Guidelines

## How to Use This Guide

- Start here for project-wide norms.
- Use the skills table below for detailed patterns on-demand.
- Skills override this file when guidance conflicts.

## Available Skills

Use these skills for detailed patterns on-demand:

### Technology Skills
| Skill | Description | URL |
|-------|-------------|-----|
| `ruby-sorbet` | Sorbet typing, T::Struct, sig blocks | [SKILL.md](skills/ruby-sorbet/SKILL.md) |
| `rspec` | RSpec patterns, FactoryBot, request specs | [SKILL.md](skills/rspec/SKILL.md) |
| `rails-8` | Rails 8 API-only, Solid Queue/Cache/Cable | [SKILL.md](skills/rails-8/SKILL.md) |

### Tooling Skills
| Skill | Description | URL |
|-------|-------------|-----|
| `skill-creator` | Create new AI agent skills | [SKILL.md](skills/skill-creator/SKILL.md) |
| `skill-sync` | Sync skill metadata to AGENTS.md | [SKILL.md](skills/skill-sync/SKILL.md) |

### Auto-invoke Skills

When performing these actions, ALWAYS invoke the corresponding skill FIRST:

| Action | Skill |
|--------|-------|
| Creating new skills | `skill-creator` |
| After creating/modifying a skill | `skill-sync` |
| Writing RSpec tests | `rspec` |
| Writing Sorbet type signatures | `ruby-sorbet` |
| Working with Rails 8 features | `rails-8` |

---

## Project Overview

<!-- TODO: Replace with your project description -->

| Component | Location | Tech Stack |
|-----------|----------|------------|
| API | `app/` | Ruby 3.3.5, Rails 8, PostgreSQL |
| Types | `sorbet/` | Sorbet, Tapioca |
| Tests | `spec/` | RSpec, FactoryBot |
| CI/CD | `.github/` | GitHub Actions |

---

## Development Commands

```bash
# Setup
bundle install

# Development
bin/rails server
bin/rails console

# Code quality
bundle exec rubocop --parallel
bundle exec srb tc
bundle exec tapioca check

# Testing
bundle exec rspec --format documentation

# Database
bin/rails db:prepare
bin/rails db:migrate
```

---

## CRITICAL RULES - NON-NEGOTIABLE

<!-- TODO: Replace with your project's rules -->

### Typing
- ALWAYS: Use Sorbet `sig` blocks for public methods
- ALWAYS: Use `T::Struct` for DTOs and entities (immutable `const` fields)
- ALWAYS: Keep `# typed: strict` on new files (except controllers at `# typed: false`)
- NEVER: Skip type annotations on repository or entity methods

---

## QA CHECKLIST

- [ ] `bundle exec rubocop --parallel` passes
- [ ] `bundle exec srb tc` passes
- [ ] `bundle exec rspec` passes
- [ ] No `binding.pry` in committed code
- [ ] Zeitwerk autoloading check passes (`bin/rails zeitwerk:check`)
- [ ] New files have appropriate `# typed:` sigil
