# cooler-kindly-web-search

Claude Code plugin wrapper for an optimized fork of [kindly-web-search-mcp-server](https://github.com/Shelpuk-AI-Technology-Consulting/kindly-web-search-mcp-server).

The original server does web search + page content extraction, but the extracted content is noisy - full of navigation elements, sidebars, cookie banners, and other UI chrome that wastes LLM context tokens.

This fork replaces the content extraction pipeline with [trafilatura](https://github.com/adbar/trafilatura), which uses statistical + rule-based content scoring to strip boilerplate and return only the actual article content as clean markdown. In benchmarks, trafilatura achieves an F-Score of 0.909 vs ~0.6 for the original BeautifulSoup + markdownify approach. The original pipeline is kept as a fallback.

Fork with the extraction changes: [skrabe/kindly-web-search-mcp-server](https://github.com/skrabe/kindly-web-search-mcp-server)

## Install via marketplace

```bash
/plugin marketplace add skrabe/skrabe-plugins
/plugin install cooler-kindly-web-search@skrabe-plugins
```

## Install as standalone MCP server

```bash
claude mcp add --transport stdio cooler-kindly-web-search \
  -e SERPER_API_KEY="$SERPER_API_KEY" \
  -e GITHUB_TOKEN="$GITHUB_TOKEN" \
  -e KINDLY_BROWSER_EXECUTABLE_PATH="$KINDLY_BROWSER_EXECUTABLE_PATH" \
  -- uvx --from git+https://github.com/skrabe/kindly-web-search-mcp-server \
  kindly-web-search-mcp-server start-mcp-server
```

## Setup

You need your own API key from one of the supported search providers. Add it to your shell profile (e.g. `~/.zshrc` or `~/.bashrc`):

```bash
# Pick one search provider:
export SERPER_API_KEY="your-key-here"       # https://serper.dev
# OR export TAVILY_API_KEY="your-key-here"  # https://tavily.com
# OR export SEARXNG_BASE_URL="https://your-instance.example.org"

# Optional - needed for page content extraction:
export KINDLY_BROWSER_EXECUTABLE_PATH="/path/to/chromium-or-chrome"

# Optional - improves GitHub content fetching:
export GITHUB_TOKEN="your-token-here"
```

## Tools

| Tool | Description |
|---|---|
| `web_search` | Search the web and return results with full page content as markdown |
| `get_content` | Fetch a single URL and return clean markdown |

## License

[MIT](LICENSE)
