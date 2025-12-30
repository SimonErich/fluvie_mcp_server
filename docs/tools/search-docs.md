---
layout: default
title: searchDocs Tool
---

# searchDocs

Search the Fluvie documentation using TF-IDF (Term Frequency-Inverse Document Frequency) ranking.

## Overview

The `searchDocs` tool provides intelligent documentation search with:

- **TF-IDF ranking** for relevance scoring
- **Category filtering** to narrow results
- **Snippet extraction** with context
- **Configurable result limits**

## Input Schema

```json
{
  "type": "object",
  "properties": {
    "query": {
      "type": "string",
      "description": "Search query text"
    },
    "category": {
      "type": "string",
      "description": "Filter by documentation category",
      "enum": ["widgets", "animations", "effects", "templates", "advanced", "getting-started"]
    },
    "limit": {
      "type": "number",
      "description": "Maximum number of results (1-10)",
      "minimum": 1,
      "maximum": 10,
      "default": 5
    }
  },
  "required": ["query"]
}
```

## Examples

### Basic Search

```json
{
  "query": "how to fade text"
}
```

### Category-Filtered Search

```json
{
  "query": "layer animations",
  "category": "widgets"
}
```

### Limited Results

```json
{
  "query": "ffmpeg configuration",
  "limit": 3
}
```

## Response Format

```json
{
  "results": [
    {
      "title": "Document Title",
      "path": "relative/path/to/doc.md",
      "category": "widgets",
      "snippet": "...relevant text excerpt with search terms...",
      "score": 0.85
    }
  ],
  "total": 1,
  "query": "original query"
}
```

## How TF-IDF Works

The search engine uses TF-IDF to rank documents:

1. **Term Frequency (TF)**: How often does the search term appear in this document?
2. **Inverse Document Frequency (IDF)**: How unique is this term across all documents?
3. **Score**: TF × IDF gives higher scores to documents where search terms are both frequent AND distinctive

This means:
- Common words like "the" have low IDF (appear everywhere)
- Specific terms like "VideoComposition" have high IDF (appear in few documents)
- A document mentioning "VideoComposition" many times scores highest

## Tips for Effective Searches

1. **Use specific terms**: "LayerStack fadeIn" is better than "how to fade"
2. **Include widget names**: "AnimatedText typewriter" finds relevant docs faster
3. **Use category filter**: Narrow results when you know the topic area
4. **Combine concepts**: "audio sync beat detection" finds intersection of topics
