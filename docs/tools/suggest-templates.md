---
layout: default
title: suggestTemplates Tool
---

# suggestTemplates

Get template recommendations based on your use case, content type, and desired mood.

## Overview

The `suggestTemplates` tool analyzes your requirements and recommends the most suitable Fluvie templates. It considers:

- **Use case** - What you're trying to create
- **Content type** - The type of content you're working with
- **Mood** - The emotional tone you want to convey

## Input Schema

```json
{
  "type": "object",
  "properties": {
    "useCase": {
      "type": "string",
      "description": "Description of what you're creating"
    },
    "contentType": {
      "type": "string",
      "description": "Type of content",
      "enum": ["year_review", "statistics", "photo_gallery", "intro", "outro", "tutorial", "promo"]
    },
    "mood": {
      "type": "string",
      "description": "Desired mood/tone",
      "enum": ["energetic", "nostalgic", "professional", "playful", "dramatic", "minimal"]
    }
  },
  "required": ["useCase"]
}
```

## Content Types

| Type | Description | Best For |
|------|-------------|----------|
| `year_review` | Annual summary content | Spotify Wrapped-style videos |
| `statistics` | Data and numbers | Infographics, reports |
| `photo_gallery` | Photo collections | Memories, portfolios |
| `intro` | Opening sequences | Video intros, titles |
| `outro` | Closing sequences | Credits, CTAs |
| `tutorial` | Educational content | How-to videos |
| `promo` | Promotional content | Ads, announcements |

## Moods

| Mood | Characteristics | Template Style |
|------|-----------------|----------------|
| `energetic` | Fast, dynamic, high-impact | Bold colors, quick transitions |
| `nostalgic` | Warm, reflective, sentimental | Soft tones, gentle animations |
| `professional` | Clean, corporate, polished | Minimal design, smooth motion |
| `playful` | Fun, whimsical, creative | Bright colors, bouncy effects |
| `dramatic` | Intense, impactful, bold | High contrast, cinematic |
| `minimal` | Simple, understated, elegant | Subtle animations, whitespace |

## Example Requests

### Year in Review Video

```json
{
  "useCase": "Create a Spotify Wrapped-style year in review video",
  "contentType": "year_review",
  "mood": "energetic"
}
```

### Professional Statistics

```json
{
  "useCase": "Present quarterly sales data to stakeholders",
  "contentType": "statistics",
  "mood": "professional"
}
```

### Photo Memories

```json
{
  "useCase": "Birthday photo compilation for a friend",
  "contentType": "photo_gallery",
  "mood": "nostalgic"
}
```

## Example Response

```json
{
  "suggestions": [
    {
      "name": "YearInReview",
      "category": "thematic",
      "matchScore": 0.95,
      "reason": "Designed specifically for annual summary videos with energetic transitions",
      "strengths": [
        "Built-in stat counters",
        "Photo grid support",
        "Music sync ready"
      ]
    },
    {
      "name": "StatShowcase",
      "category": "data_viz",
      "matchScore": 0.82,
      "reason": "Great for highlighting key statistics with dynamic number animations",
      "strengths": [
        "Animated counters",
        "Comparison charts",
        "Bold typography"
      ]
    },
    {
      "name": "GridShuffle",
      "category": "intro",
      "matchScore": 0.75,
      "reason": "Energetic grid-based intro that works well as an opener",
      "strengths": [
        "Eye-catching entrance",
        "Customizable grid",
        "Fast-paced"
      ]
    }
  ],
  "totalMatches": 3,
  "searchCriteria": {
    "useCase": "Spotify Wrapped-style year in review",
    "contentType": "year_review",
    "mood": "energetic"
  }
}
```

## Tips for Better Suggestions

1. **Be specific about your use case**: "Birthday video for a 5-year-old" gives better results than "birthday video"
2. **Combine content type and mood**: The combination helps narrow down options
3. **Describe the outcome**: "Something that will impress clients" helps match professional templates
