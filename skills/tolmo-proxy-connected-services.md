---
name: Proxy requests to connected services
description: Use the Tolmo CLI to send REST/GraphQL/CLI requests to GitHub, AWS, Linear, Sentry, and Datadog through Tolmo's secure proxy, with credentials resolved server-side.
api: Tolmo CLI (tolmo query)
method: generated
source: https://docs.tolmo.com/commands/query
commands: [query list, query github, query aws, query linear, query sentry, query datadog]
---

# Proxy requests to connected services

Operating instructions for an agent proxying requests to an organization's connected services through Tolmo. Credentials are resolved on the backend and never reach the client.

## Prerequisites
- Authenticated CLI (`tolmo auth login` or `TOLMO_API_TOKEN` + `TOLMO_ORG_SLUG`); confirm with `tolmo auth status`.

## Steps
1. **Discover connected services**: `tolmo query list` (or `tolmo query list --org <slug>`). Providers are discovered dynamically from the backend.
2. **GitHub (REST)**: `tolmo query github /repos/owner/repo/pulls?state=all`
3. **GitHub CLI passthrough** (full `gh` feature set, backend-injected token):
   ```
   tolmo query -- gh repo list myorg --limit 50
   tolmo query -- gh api search/issues -f "q=type:pr org:myorg" -f per_page=100
   ```
4. **AWS CLI passthrough**: `tolmo query -- aws ec2 describe-instances`
5. **Linear (GraphQL)**: `tolmo query linear '{ viewer { id name } }'` (or `--file query.graphql`).
6. **Sentry (REST)**: `tolmo query sentry /api/0/organizations/acme/issues/`
7. **Datadog (REST)**: `tolmo query datadog /api/v1/monitors`
8. **Disambiguate** multiple integrations of the same provider: add `--integration <id>` (IDs from `tolmo query list`).

## Critical rule
- The `--` separator before `gh`/`aws` is **mandatory**. Without it, Cobra strips unknown flags (`--repo`, `--region`, `--limit`, ...) before they reach the wrapped CLI, causing errors or silent failures.

## Security model
- Tolmo injects the org's GitHub App tokens, AWS role credentials, and Datadog/Sentry keys server-side; they never reach your machine.
