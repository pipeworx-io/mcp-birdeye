# mcp-birdeye

Birdeye MCP — wraps Birdeye Data Services (birdeye.so)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1683+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `birdeye_token_overview` | Token overview — price, liquidity, volume for token X. Returns market cap, 24h price change, and holder count for a Solana (or multichain) token by contract address. Example: birdeye_token_overview({ address: "So11111111111111111111111111111111111111112", chain: "solana", _apiKey: "your-key" }) |
| `birdeye_token_holders` | Top holders of token X. Returns the largest wallet holders of a token (owner address, token amount, and share of supply) by contract address. Example: birdeye_token_holders({ address: "So11111111111111111111111111111111111111112", limit: 20, chain: "solana", _apiKey: "your-key" }) |
| `birdeye_token_trades` | Recent DEX trades for token X. Returns the latest on-chain swap transactions for a token (side, amounts, price, trader, timestamp) by contract address. Example: birdeye_token_trades({ address: "So11111111111111111111111111111111111111112", limit: 20, chain: "solana", _apiKey: "your-key" }) |
| `birdeye_gainers_losers` | Top trader gainers/losers. Returns the ranked list of wallets by realized/unrealized PnL over a time window (yesterday/today/1W/30d/90d). Example: birdeye_gainers_losers({ type: "1W", sort_by: "PnL", chain: "solana", _apiKey: "your-key" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "birdeye": {
      "url": "https://gateway.pipeworx.io/birdeye/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/birdeye/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1683+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/birdeye_token_overview`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "birdeye": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-birdeye"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-birdeye
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Birdeye data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
