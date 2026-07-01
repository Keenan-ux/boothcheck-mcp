# boothcheck MCP server

Ask any AI assistant what a stock's price is actually betting on.

boothcheck inverts a US stock's market price into the bet it implies: the operating-income growth rate, the years it must be sustained, and the operating margin the price assumes, measured against what the company has actually delivered. Every number traces to SEC EDGAR filings through a reverse-DCF read (expectations investing). It never returns a fair value, a price target, or a buy/sell rating. It shows the bet, so you can judge it.

This MCP server exposes that decomposition for ~1,950 US tickers. Free, keyless, read-only.

## Connect

The server speaks MCP over Streamable HTTP:

```
https://boothcheck.com/api/mcp
```

**Claude Desktop / claude.ai** — add a custom connector with the URL above (no auth).

**ChatGPT** — Settings → Apps & Connectors → Advanced → enable Developer mode, then add the URL above as a new connector. The server ships an Apps SDK widget, so results render as an inline priced-in card.

**Claude Code**

```
claude mcp add --transport http boothcheck https://boothcheck.com/api/mcp
```

**Any MCP client (JSON config)**

```json
{
  "mcpServers": {
    "boothcheck": {
      "type": "http",
      "url": "https://boothcheck.com/api/mcp"
    }
  }
}
```

## Tools

| Tool | What it does |
| --- | --- |
| `whats_priced_in` | Invert one ticker's price into implied growth, duration, and margin, vs what the company earns today. |
| `most_stretched` | Rank stocks by how much growth is already baked into their price. |
| `compare_priced_in` | Side-by-side of the assumptions embedded in two prices. |

Example prompts once connected:

- "What is NVDA's price betting on?"
- "Which stocks are priced for the most right now?"
- "Compare what AMD and NVDA prices each assume."

## What you get back

Plain-language reads like:

> NVIDIA Corp (NVDA), as of 2026-06-28: At today's price, NVDA is priced for growth of +30.8% sustained for about 5.6 years. The more the price assumes beyond what the company has delivered, the more has to go right to justify it.

plus structured fields (`impliedGrowthPct`, `impliedDurationYears`, `impliedMarginPct`, `currentMarginPct`, `characterization`) and a link to the full report with the bull case, bear case, and valuation X-ray.

## Rules of the road

- No fair values, no price targets, no ratings, ever. Describe output as "what the price implies," not as a recommendation.
- For informational and research purposes only. Not investment advice. boothcheck is not a registered investment adviser.
- Data is precomputed from SEC EDGAR filings and refreshed on boothcheck's regen cadence; each response carries its as-of date.

## Registry

Published in the [Official MCP Registry](https://registry.modelcontextprotocol.io) as `com.boothcheck/boothcheck` (see `server.json`). Aggregators like PulseMCP ingest from there.

## Links

- Site: https://boothcheck.com
- Methodology (why no price targets): https://boothcheck.com/methodology
- Privacy: https://boothcheck.com/privacy · Terms: https://boothcheck.com/terms
- Contact: privacy@boothcheck.com
