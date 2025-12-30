---
layout: default
title: Development
---

# Development

This guide covers setting up a development environment and contributing to the Fluvie MCP Server.

## Getting Started

### Prerequisites

- Dart SDK >= 3.0.0
- Git
- Docker (optional)

### Clone and Setup

```bash
# Clone the repository
git clone https://github.com/SimonErich/fluvie_mcp_server.git
cd fluvie_mcp_server

# Install dependencies
dart pub get

# Run the server
dart run bin/server.dart
```

### Verify Installation

```bash
# Check health endpoint
curl http://localhost:8080/health

# List available tools
curl -X POST http://localhost:8080/mcp \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","method":"tools/list","id":1}'
```

---

## Project Structure

```
fluvie-mcp-server/
├── bin/
│   └── server.dart           # Main entry point
├── lib/
│   ├── mcp_server.dart       # Library exports
│   └── src/
│       ├── config/           # Server configuration
│       │   └── server_config.dart
│       ├── mcp/              # MCP protocol handling
│       │   ├── mcp_handler.dart
│       │   ├── mcp_types.dart
│       │   ├── tool_registry.dart
│       │   ├── resource_registry.dart
│       │   └── sse_handler.dart
│       ├── tools/            # MCP tool implementations
│       │   ├── search_docs_tool.dart
│       │   ├── get_template_tool.dart
│       │   ├── suggest_templates_tool.dart
│       │   ├── get_widget_ref_tool.dart
│       │   └── generate_code_tool.dart
│       ├── indexing/         # Documentation indexer
│       │   ├── doc_indexer.dart
│       │   ├── search_engine.dart
│       │   └── template_index.dart
│       └── middleware/       # HTTP middleware
│           ├── cors_middleware.dart
│           └── logging_middleware.dart
├── data/
│   └── docs/                 # Documentation files (mounted)
├── docs/                     # GitHub Pages documentation
├── test/                     # Tests
├── .env.example              # Environment template
├── Dockerfile
├── docker-compose.yml
├── pubspec.yaml
└── analysis_options.yaml
```

---

## Adding a New Tool

### 1. Create the Tool File

Create a new file in `lib/src/tools/`:

```dart
// lib/src/tools/my_new_tool.dart
import '../mcp/tool_registry.dart';

ToolDefinition createMyNewTool(DocIndexer indexer) {
  return ToolDefinition(
    name: 'myNewTool',
    description: 'Description of what the tool does',
    inputSchema: {
      'type': 'object',
      'properties': {
        'param1': {
          'type': 'string',
          'description': 'Description of param1',
        },
        'param2': {
          'type': 'number',
          'description': 'Optional parameter',
        },
      },
      'required': ['param1'],
    },
    handler: (Map<String, dynamic> args) async {
      final param1 = args['param1'] as String;
      final param2 = args['param2'] as int? ?? 10;

      // Tool implementation
      final result = await performToolLogic(param1, param2);

      return {
        'result': result,
        'success': true,
      };
    },
  );
}
```

### 2. Register the Tool

In `bin/server.dart`, import and register:

```dart
import 'package:mcp_server/src/tools/my_new_tool.dart';

void main() async {
  // ... existing setup ...

  // Register tools
  toolRegistry.register(createSearchDocsTool(indexer));
  toolRegistry.register(createMyNewTool(indexer)); // Add your tool

  // ... rest of server setup ...
}
```

### 3. Add Tests

Create tests in `test/tools/`:

```dart
// test/tools/my_new_tool_test.dart
import 'package:test/test.dart';
import 'package:mcp_server/src/tools/my_new_tool.dart';

void main() {
  group('myNewTool', () {
    late ToolDefinition tool;

    setUp(() {
      tool = createMyNewTool(mockIndexer);
    });

    test('returns expected result', () async {
      final result = await tool.handler({
        'param1': 'test value',
      });

      expect(result['success'], isTrue);
      expect(result['result'], isNotEmpty);
    });

    test('handles missing required param', () async {
      expect(
        () => tool.handler({}),
        throwsA(isA<ArgumentError>()),
      );
    });
  });
}
```

### 4. Document the Tool

Add documentation in `docs/tools/`:

```markdown
---
layout: default
title: myNewTool
---

# myNewTool

Description of the tool...
```

---

## Running Tests

```bash
# Run all tests
dart test

# Run specific test file
dart test test/tools/search_docs_test.dart

# Run with coverage
dart test --coverage=coverage
genhtml coverage/lcov.info -o coverage/html
```

---

## Code Style

### Formatting

```bash
# Format code
dart format .

# Check formatting (CI)
dart format --output=none --set-exit-if-changed .
```

### Analysis

```bash
# Run analyzer
dart analyze

# With fatal warnings (CI)
dart analyze --fatal-warnings
```

### Linting Rules

The project uses these lint rules (in `analysis_options.yaml`):

```yaml
include: package:lints/recommended.yaml

linter:
  rules:
    - prefer_single_quotes
    - require_trailing_commas
    - sort_pub_dependencies
```

---

## Debugging

### Verbose Logging

Set `LOG_LEVEL=debug` for detailed logs:

```bash
LOG_LEVEL=debug dart run bin/server.dart
```

### MCP Protocol Debugging

Log all MCP messages:

```dart
// In mcp_handler.dart
void handleMessage(Map<String, dynamic> message) {
  print('MCP Request: ${jsonEncode(message)}');
  // ... handle message ...
  print('MCP Response: ${jsonEncode(response)}');
}
```

### Testing with cURL

```bash
# Test tool call
curl -X POST http://localhost:8080/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "tools/call",
    "params": {
      "name": "searchDocs",
      "arguments": {"query": "test"}
    },
    "id": 1
  }' | jq .
```

---

## Contributing

See [CONTRIBUTING.md](https://github.com/SimonErich/fluvie_mcp_server/blob/main/CONTRIBUTING.md) for:

- Code of conduct
- Pull request process
- Commit message format
- Review guidelines

---

## Related Projects

- [fluvie](https://github.com/SimonErich/fluvie) - Main Flutter package
- [fluvie_website](https://github.com/SimonErich/fluvie_website) - Marketing website
