# Example requests

The server takes one JSON-RPC 2.0 request per POST to `https://www.crnojaje.hr/mcp` and gives one JSON reply. There is
no session, login or key. A tool's reply has its data twice: as JSON in `result.structuredContent` and as text in
`result.content`. The deals are in Croatian, as their pages word them; prices are in EUR.

## Revision 2026-07-28 (stateless)

Every request carries the protocol version and the client's capabilities in `params._meta`. The headers
`MCP-Protocol-Version`, `Mcp-Method` and, for `tools/call`, `Mcp-Name` repeat the body.

What the server is and what it supports:

```bash
curl -s https://www.crnojaje.hr/mcp -H 'Content-Type: application/json' -H 'Accept: application/json' \
  -H 'MCP-Protocol-Version: 2026-07-28' -H 'Mcp-Method: server/discover' \
  -d '{"jsonrpc":"2.0","id":1,"method":"server/discover","params":{"_meta":{"io.modelcontextprotocol/protocolVersion":"2026-07-28","io.modelcontextprotocol/clientCapabilities":{}}}}'
```

The tools and their arguments:

```bash
curl -s https://www.crnojaje.hr/mcp -H 'Content-Type: application/json' -H 'Accept: application/json' \
  -H 'MCP-Protocol-Version: 2026-07-28' -H 'Mcp-Method: tools/list' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/list","params":{"_meta":{"io.modelcontextprotocol/protocolVersion":"2026-07-28","io.modelcontextprotocol/clientCapabilities":{}}}}'
```

Wellness in Istria for two, an option at most 250 EUR:

```bash
curl -s https://www.crnojaje.hr/mcp -H 'Content-Type: application/json' -H 'Accept: application/json' \
  -H 'MCP-Protocol-Version: 2026-07-28' -H 'Mcp-Method: tools/call' -H 'Mcp-Name: search_deals' \
  -d '{"jsonrpc":"2.0","id":3,"method":"tools/call","params":{"name":"search_deals","arguments":{"query":"wellness","place":"Istra","price_max":250,"adults":2},"_meta":{"io.modelcontextprotocol/protocolVersion":"2026-07-28","io.modelcontextprotocol/clientCapabilities":{}}}}'
```

Two nights with half board on the coast, for two adults and a child:

```bash
curl -s https://www.crnojaje.hr/mcp -H 'Content-Type: application/json' -H 'Accept: application/json' \
  -H 'MCP-Protocol-Version: 2026-07-28' -H 'Mcp-Method: tools/call' -H 'Mcp-Name: search_deals' \
  -d '{"jsonrpc":"2.0","id":4,"method":"tools/call","params":{"name":"search_deals","arguments":{"seaside":true,"nights":2,"board":"half_board","adults":2,"children":1},"_meta":{"io.modelcontextprotocol/protocolVersion":"2026-07-28","io.modelcontextprotocol/clientCapabilities":{}}}}'
```

Skiing in Slovenia (a place can also be asked in English; the reply notes the Croatian name it searched):

```bash
curl -s https://www.crnojaje.hr/mcp -H 'Content-Type: application/json' -H 'Accept: application/json' \
  -H 'MCP-Protocol-Version: 2026-07-28' -H 'Mcp-Method: tools/call' -H 'Mcp-Name: search_deals' \
  -d '{"jsonrpc":"2.0","id":5,"method":"tools/call","params":{"name":"search_deals","arguments":{"query":"skijanje","place":"Slovenija"},"_meta":{"io.modelcontextprotocol/protocolVersion":"2026-07-28","io.modelcontextprotocol/clientCapabilities":{}}}}'
```

The deals with an extra discount today:

```bash
curl -s https://www.crnojaje.hr/mcp -H 'Content-Type: application/json' -H 'Accept: application/json' \
  -H 'MCP-Protocol-Version: 2026-07-28' -H 'Mcp-Method: tools/call' -H 'Mcp-Name: list_extra_discount_deals' \
  -d '{"jsonrpc":"2.0","id":6,"method":"tools/call","params":{"name":"list_extra_discount_deals","arguments":{"limit":5},"_meta":{"io.modelcontextprotocol/protocolVersion":"2026-07-28","io.modelcontextprotocol/clientCapabilities":{}}}}'
```

One deal's options and conditions, deals like it, and its hotel's free nights. Deals end, so take the id from a search
result above, and a check-in day in the future. Only some hotel deals have an online booking calendar (`get_deal`:
`online_booking_calendar`); for the others `check_availability` says so, and the voucher is booked as its conditions
say:

```bash
DEAL_ID=69940
CHECK_IN=2026-12-12
for TOOL in get_deal find_similar_deals check_availability; do
  ARGS='{"id":'$DEAL_ID'}'
  [ $TOOL = check_availability ] && ARGS='{"id":'$DEAL_ID',"check_in":"'$CHECK_IN'"}'
  curl -s https://www.crnojaje.hr/mcp -H 'Content-Type: application/json' -H 'Accept: application/json' \
    -H 'MCP-Protocol-Version: 2026-07-28' -H 'Mcp-Method: tools/call' -H "Mcp-Name: $TOOL" \
    -d '{"jsonrpc":"2.0","id":7,"method":"tools/call","params":{"name":"'$TOOL'","arguments":'"$ARGS"',"_meta":{"io.modelcontextprotocol/protocolVersion":"2026-07-28","io.modelcontextprotocol/clientCapabilities":{}}}}'
  echo
done
```

The names the `category` and `place` filters take:

```bash
for TOOL in list_categories list_places; do
  curl -s https://www.crnojaje.hr/mcp -H 'Content-Type: application/json' -H 'Accept: application/json' \
    -H 'MCP-Protocol-Version: 2026-07-28' -H 'Mcp-Method: tools/call' -H "Mcp-Name: $TOOL" \
    -d '{"jsonrpc":"2.0","id":8,"method":"tools/call","params":{"name":"'$TOOL'","arguments":{},"_meta":{"io.modelcontextprotocol/protocolVersion":"2026-07-28","io.modelcontextprotocol/clientCapabilities":{}}}}'
  echo
done
```

## Older clients (2025-11-25 to 2024-11-05)

An older client opens with `initialize`, then calls tools with its version in `MCP-Protocol-Version`. The server keeps
no session, so no session id is sent or needed:

```bash
curl -s https://www.crnojaje.hr/mcp -H 'Content-Type: application/json' -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-11-25","capabilities":{},"clientInfo":{"name":"example","version":"1.0"}}}'
curl -s https://www.crnojaje.hr/mcp -H 'Content-Type: application/json' -H 'Accept: application/json, text/event-stream' \
  -H 'MCP-Protocol-Version: 2025-11-25' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"list_extra_discount_deals","arguments":{"limit":5}}}'
```

## Limits

At most 20 tool calls a minute from one network; over it, a tool error says when to retry. Prices, extra discounts and
availability change: the deal page is always right.
