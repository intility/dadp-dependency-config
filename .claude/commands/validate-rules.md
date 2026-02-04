---
description: Validate rules.md matches JSON config and check for issues
---

# Validate Rules

Check that `rules.md` and JSON config files are in sync and valid.

## Checks

### 1. Parse rules.md
- Verify markdown table syntax is valid
- Extract all dependencies with their settings
- Report any parsing errors with line numbers

### 2. Read JSON Config
Read current state of:
- `presets/kubernetes.json` - all dependency rules

### 3. Compare Rules to Code

For each dependency in rules.md, verify in `kubernetes.json`:
- [ ] PackageRule exists with matching matchPackagePatterns
- [ ] Schedule matches
- [ ] Grouping settings match (separateMultipleMinor, etc.)
- [ ] Labels match
- [ ] Enabled status matches

For each dependency packageRule in JSON, verify:
- [ ] Corresponding entry exists in rules.md (except generic rules like "Pin Docker digests")

### 4. JSON Schema Validation

Verify JSON files:
- Valid JSON syntax
- Match Renovate schema
- No duplicate patterns
- Valid regex patterns

### 5. Safety Audit

Flag potential issues:
- Dependencies with `per-minor` grouping that don't have documentation justification
- Security dependencies (cert-manager, external-secrets) that are disabled
- Dependencies without labels
- Patterns that might be too broad (e.g., `*`)

### 6. Report

Output validation report:

```
rules.md Validation
===================

Dependencies: 10 defined
Parsing: OK

Sync Status
===========
ArgoCD - in sync
CloudNativePG - in sync
GitLab - rules.md has per-minor, JSON missing separateMultipleMinor
cert-manager - in sync
...

JSON Validation
===============
kubernetes.json - valid

Recommendations
===============
- Run /sync-rules to fix GitLab discrepancy
```

## Usage

Run this command to:
- Check if rules.md and JSON are in sync before making changes
- Verify changes after manual JSON edits
- Audit configuration for issues

$ARGUMENTS
