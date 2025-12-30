---
layout: default
title: getWidgetReference Tool
---

# getWidgetReference

Get comprehensive documentation for a Fluvie widget including properties, methods, and usage examples.

## Overview

The `getWidgetReference` tool provides detailed information about Fluvie widgets:

- Complete property documentation
- Type information and defaults
- Usage examples
- Related widgets

## Input Schema

```json
{
  "type": "object",
  "properties": {
    "widgetName": {
      "type": "string",
      "description": "Name of the widget (PascalCase)"
    }
  },
  "required": ["widgetName"]
}
```

## Available Widgets

### Core Widgets
- `VideoComposition` - Root composition widget
- `Sequence` - Time-bounded content
- `Scene` - Named scene container
- `TimeConsumer` - Frame/progress access

### Layout Widgets
- `LayerStack` - Z-indexed layer container
- `Layer` - Time-based visibility layer
- `VStack` / `VRow` / `VColumn` - Layout containers
- `VCenter` / `VPadding` / `VPositioned` - Positioning

### Text Widgets
- `AnimatedText` - Frame-based text animation
- `TypewriterText` - Character-by-character reveal
- `FadeText` - Fading text effect
- `CounterText` - Animated number counter

### Media Widgets
- `EmbeddedVideo` - Video embedding
- `VideoSequence` - Video clip sequencing
- `Collage` - Photo collage

### Audio Widgets
- `AudioTrack` - Audio with timing
- `AudioSource` - Audio file reference
- `BackgroundAudio` - Full-composition audio

### Animation Widgets
- `AnimatedProp` - Property animation
- `Interpolate` - Value interpolation
- `Stagger` - Staggered animations

## Example Request

```json
{
  "widgetName": "Layer"
}
```

## Example Response

```json
{
  "name": "Layer",
  "description": "A layer with time-based visibility for video compositions. Layers automatically show and hide based on the current frame, with optional fade transitions.",
  "category": "layout",
  "properties": [
    {
      "name": "id",
      "type": "String?",
      "required": false,
      "description": "Optional identifier for the layer"
    },
    {
      "name": "startFrame",
      "type": "int",
      "required": false,
      "default": 0,
      "description": "Frame when the layer becomes visible"
    },
    {
      "name": "endFrame",
      "type": "int?",
      "required": false,
      "default": null,
      "description": "Frame when the layer becomes hidden (null = visible until end)"
    },
    {
      "name": "fadeInFrames",
      "type": "int",
      "required": false,
      "default": 0,
      "description": "Number of frames for fade-in transition"
    },
    {
      "name": "fadeOutFrames",
      "type": "int",
      "required": false,
      "default": 0,
      "description": "Number of frames for fade-out transition"
    },
    {
      "name": "blendMode",
      "type": "BlendMode",
      "required": false,
      "default": "BlendMode.srcOver",
      "description": "How this layer blends with layers below"
    },
    {
      "name": "child",
      "type": "Widget",
      "required": true,
      "description": "The content of this layer"
    }
  ],
  "constructors": {
    "default": "Layer({id, startFrame, endFrame, fadeInFrames, fadeOutFrames, blendMode, required child})",
    "background": "Layer.background({fadeInFrames, required child}) - Always visible background",
    "overlay": "Layer.overlay({blendMode, required child}) - Always visible overlay"
  },
  "example": "LayerStack(\n  children: [\n    Layer.background(\n      child: GradientBackground(),\n    ),\n    Layer(\n      startFrame: 30,\n      endFrame: 120,\n      fadeInFrames: 15,\n      fadeOutFrames: 15,\n      child: TitleText('Hello World'),\n    ),\n    Layer.overlay(\n      blendMode: BlendMode.screen,\n      child: Watermark(),\n    ),\n  ],\n)",
  "seeAlso": ["LayerStack", "Sequence", "TimeConsumer"],
  "tips": [
    "Use Layer.background() for content that should always be visible",
    "Fade transitions create smooth visual flow",
    "Combine with BlendMode for creative compositing effects"
  ]
}
```

## Error Handling

If the widget is not found:

```json
{
  "error": "Widget not found",
  "widgetName": "NonExistent",
  "suggestions": ["Layer", "LayerStack", "VideoComposition"],
  "hint": "Did you mean one of these? Widget names are case-sensitive."
}
```
