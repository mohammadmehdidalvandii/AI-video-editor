# FFprobe Context

## Purpose

This document defines how FFprobe is used inside the AI Video Editor.

FFprobe is responsible for inspecting multimedia files and extracting
technical metadata before media processing begins.

FFprobe must be accessed through the Video Engine and must not be
directly controlled by frontend code, AI agents, or business logic.

---

# 1. What Is FFprobe?

FFprobe is a command-line utility distributed with FFmpeg.

It is used to inspect multimedia files and retrieve information about:

* Containers
* Streams
* Video codecs
* Audio codecs
* Duration
* Resolution
* Frame rate
* Bitrate
* Pixel format
* Sample rate
* Channels
* Metadata
* Chapters
* Stream relationships

In this project, FFprobe is the primary media inspection tool.

---

# 2. Position in the Architecture

FFprobe runs inside the Worker environment.

```text
Upload
  ↓
Media Record
  ↓
Analysis Job
  ↓
BullMQ
  ↓
Worker
  ↓
Video Engine
  ↓
FFprobe
  ↓
Media Metadata
  ↓
PostgreSQL
```

FFprobe should not be executed directly by the Express API for
long-running media analysis.

---

# 3. FFprobe Boundary

The Video Engine provides an abstraction around FFprobe.

```text
Worker
  ↓
Video Engine
  ↓
FFprobe Adapter
  ↓
FFprobe Process
```

The rest of the application should not depend on raw FFprobe command
syntax.

---

# 4. Primary Responsibilities

FFprobe is responsible for:

* Detecting media format
* Detecting streams
* Detecting codecs
* Detecting duration
* Detecting resolution
* Detecting frame rate
* Detecting audio properties
* Detecting pixel format
* Detecting bitrate
* Extracting technical metadata

FFprobe is not responsible for:

* Editing media
* Encoding media
* Rendering video
* Uploading files
* Persisting application state

---

# 5. Media Analysis Lifecycle

When a user uploads media:

```text
Upload
  ↓
Store Original
  ↓
Create Media Record
  ↓
Create Analysis Job
  ↓
Worker
  ↓
FFprobe
  ↓
Validate Metadata
  ↓
Persist Metadata
  ↓
Media = Ready
```

If analysis fails:

```text
FFprobe
  ↓
Failure
  ↓
Media = Failed
```

---

# 6. FFprobe Output

FFprobe should normally produce machine-readable output.

The preferred format is JSON.

Conceptually:

```bash
ffprobe -v quiet -print_format json -show_format -show_streams input.mp4
```

Machine-readable output should be preferred over parsing human-readable
terminal output.

---

# 7. Format Information

FFprobe can provide container-level information.

Example conceptual structure:

```json
{
  "format": {
    "filename": "input.mp4",
    "format_name": "mov,mp4,m4a,3gp,3g2,mj2",
    "duration": "120.500000",
    "size": "104857600",
    "bit_rate": "6950000"
  }
}
```

The Video Engine should normalize this information into the
application's internal media model.

---

# 8. Stream Information

A media file can contain multiple streams.

Example:

```json
{
  "streams": [
    {
      "index": 0,
      "codec_type": "video",
      "codec_name": "h264"
    },
    {
      "index": 1,
      "codec_type": "audio",
      "codec_name": "aac"
    }
  ]
}
```

The application must not assume that every media file contains exactly
one video stream and one audio stream.

---

# 9. Video Metadata

For video streams, FFprobe may provide:

* Codec
* Width
* Height
* Frame rate
* Pixel format
* Bitrate
* Profile
* Level
* Color information
* Duration
* Number of frames

Example internal representation:

```ts
interface VideoStreamMetadata {
  codec: string;
  width: number;
  height: number;
  frameRate: number;
  pixelFormat?: string;
  bitrate?: number;
  duration?: number;
}
```

---

# 10. Audio Metadata

For audio streams, FFprobe may provide:

* Codec
* Sample rate
* Channels
* Channel layout
* Bitrate
* Duration

Example:

```ts
interface AudioStreamMetadata {
  codec: string;
  sampleRate: number;
  channels: number;
  channelLayout?: string;
  bitrate?: number;
  duration?: number;
}
```

---

# 11. Container Metadata

Container information should be stored separately from codec
information.

Example:

```ts
interface ContainerMetadata {
  format: string;
  duration: number;
  size: number;
  bitrate?: number;
}
```

A container is not a codec.

For example:

```text
Container: MP4
Video Codec: H.264
Audio Codec: AAC
```

---

# 12. Internal Media Metadata

The application should normalize FFprobe output into its own schema.

Example:

```ts
interface MediaMetadata {
  duration: number;

  container: {
    format: string;
    size: number;
    bitrate?: number;
  };

  video?: {
    codec: string;
    width: number;
    height: number;
    frameRate: number;
    pixelFormat?: string;
    bitrate?: number;
  };

  audio?: {
    codec: string;
    sampleRate: number;
    channels: number;
    channelLayout?: string;
    bitrate?: number;
  };
}
```

The application should not expose raw FFprobe output throughout the
codebase.

---

# 13. Multiple Streams

Some files may contain:

* Multiple video streams
* Multiple audio streams
* Subtitle streams
* Data streams
* Attachments

The Video Engine should preserve stream information instead of
discarding unknown streams during analysis.

Example:

```text
Input
│
├── Video Stream 0
├── Audio Stream 1
├── Audio Stream 2
└── Subtitle Stream 3
```

---

# 14. Primary Stream Selection

The application may need to identify the primary video and audio
streams.

Selection rules should be explicit.

Example:

```text
Primary Video
    ↓
First compatible video stream

Primary Audio
    ↓
First compatible audio stream
```

Future versions may support user-selected streams.

---

# 15. Frame Rate

Frame rate must be handled carefully.

Media may report frame rates as ratios.

Example:

```text
30000/1001
24000/1001
25/1
30/1
60/1
```

The Video Engine should normalize frame-rate values into a consistent
internal representation while preserving the original rational value
when precision matters.

---

# 16. Variable Frame Rate

Videos may use variable frame rate.

The system should not assume that:

```text
duration × fps = exact frame count
```

for every media file.

When required, frame timestamps and FFprobe metadata should be used
instead of relying solely on nominal frame rate.

---

# 17. Duration

Duration may be available at:

* Container level
* Stream level

The application should define which value is authoritative for each
use case.

Timeline calculations should use normalized duration values.

Floating-point precision should be handled carefully when converting
media timestamps into timeline positions.

---

# 18. Resolution

FFprobe provides:

```text
width
height
```

The application should use these values to determine:

* Video dimensions
* Aspect ratio
* Orientation
* Preview layout
* Export compatibility

Example:

```text
1920 × 1080 → Landscape
1080 × 1920 → Portrait
1080 × 1080 → Square
```

---

# 19. Aspect Ratio

Aspect ratio should be calculated from media dimensions and, where
relevant, display aspect ratio metadata.

Examples:

```text
16:9
9:16
1:1
4:5
```

Aspect ratio must not always be inferred solely from the raw pixel
dimensions because pixel aspect ratio and display metadata may affect
presentation.

---

# 20. Rotation Metadata

Some media files store rotation information as metadata rather than
physically rotating frames.

The Video Engine must detect relevant rotation metadata.

Example:

```text
Physical:
1920 × 1080

Metadata:
rotation = 90°
```

The application must normalize orientation before presenting media to
the user or processing it.

---

# 21. Pixel Format

Pixel format affects processing compatibility.

Examples:

```text
yuv420p
yuv422p
yuv444p
rgba
gray
```

The Video Engine should detect unsupported pixel formats before
processing when necessary.

Export presets may define required pixel formats.

---

# 22. Codec Detection

FFprobe should identify codecs before editing or transcoding.

Examples:

```text
Video:
h264
hevc
av1
vp9

Audio:
aac
opus
mp3
```

Codec capabilities are documented in:

```text
context/video/codecs.md
```

---

# 23. Media Compatibility

FFprobe analysis should be used to determine whether a file is
compatible with the editor.

Compatibility may depend on:

* Container
* Video codec
* Audio codec
* Resolution
* Frame rate
* Pixel format
* Duration
* Number of streams

Example:

```text
Upload
  ↓
FFprobe
  ↓
Compatibility Check
  ├── Compatible → Ready
  └── Incompatible → Processing Required / Rejected
```

---

# 24. Media Validation

FFprobe should be part of the media validation pipeline.

```text
File Upload
    ↓
File Validation
    ↓
FFprobe
    ↓
Metadata Validation
    ↓
Compatibility Check
    ↓
Ready
```

Validation should detect:

* Corrupted files
* Unsupported formats
* Missing streams
* Invalid metadata
* Unexpected media structures

---

# 25. Security

FFprobe operates on user-provided media and therefore must be treated
as an untrusted-input processing component.

The system must:

* Validate input paths
* Prevent path traversal
* Use isolated temporary directories
* Restrict resource consumption
* Enforce file size limits
* Avoid unsafe shell execution
* Sanitize metadata before displaying it
* Keep FFprobe execution inside Workers

---

# 26. Timeout and Resource Limits

FFprobe should have configurable execution limits.

Possible limits include:

* Maximum execution time
* Maximum file size
* Maximum temporary storage
* Maximum concurrent analysis jobs

A corrupted or malicious media file must not be able to consume
unbounded resources.

---

# 27. Error Handling

FFprobe errors should be converted into structured application errors.

Possible categories:

```text
MEDIA_NOT_FOUND
MEDIA_CORRUPTED
UNSUPPORTED_MEDIA
INVALID_METADATA
NO_VIDEO_STREAM
NO_AUDIO_STREAM
PROBE_TIMEOUT
PROBE_PROCESS_ERROR
```

Raw FFprobe output may be logged for internal diagnostics.

---

# 28. Persistence

After successful analysis, normalized metadata should be persisted in
PostgreSQL.

Example:

```text
FFprobe
   ↓
Normalize
   ↓
Validate
   ↓
Media Metadata
   ↓
PostgreSQL
```

The database stores normalized application data rather than relying on
running FFprobe every time metadata is needed.

---

# 29. Re-Probing

The system may need to run FFprobe again when:

* Media is replaced
* Media is modified externally
* Metadata becomes inconsistent
* A processing pipeline produces a new media asset

Generated assets should receive their own media records where
appropriate.

---

# 30. FFprobe Adapter

The Video Engine should expose a high-level adapter.

Example:

```ts
interface FFprobeAdapter {
  inspect(inputPath: string): Promise<MediaMetadata>;
}
```

The adapter is responsible for:

```text
Input
 ↓
Safe FFprobe Execution
 ↓
JSON Parsing
 ↓
Normalization
 ↓
Validation
 ↓
MediaMetadata
```

---

# 31. Separation of Concerns

The system must maintain the following boundary:

```text
FFprobe
    ↓
Technical Media Information

Video Engine
    ↓
Normalized Media Metadata

Domain
    ↓
Business Meaning

Database
    ↓
Persistent State
```

FFprobe-specific field names should not leak into the entire
application.

For example, business logic should not depend directly on:

```text
codec_name
pix_fmt
r_frame_rate
```

Instead, it should use normalized domain fields such as:

```text
codec
pixelFormat
frameRate
```

---

# 32. AI Integration

AI agents may consume normalized media metadata.

Example:

```text
User:
"Make this video vertical."

        ↓

AI
        ↓

Media Metadata

1920 × 1080
16:9

        ↓

AI determines:
Target = 1080 × 1920
        ↓

Structured Edit Operation
```

AI should consume safe, normalized metadata rather than raw FFprobe
output.

---

# 33. Architectural Principle

The core principle is:

```text
FFprobe discovers media facts.

Video Engine normalizes those facts.

Domain logic interprets those facts.

AI consumes normalized information.

PostgreSQL stores persistent metadata.
```

The final architecture is:

```text
Media
  ↓
Worker
  ↓
Video Engine
  ↓
FFprobe
  ↓
Raw Metadata
  ↓
Normalizer
  ↓
Validated MediaMetadata
  ↓
PostgreSQL
  ↓
Frontend / AI / Editing Engine
```

FFprobe is therefore the system's primary media inspection layer,
not the business-domain model itself.
