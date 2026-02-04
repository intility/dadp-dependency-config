---
name: renovate-config
description: DADP Renovate configuration. Use when working with dependency updates, ArgoCD apps, or Kubernetes manifests.
---

# DADP Renovate Config

## Rules as Code

`rules.md` is the **source of truth**. Edit the markdown tables, then sync to JSON.

```
rules.md (human-readable) → /sync-rules → JSON config (machine-readable)
```

## Workflow

1. **Add/modify dependencies**: Edit the Dependencies table in `rules.md`
2. **Validate**: Run `/validate-rules` to check for issues
3. **Sync**: Run `/sync-rules` to generate JSON config
4. **Review**: Check the generated changes

## Commands

| Command | Description |
|---------|-------------|
| `/new-dependency` | Add a new dependency (with doc research) |
| `/sync-rules` | Sync rules.md → JSON with validation |
| `/validate-rules` | Check rules.md matches JSON config |

## Files

| File | Purpose | Edit directly? |
|------|---------|----------------|
| `rules.md` | Source of truth | Yes |
| `presets/kubernetes.json` | K8s/ArgoCD rules + schedules + labels | No, use /sync-rules |
| `presets/base.json` | Core settings | Manual only |
| `presets/automerge.json` | Automerge config | Manual only |

## rules.md Format

```markdown
| Name | Patterns | Schedule | Grouping | Labels | Enabled |
|------|----------|----------|----------|--------|---------|
| Redis | `redis`, `ghcr.io/org/redis/*` | Monday | all | database | [x] |
```

**Grouping options**: `all`, `per-minor`, `per-major`, `separate`

**Schedule options**: `Biweekly Monday`, `Biweekly Tuesday`, `Biweekly Wednesday`, `1st of month`, `15th of month`

## Usage

```json
{"extends": ["github>intility/dadp-dependency-config"]}
```

## Safety Features

- `/sync-rules` checks upgrade docs before removing `per-minor` grouping
- Warns when disabling security-critical dependencies
- Validates JSON against Renovate schema
- Verifies generated code matches rules
