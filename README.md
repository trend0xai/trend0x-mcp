<p align="center"><img src="logo.png" width="120" alt="Trend0x logo"></p>

# Trend0x MCP Server

**AI trading terminal for crypto and stocks, as a remote MCP server.**

Trend0x connects your AI agent (Claude, Cursor, VS Code / Copilot, Cline, Gemini CLI or any MCP client) to a full trading terminal. You can describe a strategy in plain language, save it as rules you can review, backtest it on historical candles, run it in a paper-trading runner and monitor your portfolio. Everything happens in the chat.

| | |
|---|---|
| **Remote URL** | `https://app.trend0x.com/api/mcp` |
| **Transport** | Streamable HTTP (JSON responses). Legacy SSE is also served at the same URL. |
| **Auth** | `Authorization: Bearer <AGENT_TOKEN>` |
| **Tools** | 115 |
| **MCP Registry name** | `io.github.trend0xai/trend0x` |

> This repository contains documentation and client configuration only. The server is hosted by Trend0x; no server source code is published here.

## Capabilities (115 tools)

- **Market data:** tickers, candles, order books, funding rates, open interest and instrument specs for Bybit, OKX, Binance, Bitpanda, WhiteBIT and Alpaca.
- **Research & sentiment:** cited ticker research, market news, social buzz, 0–100 sentiment scores, watchlist digests and earnings recaps.
- **Strategies & backtests:** save private strategy drafts, run multi-pair / multi-timeframe backtests, review results and start strategies in a verified paper runner.
- **Portfolio & risk:** balances, positions, PnL reports and performance and risk metrics across connected exchanges. Guardrails include position sizing, risk/reward checks, a max daily loss limit and a circuit breaker.
- **Scanner & automation:** configure market scanners, create monitoring tasks and push reports to your dashboard and your own Telegram.
- **User-controlled execution:** live orders, leverage changes and transfers work only on exchanges *you* connected in the Trend0x app. Each one requires explicit confirmation (`confirm=true` or a confirmation code).

## Authentication

1. Sign in at [app.trend0x.com](https://app.trend0x.com).
2. Open **Profile settings** and create an **agent token**.
3. Send it with every request as `Authorization: Bearer <AGENT_TOKEN>`.

Keep the token private. Exchange API keys are connected inside the Trend0x app and are **never** passed through prompts, URLs or MCP config. Tool discovery (`tools/list`) works without a token; account tools need one.

The examples below read the token from the `TREND0X_AGENT_TOKEN` environment variable where the client supports it. Otherwise, replace `YOUR_AGENT_TOKEN`.

## Client setup

### Claude Desktop / Claude Code

Claude Code:

```bash
claude mcp add --transport http trend0x https://app.trend0x.com/api/mcp \
  --header "Authorization: Bearer YOUR_AGENT_TOKEN"
```

Claude Desktop (`claude_desktop_config.json`, via the `mcp-remote` bridge):

```json
{
  "mcpServers": {
    "trend0x": {
      "command": "npx",
      "args": [
        "-y", "mcp-remote", "https://app.trend0x.com/api/mcp",
        "--header", "Authorization:Bearer ${TREND0X_AGENT_TOKEN}"
      ],
      "env": { "TREND0X_AGENT_TOKEN": "YOUR_AGENT_TOKEN" }
    }
  }
}
```

### Cursor (`~/.cursor/mcp.json` or `.cursor/mcp.json`)

```json
{
  "mcpServers": {
    "trend0x": {
      "url": "https://app.trend0x.com/api/mcp",
      "headers": { "Authorization": "Bearer ${env:TREND0X_AGENT_TOKEN}" }
    }
  }
}
```

### VS Code / GitHub Copilot (`.vscode/mcp.json`)

```json
{
  "inputs": [
    { "type": "promptString", "id": "trend0x-token", "description": "Trend0x agent token", "password": true }
  ],
  "servers": {
    "trend0x": {
      "type": "http",
      "url": "https://app.trend0x.com/api/mcp",
      "headers": { "Authorization": "Bearer ${input:trend0x-token}" }
    }
  }
}
```

### Cline (`cline_mcp_settings.json`)

```json
{
  "mcpServers": {
    "trend0x": {
      "type": "streamableHttp",
      "url": "https://app.trend0x.com/api/mcp",
      "headers": { "Authorization": "Bearer YOUR_AGENT_TOKEN" },
      "disabled": false
    }
  }
}
```

### Gemini CLI

```bash
export TREND0X_AGENT_TOKEN=YOUR_AGENT_TOKEN
gemini extensions install https://github.com/trend0xai/trend0x-mcp
```

## Example prompts

1. "Backtest an EMA 20/50 crossover on BTCUSDT and ETHUSDT, 15m, last 90 days."
2. "Show my portfolio summary and PnL across connected exchanges."
3. "Give me a sentiment score and news brief for SOL before I trade."
4. "Create a scanner task that monitors BTC, ETH and XRP on 1h and alerts me on Telegram."
5. "What's my current risk exposure, and is a 3x long on ETH within my position-sizing policy?"

## Files in this repo

| File | Purpose |
|---|---|
| `server.json` | Official MCP Registry metadata |
| `mcp.json`, `.cursor-plugin/plugin.json` | Cursor plugin config |
| `gemini-extension.json` | Gemini CLI extension manifest |
| `llms-install.md` | Step-by-step install guide for AI agents (Cline etc.) |
| `logo.png` | 400×400 logo |

## Links

- Website: <https://trend0x.com>
- App: <https://app.trend0x.com>
- Docs: <https://trend0x.gitbook.io/trend0x-docs>
- Privacy policy: <https://app.trend0x.com/privacy>
- Terms of service: <https://app.trend0x.com/terms>
- Risk disclaimer: <https://trend0x.com/risk-disclaimer/>
- Support (Telegram): <https://t.me/supp0x> · Community: <https://t.me/trend0x> · X: <https://x.com/Trend0x_com>

## Risk disclaimer

Trend0x is software, not financial, investment, legal or tax advice. Trading crypto assets and securities involves substantial risk of loss, including loss of more than your initial investment when using leverage. Backtest and paper-trading results do not guarantee future performance. You are solely responsible for any orders you confirm. See the full [risk disclaimer](https://trend0x.com/risk-disclaimer/).

## License

The documentation and configuration files in this repository are released under the [MIT License](LICENSE). The hosted Trend0x service is governed by the [Terms of Service](https://app.trend0x.com/terms).
