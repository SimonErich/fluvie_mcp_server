---
layout: default
title: generateCode Tool
---

# generateCode

Generate Fluvie Dart code from natural language descriptions.

## Overview

The `generateCode` tool uses AI to convert natural language descriptions into working Fluvie code. It can generate:

- Complete video compositions
- Individual scenes and sequences
- Template instantiations
- Animation snippets
- Layout structures

## Input Schema

```json
{
  "type": "object",
  "properties": {
    "description": {
      "type": "string",
      "description": "Natural language description of what to create"
    },
    "type": {
      "type": "string",
      "description": "Type of code to generate",
      "enum": ["full_video", "scene", "template_usage", "animation", "layout"],
      "default": "scene"
    },
    "fps": {
      "type": "number",
      "description": "Frames per second",
      "default": 30
    },
    "aspectRatio": {
      "type": "string",
      "description": "Video aspect ratio",
      "enum": ["9:16", "16:9", "1:1", "4:3"],
      "default": "16:9"
    }
  },
  "required": ["description"]
}
```

## Code Types

### full_video
Generates a complete `VideoComposition` with all necessary imports and structure.

### scene
Generates a single scene or sequence that can be inserted into an existing composition.

### template_usage
Generates code that uses one of Fluvie's built-in templates.

### animation
Generates animation-specific code using `TimeConsumer`, `AnimatedProp`, etc.

### layout
Generates layout structure using `LayerStack`, `Layer`, positioning widgets.

## Aspect Ratios

| Ratio | Dimensions | Use Case |
|-------|------------|----------|
| `9:16` | 1080x1920 | TikTok, Instagram Reels, YouTube Shorts |
| `16:9` | 1920x1080 | YouTube, presentations |
| `1:1` | 1080x1080 | Instagram posts |
| `4:3` | 1440x1080 | Classic video format |

## Example Requests

### Full Video

```json
{
  "description": "Create a 10-second intro video with a gradient background, animated title 'Welcome' that fades in, and a subtle particle effect overlay",
  "type": "full_video",
  "fps": 30,
  "aspectRatio": "16:9"
}
```

### Scene

```json
{
  "description": "A countdown from 5 to 1 with each number scaling up and fading out",
  "type": "scene",
  "fps": 30
}
```

### Template Usage

```json
{
  "description": "Use the neon gate template with title 'My 2024' and cyan/purple colors",
  "type": "template_usage"
}
```

### Animation

```json
{
  "description": "Text that types out character by character over 60 frames",
  "type": "animation"
}
```

## Example Response

```json
{
  "code": "import 'package:flutter/material.dart';\nimport 'package:fluvie/fluvie.dart';\n\nfinal composition = VideoComposition(\n  fps: 30,\n  durationInFrames: 300, // 10 seconds\n  width: 1920,\n  height: 1080,\n  child: LayerStack(\n    children: [\n      // Gradient background\n      Layer.background(\n        child: Container(\n          decoration: BoxDecoration(\n            gradient: LinearGradient(\n              begin: Alignment.topLeft,\n              end: Alignment.bottomRight,\n              colors: [Colors.blue.shade900, Colors.purple.shade900],\n            ),\n          ),\n        ),\n      ),\n      // Animated title\n      Layer(\n        startFrame: 30,\n        endFrame: 270,\n        fadeInFrames: 30,\n        fadeOutFrames: 30,\n        child: Center(\n          child: AnimatedText(\n            text: 'Welcome',\n            style: TextStyle(\n              fontSize: 96,\n              fontWeight: FontWeight.bold,\n              color: Colors.white,\n            ),\n          ),\n        ),\n      ),\n      // Particle overlay\n      Layer.overlay(\n        blendMode: BlendMode.screen,\n        child: ParticleEffect(\n          particleCount: 50,\n          color: Colors.white.withOpacity(0.3),\n        ),\n      ),\n    ],\n  ),\n);",
  "explanation": "This composition creates a 10-second intro video at 30 FPS (300 frames total) with:\n\n1. **Gradient Background**: A blue-to-purple diagonal gradient that's always visible\n2. **Animated Title**: 'Welcome' text that fades in at 1 second, stays visible until the last second, then fades out\n3. **Particle Overlay**: Subtle white particles using screen blend mode for a magical effect\n\nThe timing breakdown:\n- Frames 0-30: Background only, title fading in\n- Frames 30-270: Full title visibility\n- Frames 270-300: Title fading out",
  "dependencies": [
    "fluvie: ^0.1.0"
  ],
  "warnings": [],
  "suggestions": [
    "Consider adding background audio with AudioTrack",
    "You can customize the particle effect with different colors and speeds"
  ]
}
```

## Tips for Better Results

1. **Be specific about timing**: "fade in over 1 second" is clearer than "fade in"
2. **Mention colors**: "gradient from blue to purple" gives better results
3. **Describe animations**: "scale up while rotating" is more precise than "animate"
4. **Specify text content**: Include the actual text you want displayed
5. **Reference templates**: If you want to use a template, mention it by name

## Limitations

- Generated code may need minor adjustments for your specific use case
- Complex animations might need manual refinement
- Custom widgets are not generated (only Fluvie built-ins)
