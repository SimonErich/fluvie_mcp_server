---
layout: default
title: MCP Resources
---

# MCP Resources

The Fluvie MCP Server exposes two resources that AI clients can read for comprehensive reference information.

## Overview

MCP resources provide static content that AI assistants can load into their context. Unlike tools (which execute actions), resources are read-only data sources.

| Resource URI | Description |
|--------------|-------------|
| `fluvie://docs/ai-reference` | Complete AI-optimized documentation |
| `fluvie://templates` | Template catalog in JSON format |

## fluvie://docs/ai-reference

A comprehensive, AI-optimized reference document covering all aspects of Fluvie.

### Content Structure

The AI reference document includes:

1. **Quick Reference**
   - Package overview
   - Key concepts summary
   - Common patterns

2. **Widget Reference**
   - All widgets with properties
   - Usage examples
   - Best practices

3. **Animation Guide**
   - Frame-based animation concepts
   - Easing functions
   - Timing patterns

4. **Template Catalog**
   - All available templates
   - Parameters and customization
   - Use case recommendations

5. **FFmpeg Integration**
   - Platform setup
   - Configuration options
   - Troubleshooting

### Usage

AI clients can read this resource to get full context about Fluvie:

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

### Response

```json
{
  "jsonrpc": "2.0",
  "result": {
    "contents": [
      {
        "uri": "fluvie://docs/ai-reference",
        "mimeType": "text/markdown",
        "text": "# Fluvie AI Reference\n\n## Overview\n\nFluvie is a Flutter package for programmatic video generation..."
      }
    ]
  },
  "id": 1
}
```

## fluvie://templates

A JSON catalog of all available Fluvie templates with metadata.

### Content Structure

```json
{
  "version": "1.0.0",
  "categories": {
    "intro": {
      "description": "Opening and title screen templates",
      "templates": [
        {
          "name": "TheNeonGate",
          "description": "Dramatic neon portal entrance",
          "parameters": {...},
          "tags": ["dramatic", "neon", "portal"]
        }
      ]
    },
    "ranking": {...},
    "data_viz": {...},
    "collage": {...},
    "thematic": {...},
    "conclusion": {...}
  }
}
```

### Usage

```json
{
  "jsonrpc": "2.0",
  "method": "resources/read",
  "params": {
    "uri": "fluvie://templates"
  },
  "id": 1
}
```

### Response

```json
{
  "jsonrpc": "2.0",
  "result": {
    "contents": [
      {
        "uri": "fluvie://templates",
        "mimeType": "application/json",
        "text": "{\"version\":\"1.0.0\",\"categories\":{...}}"
      }
    ]
  },
  "id": 1
}
```

## Listing Resources

To get a list of available resources:

```json
{
  "jsonrpc": "2.0",
  "method": "resources/list",
  "id": 1
}
```

### Response

```json
{
  "jsonrpc": "2.0",
  "result": {
    "resources": [
      {
        "uri": "fluvie://docs/ai-reference",
        "name": "Fluvie AI Reference",
        "description": "Complete AI-optimized documentation for Fluvie",
        "mimeType": "text/markdown"
      },
      {
        "uri": "fluvie://templates",
        "name": "Template Catalog",
        "description": "JSON catalog of all Fluvie templates",
        "mimeType": "application/json"
      }
    ]
  },
  "id": 1
}
```

## When to Use Resources vs Tools

| Use Case | Resource or Tool |
|----------|------------------|
| Get full context about Fluvie | Resource: `ai-reference` |
| Search for specific information | Tool: `searchDocs` |
| Browse all templates | Resource: `templates` |
| Get specific template details | Tool: `getTemplate` |
| Generate code | Tool: `generateCode` |
