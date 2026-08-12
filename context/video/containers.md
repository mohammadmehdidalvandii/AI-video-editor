# Video Containers Context

## Purpose

This document defines how media containers are understood, selected,
validated, and used inside the AI Video Editor.

A container defines how media streams, metadata, timestamps, subtitles,
and related information are packaged into a single media file.

Container selection is part of the Video Engine and Rendering Pipeline.

The application must not treat file extensions as sufficient evidence
of the actual container or codec configuration.

---

# 1. What Is a Container?

A media container is a file format that packages one or more media
streams and their associated metadata.

A container may contain:

- Video streams
- Audio streams
- Subtitle streams
- Metadata
- Chapters
- Attachments
- Timing information

Conceptually:

```text
Container
│
├── Video Stream
├── Audio Stream
├── Subtitle Stream
├── Metadata
└── Timing Information