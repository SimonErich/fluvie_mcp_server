---
layout: default
title: MCP Tools
---

# MCP Tools

The Fluvie MCP Server provides 5 tools for AI-assisted development. Each tool has a specific purpose and accepts structured JSON input.

## Tool Overview

| Tool | Purpose | Primary Use Case |
|------|---------|------------------|
| [searchDocs](#searchdocs) | Search documentation | Finding relevant docs for a topic |
| [getTemplate](#gettemplate) | Get template details | Understanding a specific template |
| [suggestTemplates](#suggesttemplates) | Recommend templates | Finding templates for a use case |
| [getWidgetReference](#getwidgetreference) | Widget documentation | Learning widget APIs |
| [generateCode](#generatecode) | Generate Fluvie code | Creating compositions from descriptions |

---

## searchDocs

Search Fluvie documentation using TF-IDF ranking algorithm.

### Input Schema

```json
{
  "query": "string (required) - Search query",
  "category": "string (optional) - Filter by category",
  "limit": "number (optional, 1-10, default: 5) - Max results"
}
```

### Example

```json
{
  "query": "animate text opacity",
  "category": "widgets",
  "limit": 5
}
```

### Response

```json
{
  "results": [
    {
      "title": "AnimatedText Widget",
      "path": "widgets/text/animated-text.md",
      "category": "widgets",
      "snippet": "The AnimatedText widget provides frame-based text animations...",
      "score": 0.85
    }
  ],
  "total": 1
}
```

[Full documentation](search-docs)

---

## getTemplate

Get detailed information about a specific Fluvie template.

### Input Schema

```json
{
  "category": "string (required) - Template category",
  "name": "string (required) - Template name"
}
```

### Categories

- `intro` - Opening/title templates
- `ranking` - Countdown/ranking templates
- `data_viz` - Data visualization templates
- `collage` - Photo collage templates
- `thematic` - Themed templates
- `conclusion` - Ending/summary templates

### Example

```json
{
  "category": "intro",
  "name": "TheNeonGate"
}
```

### Response

```json
{
  "name": "TheNeonGate",
  "category": "intro",
  "description": "Dramatic neon portal entrance effect",
  "parameters": {
    "title": "Main title text",
    "subtitle": "Optional subtitle",
    "accentColor": "Neon glow color"
  },
  "example": "...",
  "preview": "templates/neon-gate.jpg"
}
```

[Full documentation](get-template)

---

## suggestTemplates

Get template recommendations based on use case, content type, and mood.

### Input Schema

```json
{
  "useCase": "string (required) - What you're creating",
  "contentType": "string (optional) - Type of content",
  "mood": "string (optional) - Desired mood/tone"
}
```

### Content Types

- `year_review` - Year in review videos
- `statistics` - Data/stats presentation
- `photo_gallery` - Photo compilations
- `intro` - Introductions
- `outro` - Conclusions

### Moods

- `energetic` - High energy, dynamic
- `nostalgic` - Reflective, sentimental
- `professional` - Clean, business-like
- `playful` - Fun, lighthearted
- `dramatic` - Intense, impactful
- `minimal` - Simple, understated

### Example

```json
{
  "useCase": "year-end summary video",
  "contentType": "statistics",
  "mood": "dramatic"
}
```

### Response

```json
{
  "suggestions": [
    {
      "name": "StatShowcase",
      "category": "data_viz",
      "matchScore": 0.92,
      "reason": "Perfect for dramatic statistics presentation"
    }
  ]
}
```

[Full documentation](suggest-templates)

---

## getWidgetReference

Get comprehensive documentation for a Fluvie widget.

### Input Schema

```json
{
  "widgetName": "string (required) - Name of the widget"
}
```

### Example

```json
{
  "widgetName": "AnimatedProp"
}
```

### Response

```json
{
  "name": "AnimatedProp",
  "description": "Animates a single property over time",
  "properties": [
    {
      "name": "from",
      "type": "double",
      "required": true,
      "description": "Starting value"
    },
    {
      "name": "to",
      "type": "double",
      "required": true,
      "description": "Ending value"
    }
  ],
  "example": "...",
  "seeAlso": ["TimeConsumer", "Interpolate"]
}
```

[Full documentation](get-widget-ref)

---

## generateCode

Generate Fluvie Dart code from natural language descriptions.

### Input Schema

```json
{
  "description": "string (required) - What to create",
  "type": "string (optional) - Code type",
  "fps": "number (optional, default: 30) - Frames per second",
  "aspectRatio": "string (optional) - Video aspect ratio"
}
```

### Code Types

- `full_video` - Complete VideoComposition
- `scene` - Single scene/sequence
- `template_usage` - Template instantiation
- `animation` - Animation snippet
- `layout` - Layout structure

### Aspect Ratios

- `9:16` - Vertical (TikTok, Reels)
- `16:9` - Horizontal (YouTube)
- `1:1` - Square (Instagram)
- `4:3` - Classic

### Example

```json
{
  "description": "Create an intro with a neon portal effect and animated title",
  "type": "scene",
  "fps": 30,
  "aspectRatio": "16:9"
}
```

### Response

```json
{
  "code": "...",
  "explanation": "This creates a neon portal intro using...",
  "dependencies": ["fluvie"],
  "warnings": []
}
```

[Full documentation](generate-code)
