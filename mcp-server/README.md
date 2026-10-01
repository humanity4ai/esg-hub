# ESG Hub MCP Server

A [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) server that provides AI agents with access to the **ESG Hub** knowledge base — 307 articles and 244 curated external resources covering Environmental, Social, and Governance topics.

## Quick Start

### Claude Desktop

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "esg-hub": {
      "command": "npx",
      "args": ["-y", "@esg-hub/mcp-server"]
    }
  }
}
```

### Cursor / Windsurf

Add to your MCP settings:

```json
{
  "esg-hub": {
    "command": "npx",
    "args": ["-y", "@esg-hub/mcp-server"]
  }
}
```

### Manual (from source)

```bash
cd mcp-server
pnpm install
pnpm build
node dist/index.js
```

## Available Tools

12 tools: 10 read-only and 2 that write (both need `ESG_HUB_WRITE_TOKEN`).

| Tool | Description | Annotations |
|------|-------------|-------------|
| `search_esg` | Full-text keyword search across ESG articles (BM25 ranking) | read-only, idempotent |
| `get_esg_page` | Full content of one ESG article by permalink or slug | read-only, idempotent |
| `list_esg_pages` | List/filter ESG articles by section, pillar, or title, paginated | read-only, idempotent |
| `list_esg_resources` | List/filter curated external ESG resources by domain or title, paginated | read-only, idempotent |
| `get_esg_metadata` | Database statistics (pages, sections, pillars, domains) | read-only, idempotent |
| `search_content` | Hybrid semantic + keyword search across all ESG Hub content | read-only, idempotent |
| `get_term` | One glossary term with its definition and related frameworks | read-only, idempotent |
| `get_related` | Traverse the knowledge graph from a page or term | read-only, idempotent |
| `list_frameworks` | ESG reporting frameworks and standards (GRI, SASB, TCFD, ESRS, etc.) | read-only, idempotent |
| `list_industries` | Industry taxonomy, for valid industry filter values | read-only, idempotent |
| `propose_term` | Submit a glossary term for review (needs write token) | write, non-destructive |
| `tag_content` | Update facet tags on a page (needs write token) | write, non-destructive, idempotent |

By default the server calls the public API at `https://esg-hub.ascent.partners`; set `ESG_HUB_API_BASE` to point it elsewhere (for example a local `http://localhost:3000`).

### v1.1.0 behaviors

- **Pagination:** `list_esg_pages` / `list_esg_resources` return `structuredContent.pagination` with `count`, `total`, `offset`, `has_more`, and `next_offset` — pass `next_offset` as the next call's `offset` to page through results.
- **Structured output:** every tool returns a `structuredContent` payload alongside the human-readable text.
- **Error envelope:** failures return `isError: true` with `structuredContent.error = { code, message, retryable, hint }` — codes: `NOT_FOUND` (retryable: false), `UPSTREAM_ERROR` (retryable on 5xx/network).
- **Empty search guidance:** zero-result searches return guidance text suggesting broader terms or browsing by section.

## Example Prompts

Once connected, you can ask your AI assistant questions like:

- "What are the key ESG reporting standards?"
- "Find information about Scope 3 emissions"
- "List all articles about corporate governance"
- "What external resources are available from the GRI?"
- "Explain the TCFD framework using the ESG Hub"

## Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `ESG_HUB_API_URL` | `https://esg-hub.ascent.partners` | Base URL of the ESG Hub API |

## API Reference

The MCP server wraps the ESG Hub REST API v1. See the [API Documentation](https://esg-hub.ascent.partners/developers/api) for full endpoint details.

## License

MIT — Content from the ESG Hub is licensed under CC BY-SA 4.0 by Ascent Partners Foundation.
