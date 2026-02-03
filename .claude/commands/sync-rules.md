---
description: Sync rules.md to Renovate JSON config files with validation
---

# Sync Rules

Synchronize `rules.md` (source of truth) to Renovate JSON configuration files.

## Workflow

### 1. Parse rules.md

Read and parse the Dependencies table from `rules.md`. Extract for each dependency:
- Name
- Patterns (convert `*` glob to regex: `foo/*` → `^foo/`)
- Schedule (map to Renovate schedule syntax)
- Grouping strategy
- Labels
- Enabled status

### 2. Detect Changes

Compare parsed rules against current JSON files:
- `presets/schedules.json` - schedule rules
- `presets/kubernetes.json` - grouping, labels, dependency rules

List what will be added, modified, or removed.

### 3. Safety Checks (Critical)

For each **removed** or **modified** dependency rule, search documentation for safety concerns:

**High-risk changes to flag:**
- Removing `separateMultipleMinor` from dependencies that require sequential upgrades (GitLab, databases)
- Removing `separateMultipleMajor` from dependencies with breaking changes between majors
- Disabling updates for security-critical dependencies (cert-manager, external-secrets)
- Changing schedule to be less frequent for dependencies with security implications

**Search queries to run:**
- `"{dependency name}" upgrade path requirements`
- `"{dependency name}" version compatibility skip versions`

Present warnings to user and require confirmation for high-risk changes.

### 4. Generate JSON

Generate the updated JSON files:

**Schedule mapping:**
| rules.md | Renovate schedule |
|----------|-------------------|
| Biweekly Monday | `every 2 weeks on monday` |
| Biweekly Tuesday | `every 2 weeks on tuesday` |
| Biweekly Wednesday | `every 2 weeks on wednesday` |
| 1st of month | `on the 1st day of the month` |
| 15th of month | `on the 15th day of the month` |

**Grouping mapping:**
| rules.md | Renovate config |
|----------|-----------------|
| all | (default, no extra config) |
| per-minor | `"separateMultipleMinor": true` |
| per-major | `"separateMultipleMajor": true` |
| separate | `"separateMajorMinor": true` |

**Pattern conversion:**
- `foo/*` → `^foo/` (registry prefix)
- `foo` → `foo` (simple match)
- Escape dots in registry URLs: `ghcr.io` → `ghcr\\.io`

### 5. Validate JSON

After generating, validate:
- JSON syntax is valid
- Matches Renovate schema (`$schema` reference)
- No duplicate dependency patterns
- All referenced patterns are valid regex

### 6. Verify Code Matches Rules

Use a subagent to independently:
1. Read the generated JSON files
2. Read rules.md
3. Verify every rule in rules.md has corresponding JSON config
4. Verify no JSON config exists without a rule in rules.md
5. Report any discrepancies

### 7. Summary

Output a summary:
```
✓ Parsed 10 dependencies from rules.md
✓ Safety checks passed (or list warnings)
✓ Generated presets/schedules.json
✓ Generated presets/kubernetes.json
✓ JSON validation passed
✓ Code matches rules

Changes:
- Added: Envoy Gateway
- Modified: GitLab (added separateMultipleMinor)
- Removed: (none)
```

## Error Handling

- If rules.md has syntax errors, report line numbers and stop
- If safety check fails, require explicit user confirmation
- If JSON validation fails, show the error and don't write files
- If verification fails, show discrepancies and ask user how to proceed

$ARGUMENTS
