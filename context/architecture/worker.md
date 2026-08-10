# Worker Architecture

## Purpose

This document defines the architecture of background workers used for
video processing, media analysis, rendering, subtitle generation,
thumbnail generation, and exporting.

Workers are responsible for CPU-intensive and long-running operations
that must not execute inside the HTTP API process.

---

# 1. Worker Responsibilities

Workers are responsible for:

- Media analysis
- FFprobe execution
- FFmpeg execution
- Video rendering
- Video transcoding
- Thumbnail generation
- Audio processing
- Subtitle generation
- Video export
- Future AI media-processing tasks

Workers must not handle normal HTTP requests.

---

# 2. Worker Architecture

The system follows a queue-based worker architecture.

```text
Frontend
   ↓
Express API
   ↓
Job Service
   ↓
BullMQ
   ↓
Redis
   ↓
Worker
   ↓
Video Engine
   ├── FFprobe
   └── FFmpeg
   ↓
Object Storage
   ↓
Database