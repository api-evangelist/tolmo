---
name: Triage and manage security findings
description: Use the Tolmo CLI to list, inspect, create, update, transition, and close security findings for an organization, with a full audit trail.
api: Tolmo CLI (tolmo findings)
method: generated
source: https://docs.tolmo.com/commands/findings
commands: [findings list, findings get, findings create, findings update, findings status, findings history, findings delete]
---

# Triage and manage security findings

Operating instructions for an agent using the Tolmo CLI to manage security findings. Ground every action in the real `tolmo findings` command surface. Always pass `--json` when parsing output programmatically.

## Prerequisites
- CLI installed (`brew install tolmohq/tolmo/tolmo` or `curl -fsSL https://tolmo.com/install.sh | sh`).
- Authenticated: interactive `tolmo auth login`, or set `TOLMO_API_TOKEN` + `TOLMO_ORG_SLUG` for CI/CD.
- Confirm context with `tolmo auth status` (shows active profile, API URL, org slug). Target a specific org with `--org <slug>`.

## Findings model
- **severity**: `critical` | `high` | `medium` | `low` | `info`
- **visibility**: `draft` (hidden from org members) | `published`
- **status**: `open` | `in_review` | `closed` | `acknowledged` | `false-positive`
- Finding IDs support prefix matching — the short 8-char ID from `findings list` works everywhere.

## Steps
1. **Survey open work**: `tolmo findings list --status open --severity critical --json`
2. **Inspect one finding**: `tolmo findings get <findingId> --json` (renders the markdown description body).
3. **Create a finding** (title + severity required; description inline or from file/stdin):
   ```
   tolmo findings create --title "Exposed S3 bucket" --severity high \
     --description-file ./finding.md --visibility published --status open
   ```
   Write the description like a CTO brief: one-sentence summary, the specific affected resource (bucket ARN / role name), blast radius, concrete next action + owner.
4. **Update fields** (only the fields you pass change): `tolmo findings update <findingId> --severity critical --visibility published`
5. **Transition status** (dedicated endpoint, status-only): `tolmo findings status <findingId> in_review` → `closed` / `acknowledged` / `false-positive`.
6. **Audit trail**: `tolmo findings history <findingId>` (every transition, timestamp, and user).
7. **Delete (permanent)**: `tolmo findings delete <findingId> --yes`.

## Conventions
- `--json` output schema is stable; table formatting may change between releases.
- New findings default to `draft` visibility and `open` status.
