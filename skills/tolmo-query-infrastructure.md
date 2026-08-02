---
name: Query the infrastructure graph and database
description: Use the Tolmo CLI to run SQL against the organization database and Cypher against the infrastructure graph, including temporal (time-machine) queries.
api: Tolmo CLI (tolmo sql / tolmo cypher)
method: generated
source: https://docs.tolmo.com/commands/sql-cypher
commands: [sql, cypher]
---

# Query the infrastructure graph and database

Operating instructions for an agent querying a Tolmo organization's data stores. Use SQL for relational/tabular data and Cypher for traversing the infrastructure graph.

## Prerequisites
- Authenticated CLI (`tolmo auth login` or `TOLMO_API_TOKEN` + `TOLMO_ORG_SLUG`); confirm with `tolmo auth status`.
- Always pass `--json` when parsing programmatically.

## Graph data model
- Resources are `GraphNode` (`resourceType`, `resourceKey`); relationships are `GRAPH_EDGE` (`type`).
- Both carry temporal fields `firstSeenAt` and `lastSeenAt` (epoch milliseconds).

## Steps
1. **Verify connectivity**: `tolmo sql "SELECT 1"` (add `--json` for scripting).
2. **Explore structured data** with SQL: `tolmo sql --json "SELECT ... FROM ..."`.
3. **Traverse the graph** with Cypher:
   ```
   tolmo cypher "MATCH (n) RETURN labels(n), count(*)"
   tolmo cypher --json "MATCH (n) RETURN n LIMIT 5"
   ```
4. **Time-machine queries** using temporal fields:
   ```
   # Resources added in the last 7 days
   tolmo cypher "MATCH (n:GraphNode) WHERE n.firstSeenAt >= (timestamp() - 7*24*60*60*1000) RETURN n.resourceType, n.resourceKey ORDER BY n.firstSeenAt DESC"
   # Stale resources not seen in the last 48 hours
   tolmo cypher "MATCH (n:GraphNode) WHERE n.lastSeenAt < (timestamp() - 48*60*60*1000) RETURN n.resourceType, n.resourceKey LIMIT 50"
   ```
5. **Target a specific org** for one command: add `--org <slug>`.

## Conventions
- `--json` returns a raw JSON array of rows; pipe to `jq`. Table output is for humans and may change across versions.
