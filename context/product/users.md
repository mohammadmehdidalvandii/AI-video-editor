# Users Context

## Purpose

This document defines the primary users of the AI Video Editor,
their goals, needs, workflows, and expected interactions with the
platform.

This context is used to guide product decisions, UX design,
architecture, and AI behavior.

---

# User Categories

The platform initially targets five main user categories:

1. Content Creators
2. YouTubers
3. Social Media Creators
4. Video Editors
5. Technical Users

---

# 1. Content Creators

## Description

Content creators produce video content regularly and need efficient
tools for editing, publishing, and repurposing their content.

## Goals

- Edit videos quickly
- Reduce repetitive editing tasks
- Create multiple versions of the same content
- Add subtitles
- Resize videos for different platforms
- Improve production efficiency

## Common Tasks

- Upload raw footage
- Cut unwanted sections
- Remove silence
- Add subtitles
- Add background music
- Create short-form versions
- Export videos for different platforms

## Pain Points

- Manual editing takes too much time
- Repetitive operations
- Difficult video editing software
- Managing large amounts of media
- Creating multiple versions of the same video

## AI Expectations

The user should be able to describe an editing task using natural
language.

Example:

> "Remove the silent parts and create a short version."

The AI should generate a structured editing plan that can be reviewed
before execution.

---

# 2. YouTubers

## Description

YouTubers create long-form and short-form video content for YouTube.

## Goals

- Produce videos faster
- Create Shorts from long videos
- Generate subtitles
- Detect important moments
- Improve editing workflow
- Export videos optimized for YouTube

## Common Tasks

- Upload long videos
- Detect important scenes
- Cut unnecessary sections
- Generate subtitles
- Create Shorts
- Add intro and outro
- Export final videos

## AI Expectations

The AI should assist with:

- Highlight detection
- Scene analysis
- Subtitle generation
- Short creation
- Content repurposing

Example:

> "Find the five most interesting moments and create Shorts from them."

---

# 3. Social Media Creators

## Description

Social media creators produce short-form content for platforms such
as TikTok, Instagram Reels, and YouTube Shorts.

## Goals

- Quickly create short videos
- Automatically resize content
- Add captions
- Detect important moments
- Create multiple aspect ratios
- Reduce manual editing

## Common Tasks

- Convert landscape video to vertical
- Generate subtitles
- Remove silence
- Add captions
- Crop video intelligently
- Export platform-specific versions

## AI Expectations

The AI should understand platform-specific requirements and generate
appropriate editing plans.

Example:

> "Convert this video into a vertical short with captions."

---

# 4. Video Editors

## Description

Professional or experienced editors require more control over the
editing process.

## Goals

- Maintain precise control
- Use AI as an assistant
- Automate repetitive operations
- Inspect generated editing plans
- Modify AI-generated edits manually

## Common Tasks

- Timeline editing
- Multi-track editing
- Audio editing
- Transitions
- Subtitles
- Effects
- Rendering
- Exporting

## AI Expectations

AI should assist rather than replace the editor.

The editor should be able to:

1. Request an AI operation
2. Review the generated plan
3. Modify the plan
4. Approve the operation
5. Execute the operation

---

# 5. Technical Users

## Description

Technical users include developers and advanced users who want
programmatic or highly configurable video processing workflows.

## Goals

- Automate video processing
- Inspect media metadata
- Use structured editing operations
- Integrate video processing into workflows
- Access predictable and validated operations

## AI Expectations

AI should generate structured operations rather than arbitrary
FFmpeg commands.

Example:

```json
{
  "operation": "trim",
  "start": 10,
  "end": 45
}