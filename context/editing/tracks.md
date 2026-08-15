# Tracks Context

## Purpose

This document defines the structure, behavior, responsibilities, and
rules of Tracks inside the AI Video Editor.

A Track is a logical container for timeline elements such as video,
audio, subtitles, overlays, and other media objects.

Tracks organize media vertically while the Timeline organizes media
temporally.

---

# 1. What Is a Track?

A Track is a layer inside the Timeline.

Conceptually:

```text
Timeline
│
├── Track 4 ── Text / Captions
├── Track 3 ── Overlay Video
├── Track 2 ── Main Video
└── Track 1 ── Background