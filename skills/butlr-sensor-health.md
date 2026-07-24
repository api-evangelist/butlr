---
name: Check sensor and hardware health
description: Inspect Butlr sensor/hive topology and hardware health (online/offline, battery).
api: mcp/butlr-mcp.yml
operations: [butlr_hardware_snapshot, butlr_list_topology, butlr_get_asset_details]
source: https://docs.butlr.io/mcp-server
---

# Check sensor and hardware health

Use the read-only MCP tools (or the GraphQL asset API) to audit deployed
hardware across the portfolio.

## Auth
OAuth 2.0 client credentials (read-only scopes). See
`authentication/butlr-authentication.yml`.

## Steps
1. Map the estate. Call `butlr_list_topology` to render the
   organization -> site -> building -> floor -> room/zone -> sensor tree
   (control depth as needed).
2. Snapshot hardware. Call `butlr_hardware_snapshot` for online/offline status
   and battery levels across sensors and hives.
3. Drill in. For any flagged device call `butlr_get_asset_details` (or the
   GraphQL `sensors` / `hives` queries) for full attributes by id.
4. Report offline or low-battery devices with their floor/room location.

## Rules
- Read-only; the MCP server exposes no mutations. Provision/replace hardware in
  the Butlr web app.
- A Hive is the floor-level gateway; if a Hive is offline its downstream sensors
  may report stale data even if individually healthy.
