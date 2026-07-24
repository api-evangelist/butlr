---
name: Find available space and current busyness
description: Locate free rooms and check how busy a space is right now using Butlr occupancy sensing.
api: mcp/butlr-mcp.yml
operations: [butlr_search_assets, butlr_available_rooms, butlr_space_busyness, butlr_get_current_occupancy]
source: https://docs.butlr.io/mcp-server
---

# Find available space and current busyness

Butlr provides anonymous, thermal-based occupancy sensing. Use the official
read-only MCP server (`@butlr/butlr-mcp-server`) or the underlying GraphQL +
Reporting APIs to answer availability and busyness questions.

## Auth
Set `BUTLR_CLIENT_ID` / `BUTLR_CLIENT_SECRET` (OAuth 2.0 client credentials,
read-only scopes are sufficient). See `authentication/butlr-authentication.yml`.

## Steps
1. Resolve the space. Call `butlr_search_assets` to fuzzy-match a site, building,
   floor, room, or zone by name (e.g. "huddle rooms in Building 2").
2. Find free rooms. Call `butlr_available_rooms`, filtering by capacity and tags,
   to list currently unoccupied rooms.
3. Check busyness. For a specific space call `butlr_space_busyness` (qualitative
   label + trend) or `butlr_get_current_occupancy` (last 5-minute median count).
4. Present the answer with the space name, current occupancy, and capacity.

## Rules
- The MCP server is read-only; it cannot book or modify anything.
- Occupancy counts are anonymous and camera-free; never imply identity of people.
- Event/occupancy timestamps are the source of truth; a single 5-minute median
  can lag rapid changes.
