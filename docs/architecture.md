---
layout: default
title: Architecture
---

# Architecture

The Fluvie MCP Server is designed as a stateless HTTP service that implements the Model Context Protocol specification.

## System Overview

```
┌─────────────────────────────────────────────────────────────┐
│                     AI Client                                │
│  (Claude Desktop, Continue.dev, Cursor, etc.)               │
└─────────────────────┬───────────────────────────────────────┘
                      │ MCP Protocol (JSON-RPC over HTTP)
                      ▼
┌─────────────────────────────────────────────────────────────┐
│                   Fluvie MCP Server                          │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │
│  │   HTTP      │  │    MCP      │  │    Tool/Resource    │  │
│  │  Handler    │──│   Handler   │──│      Registry       │  │
│  └─────────────┘  └─────────────┘  └─────────────────────┘  │
│         │                                    │               │
│         ▼                                    ▼               │
│  ┌─────────────┐              ┌──────────────────────────┐  │
│  │ Middleware  │              │        Tools             │  │
│  │  - CORS     │              │  - searchDocs            │  │
│  │  - Logging  │              │  - getTemplate           │  │
│  └─────────────┘              │  - suggestTemplates      │  │
│                               │  - getWidgetReference    │  │
│                               │  - generateCode          │  │
│                               └──────────────────────────┘  │
│                                          │                   │
│                                          ▼                   │
│                               ┌──────────────────────────┐  │
│                               │      Indexing            │  │
│                               │  - DocIndexer            │  │
│                               │  - SearchEngine (TF-IDF) │  │
│                               │  - TemplateIndex         │  │
│                               └──────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────────┐
│                 Documentation Files                          │
│         (Mounted from Fluvie main repository)                │
└─────────────────────────────────────────────────────────────┘
```

## Components

### HTTP Handler

The server uses [Shelf](https://pub.dev/packages/shelf) as the HTTP framework, providing:

- RESTful endpoints for health checks and documentation
- MCP JSON-RPC endpoint at `/mcp`
- Server-Sent Events stream at `/mcp/sse`
- Resource access at `/resources/{path}`

### MCP Handler

Implements the MCP protocol specification:

- **JSON-RPC 2.0** message format
- **Method routing** for `tools/list`, `tools/call`, `resources/list`, `resources/read`
- **Error handling** with proper MCP error codes

### Tool Registry

Manages available MCP tools:

```dart
class ToolRegistry {
  void register(ToolDefinition tool);
  List<ToolDefinition> listTools();
  Future<Map<String, dynamic>> callTool(String name, Map<String, dynamic> args);
}
```

### Resource Registry

Manages available MCP resources:

```dart
class ResourceRegistry {
  void register(ResourceDefinition resource);
  List<ResourceDefinition> listResources();
  Future<String> readResource(String uri);
}
```

### Indexing System

#### DocIndexer

Parses and indexes markdown documentation files:

- Extracts metadata (title, category, tags)
- Builds searchable document corpus
- Generates snippets for search results

#### SearchEngine

Implements TF-IDF (Term Frequency-Inverse Document Frequency) ranking:

```
score(term, doc) = TF(term, doc) × IDF(term)

TF(term, doc) = frequency of term in document
IDF(term) = log(total docs / docs containing term)
```

#### TemplateIndex

Catalogs available Fluvie templates:

- Categories: intro, ranking, data_viz, collage, thematic, conclusion
- Metadata: name, description, parameters, example usage
- Use case matching for recommendations

## Request Flow

1. **Client sends MCP request** to `/mcp` endpoint
2. **HTTP handler** receives and validates request
3. **MCP handler** parses JSON-RPC message
4. **Tool/Resource registry** routes to appropriate handler
5. **Tool executes** (e.g., search, code generation)
6. **Response formatted** as JSON-RPC response
7. **HTTP handler** returns response to client

## Configuration

The server is configured via environment variables:

| Variable | Default | Description |
|----------|---------|-------------|
| `PORT` | `8080` | HTTP server port |
| `HOST` | `0.0.0.0` | Bind address |
| `DOCS_PATH` | `../doc` | Path to documentation files |
| `LOG_LEVEL` | `info` | Logging verbosity |

## Deployment Architecture

### Docker Deployment

```
┌─────────────────────────────────────────┐
│              Docker Host                 │
│  ┌───────────────────────────────────┐  │
│  │     fluvie-mcp container          │  │
│  │  ┌─────────────────────────────┐  │  │
│  │  │   Dart AOT Binary           │  │  │
│  │  │   (fluvie_mcp_server)       │  │  │
│  │  └─────────────────────────────┘  │  │
│  │              │                     │  │
│  │              ▼                     │  │
│  │  ┌─────────────────────────────┐  │  │
│  │  │   /app/data/docs            │  │  │
│  │  │   (volume mount)            │  │  │
│  │  └─────────────────────────────┘  │  │
│  └───────────────────────────────────┘  │
│                  │ :8080                 │
└──────────────────┼──────────────────────┘
                   ▼
          External Traffic
```

### Production with Traefik

```
┌─────────────────────────────────────────┐
│              Docker Host                 │
│  ┌───────────────────────────────────┐  │
│  │         Traefik Proxy             │  │
│  │  - HTTPS termination              │  │
│  │  - Let's Encrypt certs            │  │
│  │  - Rate limiting                  │  │
│  └───────────────┬───────────────────┘  │
│                  │                       │
│  ┌───────────────▼───────────────────┐  │
│  │     fluvie-mcp container          │  │
│  └───────────────────────────────────┘  │
└─────────────────────────────────────────┘
```

## Security Considerations

- **Read-only documentation access** - no write operations
- **Rate limiting** - configurable request limits
- **CORS support** - configurable allowed origins
- **No authentication required** - public documentation server
- **Input validation** - all tool inputs validated against schema
