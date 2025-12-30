---
layout: default
title: getTemplate Tool
---

# getTemplate

Get detailed information about a specific Fluvie template.

## Overview

The `getTemplate` tool retrieves comprehensive information about Fluvie's built-in templates, including:

- Template description and purpose
- Required and optional parameters
- Example usage code
- Preview image reference

## Input Schema

```json
{
  "type": "object",
  "properties": {
    "category": {
      "type": "string",
      "description": "Template category",
      "enum": ["intro", "ranking", "data_viz", "collage", "thematic", "conclusion"]
    },
    "name": {
      "type": "string",
      "description": "Template name (PascalCase)"
    }
  },
  "required": ["category", "name"]
}
```

## Template Categories

### intro
Opening and title screen templates:
- `TheNeonGate` - Dramatic neon portal entrance
- `LiquidMinutes` - Fluid morphing text intro
- `GridShuffle` - Grid-based reveal animation

### ranking
Countdown and ranking list templates:
- `TopTenCountdown` - Animated countdown list
- `RankingCards` - Card-based ranking display

### data_viz
Data visualization templates:
- `StatShowcase` - Animated statistics display
- `ChartReveal` - Progressive chart animation
- `NumberCounter` - Large number counter

### collage
Photo and media collage templates:
- `PhotoGrid` - Grid-based photo display
- `PolaroidStack` - Stacked polaroid effect
- `MasonryFlow` - Masonry layout animation

### thematic
Themed and seasonal templates:
- `YearInReview` - Annual summary template
- `SeasonalWrap` - Seasonal highlights
- `MilestoneMarker` - Achievement celebrations

### conclusion
Ending and summary templates:
- `SummaryPoster` - Final summary screen
- `CreditsRoll` - Scrolling credits
- `CallToAction` - CTA end screen

## Example Request

```json
{
  "category": "intro",
  "name": "TheNeonGate"
}
```

## Example Response

```json
{
  "name": "TheNeonGate",
  "category": "intro",
  "description": "A dramatic neon portal entrance effect with customizable glow colors and text animations. Perfect for high-energy intros.",
  "parameters": {
    "title": {
      "type": "String",
      "required": true,
      "description": "Main title text displayed in the center"
    },
    "subtitle": {
      "type": "String",
      "required": false,
      "description": "Optional subtitle below the main title"
    },
    "accentColor": {
      "type": "Color",
      "required": false,
      "default": "Colors.cyan",
      "description": "Primary neon glow color"
    },
    "secondaryColor": {
      "type": "Color",
      "required": false,
      "default": "Colors.purple",
      "description": "Secondary glow color for gradient effects"
    },
    "durationInFrames": {
      "type": "int",
      "required": false,
      "default": 90,
      "description": "Total animation duration in frames"
    }
  },
  "example": "TheNeonGate(\n  title: '2024 Wrapped',\n  subtitle: 'Your Year in Review',\n  accentColor: Colors.cyan,\n)",
  "preview": "templates/neon-gate.jpg",
  "tags": ["dramatic", "neon", "portal", "intro", "title"]
}
```

## Error Handling

If the template is not found:

```json
{
  "error": "Template not found",
  "category": "intro",
  "name": "NonExistent",
  "availableTemplates": ["TheNeonGate", "LiquidMinutes", "GridShuffle"]
}
```
