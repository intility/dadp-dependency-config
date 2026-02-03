---
description: Add a new dependency to the Renovate configuration
---

# Add New Dependency

Add a new dependency to the DADP Renovate configuration by updating `rules.md`.

## Steps

### 1. Read Current State
Read `rules.md` to see existing dependencies and patterns.

### 2. Get Dependency Name
Ask the user for the dependency name.

### 3. Research Dependency

Search the web for the dependency's upgrade documentation:
- `"{dependency name}" upgrade guide`
- `"{dependency name}" version compatibility`
- `"{dependency name}" helm chart`

Look for:
- Official upgrade guides and paths
- Version compatibility requirements
- Warnings about skipping versions
- Database migration requirements
- Breaking changes between versions
- Recommended update frequency

### 4. Present Findings and Gather Input

Share what you found from documentation, then ask user for:

**Match patterns** - Based on how the dependency is distributed:
- Helm chart name (e.g., `redis`, `grafana`)
- Container registry (e.g., `ghcr.io/org/*`, `quay.io/org/*`)

**Schedule** - Suggest based on docs:
- Security-critical → more frequent (Monday/Weekdays)
- Complex upgrades → less frequent (monthly)
- Options: Monday, Tuesday, Weekdays, Biweekly Tuesday, 1st of month, 15th of month, Last day of month

**Grouping** - Recommend based on docs:
- If docs say "upgrade one minor at a time" → `per-minor`
- If docs mention breaking changes per major → `per-major`
- Otherwise → `all` (default)

**Labels** - Categorize the dependency:
- database, security, observability, infrastructure, argocd, backup, autoscaling, etc.

**Enabled** - Whether to enable updates immediately

### 5. Update rules.md

Add a new row to the Dependencies table in `rules.md`:

```markdown
| {Name} | `{patterns}` | {Schedule} | {grouping} | {labels} | [x] |
```

### 6. Run Sync

Tell the user to run `/sync-rules` to apply the changes to JSON files, or offer to do it automatically.

## Schedule Options

| rules.md | Description |
|----------|-------------|
| Biweekly Monday | Every 2 weeks on Monday |
| Biweekly Tuesday | Every 2 weeks on Tuesday |
| Biweekly Wednesday | Every 2 weeks on Wednesday |
| 1st of month | First day of month |
| 15th of month | 15th day of month |

## Grouping Options

| Value | When to use | Renovate setting |
|-------|-------------|------------------|
| all | Most dependencies (default) | (none) |
| per-minor | Sequential minor upgrades required | `separateMultipleMinor: true` |
| per-major | Each major needs own PR | `separateMultipleMajor: true` |
| separate | Major vs minor/patch split | `separateMajorMinor: true` |

$ARGUMENTS
