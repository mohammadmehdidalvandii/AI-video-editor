# Timeline Context

## Purpose

This document defines the conceptual model, behavior, data structure,
and architectural rules of the video editing timeline.

The Timeline is the central editing model of the AI Video Editor.

It represents:

- Time
- Tracks
- Clips
- Transitions
- Effects
- Audio
- Video
- Editing operations

The Timeline must remain independent from FFmpeg implementation details.

FFmpeg is only responsible for executing the final processing plan
generated from the Timeline.

---

# 1. What Is a Timeline?

A Timeline represents the temporal structure of a video project.

Conceptually:

```text
Time →

0s        10s        20s        30s        40s
│─────────│──────────│──────────│──────────│

Track 1
[──── Clip A ─────][── Clip B ──]

Track 2
       [──── Overlay ─────]

Track 3
[──────────── Music ───────────────]