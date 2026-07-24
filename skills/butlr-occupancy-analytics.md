---
name: Analyze historical occupancy and traffic
description: Query time-series floor/room/zone occupancy and entryway traffic from the Butlr Reporting API.
api: https://api.butlr.io/api/v3/reporting
operations: [reporting_query, butlr_get_occupancy_timeseries, butlr_traffic_flow]
source: https://docs.butlr.io/historical-occupancy/reporting-api-overview
---

# Analyze historical occupancy and traffic

The Butlr Reporting API is a RESTful, time-series API for historical
space-utilization analysis. Use it (or the MCP tools
`butlr_get_occupancy_timeseries` / `butlr_traffic_flow`) to spot trends.

## Auth
`Authorization: Bearer <access_token>` (OAuth 2.0 JWT). See
`authentication/butlr-authentication.yml`.

## Steps
1. Identify the space id(s) with the GraphQL API or `butlr_search_assets`
   (ids look like `space_...`).
2. Build a query and `POST https://api.butlr.io/api/v3/reporting` with a JSON
   body containing `window`, `filter`, and `group_by`:
   - `window.every`: `1m`, `1h`, or `1d`.
   - `window.function`: `median` for 1-minute intervals, `mean`/`max` for larger
     intervals, `sum` for traffic.
   - `window.timezone`: an IANA timezone (required for traffic-based occupancy).
   - `filter.start` / `filter.stop`: absolute ISO-8601 times (avoid relative time).
   - `filter.measurements`: e.g. `traffic_floor_occupancy`, `room_occupancy`,
     `zone_occupancy`.
   - `filter.spaces.eq`: the space ids.
   - Add `window.create_empty: true` + `filter.value.gte: 0` to include zeros.
3. For entry/exit flow use `butlr_traffic_flow` (hourly breakdown, traffic-mode
   sensors) or the `traffic_*` measurements.
4. Aggregate results by `group_by.order: ["time"]`.

## Rules
- Prefer <24h ranges for 1-minute queries for performance.
- `traffic_*` (entry/exit) and presence-based `*_occupancy` counts differ by
  design - do not expect them to match (see the Reporting API FAQs).
- Traffic-mode occupancy resets to zero at local midnight.
