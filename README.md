# boothcheck MCP server

Ask any AI assistant what a stock's price is actually betting on.

boothcheck inverts a US stock's market price into the bet it implies: the operating-income growth rate, the years it must be sustained, and the operating margin the price assumes, measured against what the company has actually delivered. Every number traces to SEC EDGAR filings through a reverse-DCF read (expectations investing). It never returns a fair value, a price target, or a buy/sell rating. It shows the bet, so you can judge it.

This MCP server exposes that decomposition for about 2,000 US tickers. Read-only. Five tools work with no account at all; the rest come with a boothcheck plan.

## Connect

The setup walkthrough, with one-click installers for Cursor and VS Code, lives at **https://boothcheck.com/connect**. It covers everything below and fills in the URL for you.

There are two endpoints, and they are not interchangeable.

| Endpoint | Auth | What you get |
| --- | --- | --- |
| `https://boothcheck.com/api/mcp` | none, or `Authorization: Bearer bck_...` | Keyless: the five free tools. With an API key: everything on that plan. |
| `https://boothcheck.com/api/mcp-auth` | OAuth 2.1 | Sign in through the browser once. Everything on your plan, tokens refresh themselves. |

A keyless client pointed at `/api/mcp-auth` gets a 401, so pick the one that matches how you want to authenticate.

**Claude and Claude Desktop.** Settings, then Connectors, then Add custom connector. Paste an endpoint. For the OAuth one, choose Connect and approve the read-only permission. Then switch boothcheck on under Search and tools in a conversation.

**ChatGPT.** Settings, then Apps and Connectors, then Advanced, and turn on developer mode. Create a new connector with the endpoint. The server ships an Apps SDK widget, so results render as an inline priced-in card.

**Claude Code**

```
claude mcp add --transport http boothcheck https://boothcheck.com/api/mcp-auth
```

**Any MCP client (JSON config)**

A config-file client cannot run the OAuth browser step, so use the keyless endpoint and attach an API key. Create one in account settings; keys are shown once and stored hashed.

```json
{
  "mcpServers": {
    "boothcheck": {
      "type": "http",
      "url": "https://boothcheck.com/api/mcp",
      "headers": { "Authorization": "Bearer bck_YOUR_KEY" }
    }
  }
}
```

## Tools

**Free and keyless. No account, nothing to approve.**

| Tool | What it does |
| --- | --- |
| `whats_priced_in` | Invert one ticker's price into implied growth, duration, and margin, against what the company earns today. |
| `most_stretched` | Rank stocks by how much growth is already baked into their price. |
| `compare_priced_in` | Side by side of the assumptions embedded in two prices. |
| `list_coverage` | The coverage inventory: every ticker with a live record and its resolution level. |
| `get_report_summary` | Bullet takeaways of a boothcheck research report. Public reports keyless; the full library needs a Research key. |

**With a free API key**

| Tool | What it does |
| --- | --- |
| `get_guidance_ledger` | Management guidance behavior from 8-K filings: lifetime counts of raises, cuts, initiations, withdrawals, and reaffirmations. A Research key adds the per-year history and the scored delivery record. |

**With a Research plan**

| Tool | What it does |
| --- | --- |
| `get_report` | Full narrative report sections: valuation, bull case, bear case, takeaways, catalysts. |
| `get_iee_record` | The full structured price inversion: solved variables, held parameters, discount basis, sensitivity, plausibility panel. |
| `get_segments` | Clean reportable-segment snapshots from the latest 10-K segment note. |
| `get_peer_cohort` | The peer cohorts we compare a company against, per segment, with how each was matched. Cohort multiple medians are not served; they are computed inside the engine per valuation line, not stored as standalone facts. |
| `get_risk_factor_deltas` | Risk factors added to the latest 10-K versus the prior year, as verbatim Item 1A headings. |
| `get_insider_signals` | The current insider register from Forms 3/4/5: officers and directors, their current reported stakes, and lifetime filing counts. Current facts only, no performance claims. |
| `get_holders` | The current 13F institutional holders register: fund families, concentration, positions opened and closed. |

**With an Analyst/API plan**

| Tool | What it does |
| --- | --- |
| `analyst_desk` | One standardized table across 2 to 25 companies plus a deterministic research brief naming the largest differences. No model runs, no ranking, no fair value. |

The same data is served as plain REST at `https://boothcheck.com/api/v1`, with an OpenAPI 3.1 spec at `https://boothcheck.com/api/v1/openapi.json` generated from the same registry that runs the endpoints. Docs: https://boothcheck.com/developers

Example prompts once connected:

- "What is NVDA's price betting on?"
- "Which stocks are priced for the most right now?"
- "Compare what AMD and NVDA prices each assume."

## What you get back

Plain-language reads like:

> NVIDIA Corp (NVDA), as of 2026-06-28: At today's price, NVDA is priced for growth of +30.8% sustained for about 5.6 years. The more the price assumes beyond what the company has delivered, the more has to go right to justify it.

plus structured fields (`impliedGrowthPct`, `impliedDurationYears`, `impliedMarginPct`, `currentMarginPct`, `characterization`) and a link to the full report with the bull case, bear case, and valuation X-ray.

A price does not always resolve to a single number. Where the engine cannot solve one defensibly it says so in words rather than returning a point, and those reads are labelled as bounds, not estimates.

## Rules of the road

- No fair values, no price targets, no ratings. Describe output as "what the price implies," not as a recommendation.
- For informational and research purposes only. Not investment advice. boothcheck is not a registered investment adviser.
- Data is precomputed from SEC EDGAR filings and refreshed on boothcheck's regen cadence; each response carries its as-of date.
- Coverage is roughly 2,000 US tickers and grows filing by filing. Some tools cover fewer names than others. When a tool returns nothing for a ticker, that is the answer, not an error to retry.

## Registry

Published in the [Official MCP Registry](https://registry.modelcontextprotocol.io) as `com.boothcheck/boothcheck` (see `server.json`). Aggregators like PulseMCP ingest from there. The listed remote is the keyless endpoint, so a registry install works with no account.

## Links

- Site: https://boothcheck.com
- Connect an AI agent: https://boothcheck.com/connect
- API docs: https://boothcheck.com/developers · API terms: https://boothcheck.com/api-terms
- Methodology (why no price targets): https://boothcheck.com/methodology
- Privacy: https://boothcheck.com/privacy · Terms: https://boothcheck.com/terms
- Contact: privacy@boothcheck.com
