---
layout: default
title: Fluvie MCP Server
---

# Fluvie MCP Server

The Fluvie MCP Server implements the [Model Context Protocol](https://modelcontextprotocol.io/) to provide AI assistants with deep knowledge of the Fluvie video composition library. It enables AI tools like Claude, Continue.dev, and other MCP-compatible clients to help you write Fluvie code more effectively.

## What is MCP?

The Model Context Protocol (MCP) is an open standard that allows AI assistants to access external tools and resources. The Fluvie MCP Server exposes:

- **5 Tools** for documentation search, code generation, and template suggestions
- **2 Resources** for accessing comprehensive reference documentation

## Quick Start

### Using the Public Server

The easiest way to get started is using the public server at `https://mcp.fluvie.dev`:

**Claude Desktop** (`claude_desktop_config.json`):
```json
{
  "mcpServers": {
    "fluvie": {
      "url": "https://mcp.fluvie.dev/mcp",
      "transport": "http"
    }
  }
}
```

**VS Code Continue.dev** (`.continue/config.json`):
```json
{
  "mcpServers": {
    "fluvie": {
      "url": "https://mcp.fluvie.dev/mcp"
    }
  }
}
```

### Self-Hosted

Run your own instance:

```bash
# Clone the repository
git clone https://github.com/SimonErich/fluvie_mcp_server.git
cd fluvie_mcp_server

# Install dependencies
dart pub get

# Run the server
dart run bin/server.dart
```

Or with Docker:

```bash
docker-compose up -d
```

## Available Tools

| Tool | Description |
|------|-------------|
| [searchDocs](tools/search-docs) | Search Fluvie documentation with TF-IDF ranking |
| [getTemplate](tools/get-template) | Get detailed information about a template |
| [suggestTemplates](tools/suggest-templates) | Get template recommendations based on use case |
| [getWidgetReference](tools/get-widget-ref) | Get widget documentation and examples |
| [generateCode](tools/generate-code) | Generate Fluvie code from natural language |

## Available Resources

| URI | Description |
|-----|-------------|
| `fluvie://docs/ai-reference` | Complete AI-optimized documentation |
| `fluvie://templates` | Template catalog in JSON format |

## Architecture

The MCP server is built with:

- **Dart** - Server-side implementation
- **Shelf** - HTTP server framework
- **TF-IDF Search** - Document ranking algorithm
- **Docker** - Containerized deployment

[Learn more about the architecture](architecture)

## Related Projects

- [fluvie](https://github.com/SimonErich/fluvie) - Main Flutter package
- [fluvie_website](https://github.com/SimonErich/fluvie_website) - Marketing website
