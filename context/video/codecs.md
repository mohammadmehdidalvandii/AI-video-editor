# Video Codecs Context

## Purpose

This document defines how video and audio codecs are understood,
selected, validated, and used inside the AI Video Editor.

Codec selection is part of the Video Engine and Rendering Pipeline.

The application must not expose raw codec configuration directly to
users or AI agents.

AI agents should select high-level encoding presets rather than directly
constructing codec-specific FFmpeg arguments.

---

# 1. What Is a Codec?

A codec is a technology used to encode and decode audio or video data.

The term codec comes from:

Encoder + Decoder

Conceptually:

```text
Raw Media
    ↓
Encoder
    ↓
Compressed Media
    ↓
Decoder
    ↓
Raw Media