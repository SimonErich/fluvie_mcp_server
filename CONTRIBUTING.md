# Contributing to Fluvie MCP Server

Thank you for your interest in contributing to the Fluvie MCP Server! This document provides guidelines and instructions for contributing.

## Code of Conduct

By participating in this project, you agree to maintain a respectful and inclusive environment for everyone.

## Getting Started

### Prerequisites

- Dart SDK >= 3.0.0
- Docker (optional, for containerized development)
- Git

### Setup

1. Fork the repository on GitHub

2. Clone your fork:
   ```bash
   git clone https://github.com/YOUR_USERNAME/fluvie_mcp_server.git
   cd fluvie_mcp_server
   ```

3. Add the upstream remote:
   ```bash
   git remote add upstream https://github.com/SimonErich/fluvie_mcp_server.git
   ```

4. Install dependencies:
   ```bash
   dart pub get
   ```

5. Run the server locally:
   ```bash
   dart run bin/server.dart
   ```

6. Verify it's working:
   ```bash
   curl http://localhost:8080/health
   ```

## Development Workflow

### Creating a Branch

Create a branch for your work:

```bash
git checkout -b feature/your-feature-name
# or
git checkout -b fix/issue-description
```

### Making Changes

1. Write your code following the project style guidelines
2. Add tests for new functionality
3. Update documentation as needed
4. Run the analyzer and fix any issues:
   ```bash
   dart analyze --fatal-warnings
   ```

5. Format your code:
   ```bash
   dart format .
   ```

### Running Tests

```bash
# Run all tests
dart test

# Run with coverage (requires coverage package)
dart test --coverage=coverage
```

### Committing

Write clear, concise commit messages:

```
feat: add new MCP tool for template validation

- Add validateTemplate tool
- Add input schema validation
- Add error responses for invalid templates
```

Use conventional commit prefixes:
- `feat:` - New feature
- `fix:` - Bug fix
- `docs:` - Documentation changes
- `test:` - Test changes
- `refactor:` - Code refactoring
- `chore:` - Maintenance tasks

### Submitting a Pull Request

1. Push your branch to your fork:
   ```bash
   git push origin feature/your-feature-name
   ```

2. Open a Pull Request on GitHub

3. Fill out the PR template completely

4. Wait for CI to pass and address any review feedback

## Adding a New MCP Tool

To add a new tool to the MCP server:

1. Create a new file in `lib/src/tools/`:
   ```dart
   // lib/src/tools/my_new_tool.dart
   import '../mcp/tool_registry.dart';

   ToolDefinition createMyNewTool() {
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
         },
         'required': ['param1'],
       },
       handler: (Map<String, dynamic> args) async {
         final param1 = args['param1'] as String;

         // Tool implementation

         return {
           'result': 'Tool output',
         };
       },
     );
   }
   ```

2. Register the tool in `bin/server.dart`:
   ```dart
   import 'package:mcp_server/src/tools/my_new_tool.dart';

   // In the setup section:
   toolRegistry.register(createMyNewTool());
   ```

3. Add documentation for the tool in `docs/tools/`

4. Add tests for the tool

## Code Style

### General Guidelines

- Follow the [Effective Dart](https://dart.dev/guides/language/effective-dart) style guide
- Use meaningful variable and function names
- Keep functions focused and small
- Write dartdoc comments for public APIs

### Naming Conventions

- Classes: `PascalCase`
- Functions/variables: `camelCase`
- Constants: `camelCase` or `SCREAMING_SNAKE_CASE`
- Files: `snake_case.dart`

## Project Structure

```
fluvie-mcp-server/
├── bin/
│   └── server.dart           # Main entry point
├── lib/
│   ├── mcp_server.dart       # Library exports
│   └── src/
│       ├── config/           # Server configuration
│       ├── mcp/              # MCP protocol handling
│       ├── tools/            # MCP tool implementations
│       ├── indexing/         # Documentation indexer
│       └── middleware/       # HTTP middleware
├── docs/                     # Documentation site
├── test/                     # Tests
├── Dockerfile
└── docker-compose.yml
```

## Docker Development

Build and run with Docker:

```bash
# Build the image
docker build -t fluvie-mcp:dev .

# Run the container
docker run -p 8080:8080 fluvie-mcp:dev

# Or use docker-compose
docker-compose up
```

## Reporting Issues

When reporting issues, please include:

1. A clear description of the problem
2. Steps to reproduce
3. Expected vs actual behavior
4. Dart version (`dart --version`)
5. Server version
6. Relevant logs or error messages

## Feature Requests

Feature requests are welcome! Please:

1. Check existing issues to avoid duplicates
2. Describe the use case clearly
3. Explain why existing functionality doesn't meet your needs
4. If possible, suggest an implementation approach

## Related Projects

- [fluvie](https://github.com/SimonErich/fluvie) - Main Flutter package
- [fluvie_website](https://github.com/SimonErich/fluvie_website) - Marketing website

## Questions?

If you have questions about contributing, feel free to:

- Open a GitHub Discussion
- Create an issue with the "question" label

Thank you for contributing to Fluvie MCP Server!
