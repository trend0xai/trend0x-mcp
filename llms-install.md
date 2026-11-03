# Trend0x MCP — installation guide for AI agents (Cline, etc.)

Trend0x is a **remote** MCP server. **Do not clone, build, or run anything locally** — there is no package to install. You only need to add a config entry pointing at the hosted endpoint.

- Endpoint: `https://app.trend0x.com/api/mcp`
- Transport: Streamable HTTP (in Cline: `"type": "streamableHttp"`)
- Auth: HTTP header `Authorization: Bearer <AGENT_TOKEN>`

## Step 1 — Get the user's agent token

Ask the user for their Trend0x agent token. If they don't have one:

1. Sign in (or sign up) at https://app.trend0x.com
2. Open **Profile settings**
3. Create an **agent token** and copy it

Rules:
- Never invent a token and never print it back in chat or commit it to a repository.
- Do **not** ask for exchange API keys. Exchanges are connected by the user inside the Trend0x app, not through MCP.

## Step 2 — Add the server config

### Cline

Open the MCP settings file (`cline_mcp_settings.json`, via *MCP Servers → Configure*) and add this entry inside `mcpServers`, replacing `YOUR_AGENT_TOKEN` with the user's token:

```json
{
  "mcpServers": {
    "trend0x": {
      "type": "streamableHttp",
      "url": "https://app.trend0x.com/api/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_AGENT_TOKEN"
      },
      "disabled": false
    }
  }
}
```

Keep any existing servers in the file; merge, don't overwrite.

### Other clients

See the README for Claude Code / Claude Desktop, Cursor, VS Code and Gemini CLI snippets. The values are always the same URL and the same `Authorization: Bearer ...` header.

## Step 3 — Verify the connection (read-only)

1. Confirm the server shows as connected and lists tools (about 115 tools, e.g. `get_market_data`, `run_backtest`, `get_portfolio_summary`).
2. Call the read-only tool **`get_market_data`** with:

   ```json
   { "symbol": "BTCUSDT" }
   ```

   Expected result: text like `Current price for BTCUSDT: $<price>`.

3. Optionally call **`terminal_config`** (no arguments) — it returns the user's masked profile and settings, confirming the token belongs to the right account.

## Troubleshooting

| Symptom | Fix |
|---|---|
| `Authentication Required. Please provide AGENT_TOKEN.` | The `Authorization` header is missing or malformed. It must be exactly `Bearer <token>` (with a space). |
| Tools list loads but calls fail | Token expired or revoked: create a new one in Profile settings. |
| Connection error | Check that the URL is exactly `https://app.trend0x.com/api/mcp` and the type is `streamableHttp`. |

## Safety notes for agents

- Tools that place orders, change leverage, transfer funds or close positions act on the user's real connected exchanges. Only call them when the user explicitly asks, and pass `confirm=true` only after the user has confirmed the specific action.
- For verification, use only read-only tools such as `get_market_data`.
- Trend0x is software, not financial advice. Risk disclaimer: https://trend0x.com/risk-disclaimer/

Support: https://t.me/supp0x · Docs: https://trend0x.gitbook.io/trend0x-docs
