# Crno Jaje MCP server

A free, read-only [Model Context Protocol](https://modelcontextprotocol.io) server for the deals online now on
[Crno Jaje](https://www.crnojaje.hr): discount vouchers for hotel stays, spa and thermal spas, trips, skiing, beauty,
health, restaurants, activities and courses in Croatia and nearby. Your AI assistant can search them, compare prices
and extra discounts, and link you to the deal page, where you buy the voucher. No account and no key.

The deals are written in Croatian; your assistant searches in Croatian and answers in your language.

- **Address:** `https://www.crnojaje.hr/mcp`
- **Transport:** Streamable HTTP (POST, JSON replies, no session)
- **Protocols:** 2026-07-28, and the initialize-based revisions 2024-11-05 to 2025-11-25 for older clients
- **MCP Registry:** `hr.crnojaje/ponude`
- **For people:** open [the address](https://www.crnojaje.hr/mcp) in a browser

The server runs at crnojaje.hr. This repository describes it: this README, [server.json](server.json) for the MCP
Registry, [.mcp.json](.mcp.json) for clients and [example requests](examples/requests.md).

## Add it to your assistant

**Claude** (claude.ai, desktop and mobile): Settings → Connectors → Add custom connector, name it Crno Jaje and paste
the address. No authentication.

**Claude Code:**

```bash
claude mcp add --transport http crnojaje https://www.crnojaje.hr/mcp
```

or put this repository's [.mcp.json](.mcp.json) in your project.

**ChatGPT:** Settings → Apps & Connectors → Advanced settings, turn on developer mode, then create a connector with
this address and no authentication.

**Cursor**, in `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "crnojaje": { "url": "https://www.crnojaje.hr/mcp" }
  }
}
```

**VS Code**, in `.vscode/mcp.json`:

```json
{
  "servers": {
    "crnojaje": { "type": "http", "url": "https://www.crnojaje.hr/mcp" }
  }
}
```

Menu names change between versions; any client that takes a remote MCP server URL works.

## Tools

All read-only: they only read the deals that are online now.

| Tool | What it does |
|---|---|
| `search_deals` | Search every deal online now by words, category, place, price, nights, persons, board and dates. Each result links to its deal page, with the price, the discount and the extra discounts active now. |
| `list_extra_discount_deals` | Deals with an extra discount active right now (newsletter, card or site-wide voucher), biggest first. |
| `get_deal` | Everything about a deal: every option with its price, nights, persons, board and periods, the conditions, the address, images and the page to buy it. |
| `find_similar_deals` | The deals most like a given deal: alternatives, upsell and cross-sell. |
| `list_categories` | The site's categories that have online deals now. |
| `list_places` | The regions, countries and towns that have online deals now. |
| `check_availability` | Free nights in a hotel deal's online booking calendar. |

Every tool's arguments are in [llms-full.txt](https://www.crnojaje.hr/llms-full.txt), and the server lists them
(`tools/list`).

## Try asking

- Find a weekend for two in Istria with half board under 250 EUR.
- Which spa deals in Slovenia take a family with two children?
- Show me today's deals with an extra discount.
- Is the hotel of this deal free from 12 to 14 December?

## Limits

At most 20 tool calls a minute from one network; over it, a tool error says when to retry. Prices, extra discounts and
availability change: the deal page is always right.

## Privacy

No account, no cookies, no session. You buy a voucher only on the deal page. See the
[privacy policy](https://www.crnojaje.hr/ZastitaOsobnihPodataka).

## For machines

- Server card: https://www.crnojaje.hr/mcp/server-card
- AI catalog: https://www.crnojaje.hr/.well-known/ai-catalog.json
- [llms.txt](https://www.crnojaje.hr/llms.txt) and [llms-full.txt](https://www.crnojaje.hr/llms-full.txt)
- Example requests: [examples/requests.md](examples/requests.md)

## Support

Write to us through the [contact page](https://www.crnojaje.hr/Kontakt).

## License

[MIT](LICENSE). Crno Jaje is run by Pravi Klik turistička agencija d.o.o., Vlaška 63, 10000 Zagreb, Croatia.
