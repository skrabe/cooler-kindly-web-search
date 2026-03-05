# cooler-kindly-web-search

MCP server for web search with clean, LLM-ready content extraction. Optimized fork of [kindly-web-search-mcp-server](https://github.com/Shelpuk-AI-Technology-Consulting/kindly-web-search-mcp-server).

The original server does web search + page content extraction, but the extracted content is noisy — full of navigation elements, sidebars, cookie banners, and other UI chrome that wastes LLM context tokens.

This fork replaces the content extraction pipeline with [trafilatura](https://github.com/adbar/trafilatura), which uses statistical + rule-based content scoring to strip boilerplate and return only the actual article content as clean markdown. In benchmarks, trafilatura achieves an F-Score of 0.909 vs ~0.6 for the original BeautifulSoup + markdownify approach. The original pipeline is kept as a fallback.

Fork with the extraction changes: [skrabe/kindly-web-search-mcp-server](https://github.com/skrabe/kindly-web-search-mcp-server)

## Prerequisites

You need an API key from at least one search provider:

| Provider | Env Variable | Sign up |
|---|---|---|
| Serper | `SERPER_API_KEY` | [serper.dev](https://serper.dev) |
| Tavily | `TAVILY_API_KEY` | [tavily.com](https://tavily.com) |
| SearXNG | `SEARXNG_BASE_URL` | Self-hosted |

Add your chosen key to `~/.zshrc` or `~/.bashrc`:

```bash
export SERPER_API_KEY="your-key-here"
```

Optional environment variables:

| Variable | What it does |
|---|---|
| `KINDLY_BROWSER_EXECUTABLE_PATH` | Path to Chrome/Chromium binary. Enables JavaScript-rendered page extraction for sites that don't work with plain HTTP requests. |
| `GITHUB_TOKEN` | GitHub personal access token. Improves fetching of GitHub content by using the API instead of scraping, avoiding rate limits. |

## Install (Claude Code)

```bash
claude mcp add --transport stdio cooler-kindly-web-search \
  -e SERPER_API_KEY="$SERPER_API_KEY" \
  -- uvx --from git+https://github.com/skrabe/kindly-web-search-mcp-server \
  kindly-web-search-mcp-server start-mcp-server
```

## Install (Cursor, Windsurf, VS Code, etc.)

Add to your MCP client config:

```json
{
  "mcpServers": {
    "cooler-kindly-web-search": {
      "command": "uvx",
      "args": [
        "--from",
        "git+https://github.com/skrabe/kindly-web-search-mcp-server",
        "kindly-web-search-mcp-server",
        "start-mcp-server"
      ],
      "env": {
        "SERPER_API_KEY": "your-key-here"
      }
    }
  }
}
```

## Tools

| Tool | Description |
|---|---|
| `web_search` | Search the web and return results with full page content as markdown |
| `get_content` | Fetch a single URL and return clean markdown |

## License

[MIT](LICENSE)
