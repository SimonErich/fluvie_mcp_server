---
layout: default
title: API Reference
---

# API Reference

The Fluvie MCP Server exposes HTTP endpoints for MCP communication and server management.

## Base URL

- **Public Server**: `https://mcp.fluvie.at`
- **Local Development**: `http://localhost:8080`

## Endpoints

### GET /

Returns an HTML documentation page for the server.

**Response**: `text/html`

### GET /health

Health check endpoint for monitoring and load balancers.

**Response**:
```json
{
  "status": "healthy",
  "version": "1.0.0",
  "uptime": 3600,
  "indices": {
    "docs": 75,
    "templates": 24
  }
}
```

### POST /mcp

Main MCP JSON-RPC endpoint. All MCP protocol communication goes through this endpoint.

**Headers**:
```
Content-Type: application/json
```

**Request Body**: JSON-RPC 2.0 message

**Response**: JSON-RPC 2.0 response

### GET /mcp/sse

Server-Sent Events stream for real-time updates.

**Response**: `text/event-stream`

**Events**:
- `message` - MCP notifications
- `ping` - Keep-alive (every 30 seconds)

### GET /resources/{path}

Direct access to server resources.

**Parameters**:
- `path` - Resource path (e.g., `docs/ai-reference`)

**Response**: Resource content with appropriate MIME type

---

## MCP Protocol

The server implements the [Model Context Protocol](https://modelcontextprotocol.io/) specification.

### Message Format

All MCP messages use JSON-RPC 2.0:

```json
{
  "jsonrpc": "2.0",
  "method": "method_name",
  "params": {},
  "id": 1
}
```

### Supported Methods

#### initialize

Initialize the MCP session.

```json
{
  "jsonrpc": "2.0",
  "method": "initialize",
  "params": {
    "protocolVersion": "2024-11-05",
    "capabilities": {},
    "clientInfo": {
      "name": "my-client",
      "version": "1.0.0"
    }
  },
  "id": 1
}
```

**Response**:
```json
{
  "jsonrpc": "2.0",
  "result": {
    "protocolVersion": "2024-11-05",
    "capabilities": {
      "tools": {},
      "resources": {}
    },
    "serverInfo": {
      "name": "fluvie-mcp-server",
      "version": "1.0.0"
    }
  },
  "id": 1
}
```

#### tools/list

List available tools.

```json
{
  "jsonrpc": "2.0",
  "method": "tools/list",
  "id": 1
}
```

**Response**:
```json
{
  "jsonrpc": "2.0",
  "result": {
    "tools": [
      {
        "name": "searchDocs",
        "description": "Search Fluvie documentation",
        "inputSchema": {...}
      }
    ]
  },
  "id": 1
}
```

#### tools/call

Call a tool with arguments.

```json
{
  "jsonrpc": "2.0",
  "method": "tools/call",
  "params": {
    "name": "searchDocs",
    "arguments": {
      "query": "animate text",
      "limit": 5
    }
  },
  "id": 1
}
```

**Response**:
```json
{
  "jsonrpc": "2.0",
  "result": {
    "content": [
      {
        "type": "text",
        "text": "{\"results\":[...]}"
      }
    ]
  },
  "id": 1
}
```

#### resources/list

List available resources.

```json
{
  "jsonrpc": "2.0",
  "method": "resources/list",
  "id": 1
}
```

#### resources/read

Read a resource by URI.

```json
{
  "jsonrpc": "2.0",
  "method": "resources/read",
  "params": {
    "uri": "fluvie://docs/ai-reference"
  },
  "id": 1
}
```

---

## Error Codes

| Code | Message | Description |
|------|---------|-------------|
| -32700 | Parse error | Invalid JSON |
| -32600 | Invalid Request | Invalid JSON-RPC |
| -32601 | Method not found | Unknown method |
| -32602 | Invalid params | Invalid parameters |
| -32603 | Internal error | Server error |

---

## CORS

The server supports CORS for browser-based clients:

**Allowed Origins**: Configurable via `CORS_ORIGINS` environment variable

**Allowed Methods**: `GET, POST, OPTIONS`

**Allowed Headers**: `Content-Type, Authorization`

---

## Rate Limiting

The public server implements rate limiting:

- **Default**: 100 requests per minute per IP
- **Configurable**: Via `RATE_LIMIT` environment variable

When rate limited, the server returns:

```json
{
  "jsonrpc": "2.0",
  "error": {
    "code": -32000,
    "message": "Rate limit exceeded"
  },
  "id": 1
}
```

---

## Examples

### cURL

```bash
# Health check
curl https://mcp.fluvie.at/health

# List tools
curl -X POST https://mcp.fluvie.at/mcp \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","method":"tools/list","id":1}'

# Search docs
curl -X POST https://mcp.fluvie.at/mcp \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","method":"tools/call","params":{"name":"searchDocs","arguments":{"query":"layer animation"}},"id":1}'
```

### JavaScript

```javascript
const response = await fetch('https://mcp.fluvie.at/mcp', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    jsonrpc: '2.0',
    method: 'tools/call',
    params: {
      name: 'searchDocs',
      arguments: { query: 'layer animation' }
    },
    id: 1
  })
});

const result = await response.json();
console.log(result);
```
