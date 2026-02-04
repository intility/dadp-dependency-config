# DADP Renovate Configuration

Centralized Renovate configuration for DADP repositories with rules-as-code workflow.

## Quick Start

Add to your repository's `renovate.json`:

```json
{
  "extends": ["github>intility/dadp-dependency-config"]
}
```

## What You Get

| Feature | Value |
|---------|-------|
| PR Schedule | 07:00-10:00 weekdays (Europe/Oslo) |
| Branch Prefix | `deps/` |
| Commit Format | `chore(deps): ...` |
| PR Limits | 5/hour, 10 concurrent |
| Automerge | Disabled |
| Dependency Dashboard | Enabled |

### Labels

- All PRs: `dependencies`, `renovate`
- Major updates: `breaking-change`
- Security patches: `security`
- Dependency-specific labels (see Dependencies table)

### ArgoCD Support

Automatically detects and updates `targetRevision` in:
- ArgoCD Application/ApplicationSet manifests
- Kustomize `helmCharts` blocks

## Managed Dependencies

| Dependency | Schedule | Grouping | Labels |
|------------|----------|----------|--------|
| cert-manager | Biweekly Monday | all | security |
| external-secrets | Biweekly Monday | all | security |
| ArgoCD | Biweekly Tuesday | all | argocd |
| CloudNativePG | Biweekly Wednesday | all | database |
| Barman Cloud | Biweekly Wednesday | all | database, backup |
| GitLab | 1st of month | per-minor | gitlab |
| Logfire | 1st of month | all | observability |
| Prometheus | 15th of month | all | observability |
| Envoy Gateway | 15th of month | all | infrastructure |
| KEDA | 15th of month | all | autoscaling |

### Schedule Distribution

Updates are spread across the month to avoid overwhelming reviewers:

```
Biweekly (weeks 1 & 3):
  Monday:    Security (cert-manager, external-secrets)
  Tuesday:   GitOps (ArgoCD)
  Wednesday: Database (CloudNativePG, Barman Cloud)

Monthly:
  1st:  GitLab, Logfire
  15th: Prometheus, Envoy Gateway, KEDA
```

## Extending

Override settings in your repository:

```json
{
  "extends": ["github>intility/dadp-dependency-config"],
  "packageRules": [
    {
      "matchPackageNames": ["my-internal-package"],
      "schedule": ["every weekend"],
      "addLabels": ["internal"]
    }
  ]
}
```

Use individual presets:

```json
{
  "extends": [
    "github>intility/dadp-dependency-config//presets/base",
    "github>intility/dadp-dependency-config//presets/kubernetes"
  ]
}
```

## Project Structure

```
├── rules.md              # Source of truth (human-readable)
├── default.json          # Main entry point
├── presets/
│   ├── base.json         # Core settings (timezone, PR limits, default labels)
│   ├── kubernetes.json   # K8s/ArgoCD detection + all dependency rules (schedules, labels, grouping)
│   └── automerge.json    # Automerge settings
└── .claude/
    └── commands/         # Claude Code commands
```

## Contributing (Claude Code)

This repository uses a rules-as-code workflow. `rules.md` is the source of truth.

### Workflow

```
Edit rules.md → /validate-rules → /sync-rules → JSON config
```

### Commands

| Command | Description |
|---------|-------------|
| `/new-dependency` | Add a new dependency (researches docs, suggests config) |
| `/sync-rules` | Sync rules.md to JSON with validation and safety checks |
| `/validate-rules` | Check rules.md matches JSON, find discrepancies |

### Adding a Dependency

1. Run `/new-dependency`
2. Provide dependency name
3. Claude researches upgrade docs and recommends settings
4. Review and confirm configuration
5. Run `/sync-rules` to generate JSON

### Safety Features

- Checks official docs before allowing risky changes
- Warns when removing `per-minor` from dependencies that require sequential upgrades
- Validates JSON against Renovate schema
- Verifies generated code matches rules

## Presets Reference

### base.json
Core settings: timezone, default schedule window, branch prefix, commit format, PR limits, default labels, and generic grouping rules (dev dependencies, Docker digests).

### kubernetes.json
- Kubernetes manifest detection (`.yaml`, `.yml`)
- Helm values detection (`values.yaml`, `values-*.yaml`)
- ArgoCD Application/ApplicationSet support
- Custom regex managers for `targetRevision`
- **All dependency rules** - each package has schedule, labels, and grouping in one place

### automerge.json
Automerge configuration (currently disabled). Security vulnerability alerts bypass schedules.

## Links

- [Renovate Documentation](https://docs.renovatebot.com/)
- [Renovate Presets](https://docs.renovatebot.com/config-presets/)
- [Package Rules](https://docs.renovatebot.com/configuration-options/#packagerules)
