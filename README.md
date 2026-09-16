# mcp-mapillary

Mapillary MCP — wraps the Mapillary Graph API (graph.mapillary.com)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1576+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `mapillary_images_near` | Street-level images near a lat/lon point. Returns image ids, coordinates, capture time, compass heading, and a 1024px thumbnail URL. Example: mapillary_images_near({ lat: 40.7580, lon: -73.9855, radius: 30, _apiKey: "MLY\|..." }) |
| `mapillary_image` | Details for a Mapillary image. Given an image id, returns capture time, coordinates, compass heading, camera type, sequence id, and a 2048px thumbnail URL. Example: mapillary_image({ image_id: "498763468214164", _apiKey: "MLY\|..." }) |
| `mapillary_detections` | Detected objects/signs in an image. Returns Mapillary computer-vision detections (object/sign classes like "object--support--pole" or "regulatory--stop--g1") found inside a given image id. Example: mapillary_detections({ image_id: "498763468214164", _apiKey: "MLY\|..." }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "mapillary": {
      "url": "https://gateway.pipeworx.io/mapillary/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/mapillary/mcp` returns the tools in the table
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

Both URLs reach the same gateway and the same 1576+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "mapillary": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-mapillary"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-mapillary
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Mapillary data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
