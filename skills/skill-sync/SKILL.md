---
name: skill-sync
description: >
  Syncs skill metadata to AGENTS.md Auto-invoke sections.
  Trigger: When updating skill metadata (metadata.scope/metadata.auto_invoke), regenerating Auto-invoke tables, or running ./skills/skill-sync/assets/sync.sh.
license: MIT
metadata:
  author: ""
  version: "1.0"
  scope: [root]
  auto_invoke:
    - "After creating/modifying a skill"
    - "Regenerate AGENTS.md Auto-invoke tables (sync.sh)"
    - "Troubleshoot why a skill is missing from AGENTS.md auto-invoke"
---

## Purpose

Keeps AGENTS.md Auto-invoke sections in sync with skill metadata. When you create or modify a skill, run the sync script to automatically update the AGENTS.md file.

## Required Skill Metadata

Each skill that should appear in Auto-invoke sections needs these fields in `metadata`:

```yaml
metadata:
  author: ""
  version: "1.0"
  scope: [root]                                    # Which AGENTS.md to update
  auto_invoke: "Creating/modifying components"     # Single action
  # OR
  # auto_invoke:
  #   - "Creating/modifying components"            # Multiple actions
  #   - "Refactoring component folder placement"
```

### Scope Values

| Scope | Updates |
|-------|---------|
| `root` | `AGENTS.md` (repo root) |
| `app` | `AGENTS.md` (repo root) |
| `spec` | `AGENTS.md` (repo root) |

All scopes map to the root `AGENTS.md` for single-component projects.

---

## Usage

```bash
# Sync AGENTS.md
./skills/skill-sync/assets/sync.sh

# Dry run (show what would change)
./skills/skill-sync/assets/sync.sh --dry-run
```

---

## Checklist After Modifying Skills

- [ ] Added `metadata.scope` to new/modified skill
- [ ] Added `metadata.auto_invoke` with action description
- [ ] Ran `./skills/skill-sync/assets/sync.sh`
- [ ] Verified AGENTS.md updated correctly
