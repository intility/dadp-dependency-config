# DADP Renovate Configuration

> **Source of Truth**: Edit this file to change Renovate behavior. Run `/sync-rules` to apply changes.

## Settings

| Setting | Value |
|---------|-------|
| Branch Prefix | `deps/` |
| Commit Prefix | `chore(deps):` |
| Timezone | `Europe/Oslo` |
| Schedule | `07:00-10:00 weekdays` |
| Automerge | `disabled` |
| PR Limit | `5/hour`, `10 concurrent` |

## Dependencies

| Name | Patterns | Schedule | Grouping | Labels | Enabled |
|------|----------|----------|----------|--------|---------|
| cert-manager | `cert-manager`, `quay.io/jetstack/*` | Biweekly Monday | all | security | [x] |
| external-secrets | `external-secrets` | Biweekly Monday | all | security | [x] |
| ArgoCD | `quay.io/argoproj/*`, `argoproj/*`, `argo-cd` | Biweekly Tuesday | all | argocd | [x] |
| CloudNativePG | `ghcr.io/cloudnative-pg/*`, `cloudnative-pg` | Biweekly Wednesday | all | database | [x] |
| Barman Cloud | `plugin-barman-cloud` | Biweekly Wednesday | all | database, backup | [x] |
| GitLab | `registry.gitlab.com/*`, `gitlab/*` | 1st of month | per-minor | gitlab | [x] |
| Logfire | `logfire`, `us-docker.pkg.dev/pydantic-public-registries/*` | 1st of month | all | observability | [x] |
| Prometheus | `kube-prometheus-stack` | 15th of month | all | observability | [x] |
| Envoy Gateway | `gateway-helm` | 15th of month | all | infrastructure | [x] |
| KEDA | `ghcr.io/kedacore/*`, `keda` | 15th of month | all | autoscaling | [ ] |

### Schedule Distribution

```
Week 1 & 3:
  Mon: cert-manager, external-secrets (security)
  Tue: ArgoCD (gitops)
  Wed: CloudNativePG, Barman Cloud (database)

Monthly:
  1st:  GitLab, Logfire
  15th: Prometheus, Envoy Gateway, KEDA
```

### Grouping Options

- `all` - Group all updates into one PR (default)
- `per-minor` - Separate PR for each minor version (for sequential upgrades)
- `per-major` - Separate PR for each major version
- `separate` - Separate PRs for major vs minor/patch

### Schedule Options

- `Biweekly Monday`, `Biweekly Tuesday`, `Biweekly Wednesday` - Every 2 weeks
- `1st of month`, `15th of month` - Monthly schedules

## Default Labels

| Condition | Labels |
|-----------|--------|
| All PRs | `dependencies`, `renovate` |
| Major updates | `breaking-change` |
| Security patches | `security` |

## ArgoCD Detection

Renovate detects `targetRevision` in:
- ArgoCD Application/ApplicationSet manifests
- Kustomize `helmCharts` blocks
