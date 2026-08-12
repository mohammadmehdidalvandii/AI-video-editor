# FFmpeg Context

## Purpose

This document defines how FFmpeg is used inside the AI Video Editor.

FFmpeg is the core media-processing engine of the system.

The application must not expose raw FFmpeg commands directly to users,
frontend components, AI agents, or business logic.

FFmpeg execution must be isolated behind the Video Engine.

---

# 1. What Is FFmpeg?

FFmpeg is an open-source multimedia framework used for:

* Video processing
* Audio processing
* Encoding
* Decoding
* Transcoding
* Filtering
* Cutting
* Merging
* Resizing
* Cropping
* Subtitle processing
* Frame extraction
* Audio extraction
* Media conversion

In this project, FFmpeg is treated as an infrastructure-level
processing engine.

---

# 2. FFmpeg Position in the Architecture

FFmpeg must run inside the Worker environment.

The architecture is:

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
    ↓
FFmpeg
```

The HTTP API must never execute long-running FFmpeg operations
directly.

---

# 3. Video Engine Boundary

The Video Engine provides a controlled abstraction around FFmpeg.

```text
Worker
  ↓
Video Engine
  ↓
FFmpeg Adapter
  ↓
FFmpeg Process
```

The Worker should not construct raw FFmpeg commands.

The Video Engine owns:

* Command construction
* Input validation
* Output configuration
* Process execution
* Progress handling
* Error handling
* Temporary file management

---

# 4. FFmpeg Command Structure

A simplified FFmpeg command follows this structure:

```bash
ffmpeg [global options] [input options] -i input [filter/options] output
```

Example:

```bash
ffmpeg -i input.mp4 output.mp4
```

The command consists of several conceptual parts:

```text
Global Options
      ↓
Input Options
      ↓
Input
      ↓
Filters
      ↓
Codec / Encoding Options
      ↓
Output
```

---

# 5. Input

FFmpeg receives one or more input sources.

Examples:

```bash
ffmpeg -i input.mp4 output.mp4
```

```bash
ffmpeg -i video.mp4 -i audio.mp3 output.mp4
```

Inputs may represent:

* Video files
* Audio files
* Images
* Image sequences
* Subtitle files
* Other supported media sources

The application must validate all input sources before processing.

---

# 6. Output

FFmpeg produces an output media file.

Example:

```bash
ffmpeg -i input.mp4 output.mp4
```

The output configuration may include:

* Container
* Video codec
* Audio codec
* Resolution
* Frame rate
* Bitrate
* Quality
* Pixel format

The Video Engine should explicitly control output configuration.

---

# 7. Streams

A media file may contain multiple streams.

Typical streams include:

```text
Video Stream
Audio Stream
Subtitle Stream
```

Example:

```text
input.mp4
│
├── Video Stream
│
└── Audio Stream
```

FFmpeg operates on streams rather than treating the media file as
one indivisible object.

---

# 8. Stream Mapping

When multiple inputs or streams exist, explicit stream mapping may be
required.

Conceptually:

```text
Input 0
├── Video
└── Audio

Input 1
└── Audio

       ↓

Output
├── Video from Input 0
└── Audio from Input 1
```

The Video Engine should generate explicit stream mappings when
ambiguity exists.

---

# 9. Codecs vs Containers

FFmpeg processing must distinguish between:

```text
Container
Codec
```

Examples:

```text
Container:
MP4
MOV
MKV
WebM

Video Codec:
H.264
H.265
AV1
VP9

Audio Codec:
AAC
Opus
MP3
```

A container is not the same thing as a codec.

Detailed codec and container rules are defined in:

```text
context/video/codecs.md
context/video/containers.md
```

---

# 10. Encoding

Encoding converts decoded media into a selected codec.

Example conceptual pipeline:

```text
Input
 ↓
Decode
 ↓
Process
 ↓
Encode
 ↓
Output
```

Encoding settings may affect:

* Quality
* File size
* CPU usage
* Processing time
* Compatibility

The Video Engine should use predefined encoding presets where possible.

---

# 11. Transcoding

Transcoding converts media from one codec or format into another.

Example:

```text
H.265
  ↓
Decode
  ↓
Processing
  ↓
Encode
  ↓
H.264
```

Transcoding is generally CPU-intensive and must run inside Workers.

---

# 12. Stream Copy

Not every operation requires re-encoding.

For compatible operations, FFmpeg may copy streams without decoding and
re-encoding them.

Conceptually:

```text
Input
 ↓
Stream Copy
 ↓
Output
```

This can be significantly faster than full transcoding.

The Video Engine should use stream copying only when it is technically
safe and compatible with the requested operation.

---

# 13. Filters

FFmpeg filters modify media during processing.

Examples include:

```text
scale
crop
fps
trim
setpts
volume
atempo
overlay
drawtext
subtitles
```

Filters can operate on:

```text
Video
Audio
Both
```

Filter architecture is documented separately in:

```text
context/video/filters.md
```

---

# 14. Filter Graph

Complex editing operations may require multiple filters.

Conceptually:

```text
Input
  ↓
Crop
  ↓
Scale
  ↓
Text Overlay
  ↓
Output
```

FFmpeg represents complex processing as a filter graph.

The Video Engine should build filter graphs from structured editing
operations.

---

# 15. Editing Operation → FFmpeg

The application must never map user actions directly to shell commands.

The preferred flow is:

```text
User Action
    ↓
Editing Operation
    ↓
Operation Validation
    ↓
Video Engine
    ↓
Filter / Encoding Plan
    ↓
FFmpeg Command
    ↓
Process
```

Example:

```json
{
  "type": "resize",
  "width": 1080,
  "height": 1920
}
```

The Video Engine converts this structured operation into the required
FFmpeg processing pipeline.

---

# 16. Trim

A trim operation changes the selected temporal range of media.

Conceptually:

```text
Original

00:00 ───────────────────────── 01:00

          Selected Range

          00:10 ─────── 00:30
```

The application should represent this as an editing operation rather
than storing an FFmpeg command.

Example:

```json
{
  "type": "trim",
  "start": 10,
  "end": 30
}
```

---

# 17. Crop

Crop changes the visible region of the video.

Example:

```json
{
  "type": "crop",
  "width": 1080,
  "height": 1920,
  "x": 420,
  "y": 0
}
```

The Video Engine converts this operation into an appropriate FFmpeg
filter.

---

# 18. Resize

Resize changes output dimensions.

Example:

```json
{
  "type": "resize",
  "width": 1920,
  "height": 1080
}
```

Common target formats may include:

```text
16:9
9:16
1:1
4:5
```

The Video Engine should use predefined presets where appropriate.

---

# 19. Audio Processing

FFmpeg can process audio independently from video.

Supported operations may include:

* Volume adjustment
* Audio extraction
* Audio replacement
* Audio mixing
* Fading
* Speed adjustment
* Silence removal

Audio operations should use the same structured-operation architecture
as video operations.

---

# 20. Subtitle Processing

Subtitle workflows may involve:

```text
Subtitle Generation
       ↓
Subtitle Data
       ↓
Subtitle Styling
       ↓
FFmpeg
       ↓
Rendered Video
```

Subtitles may be:

* External
* Embedded
* Burned into video

The exact strategy depends on the export requirements.

---

# 21. Thumbnail Generation

Workers can use FFmpeg to generate thumbnails.

Conceptual flow:

```text
Media
 ↓
FFmpeg
 ↓
Selected Frame
 ↓
Image
 ↓
Object Storage
```

Thumbnail generation should be asynchronous for uploaded media.

---

# 22. Preview vs Final Render

Preview rendering and final rendering are separate concerns.

```text
Timeline
   │
   ├── Preview Pipeline
   │
   └── Final Render Pipeline
```

Preview processing should prioritize:

* Speed
* Responsiveness
* Low resolution

Final rendering should prioritize:

* Output quality
* Correctness
* Requested resolution
* Requested codec
* Requested format

---

# 23. Temporary Processing

FFmpeg may require temporary working files.

The Worker should create an isolated workspace:

```text
/tmp/job_123/
├── input.mp4
├── intermediate.mp4
└── output.mp4
```

After successful processing:

```text
Output
 ↓
Object Storage
```

After processing:

```text
Temporary Workspace
 ↓
Cleanup
```

Cleanup must also occur after failed jobs.

---

# 24. Process Execution

FFmpeg must be executed through a safe process API.

The application should avoid unsafe shell string construction.

Preferred conceptual model:

```text
Executable
+
Arguments[]
```

instead of:

```text
Shell Command String
```

This reduces command injection risks.

---

# 25. FFmpeg Errors

FFmpeg failures must be captured and converted into structured
application errors.

Possible categories:

```text
INVALID_INPUT
UNSUPPORTED_FORMAT
INVALID_FILTER
ENCODING_ERROR
DECODING_ERROR
OUTPUT_ERROR
PROCESS_ERROR
TIMEOUT
```

Raw FFmpeg stderr should be available for internal debugging but
should not automatically be exposed to end users.

---

# 26. Progress

FFmpeg processing progress should be monitored where possible.

The Worker may report:

```json
{
  "status": "processing",
  "progress": 65
}
```

Progress should be communicated to the backend job system.

The frontend can then display the progress to the user.

---

# 27. Resource Management

FFmpeg may consume significant:

* CPU
* RAM
* Disk I/O
* Network bandwidth

The Worker must control:

* Concurrency
* Temporary disk usage
* Maximum processing duration
* Input size
* Output size

Resource limits should be configurable.

---

# 28. Security

FFmpeg execution is considered a privileged infrastructure operation.

The system must:

* Validate input paths
* Validate output paths
* Prevent path traversal
* Validate operation parameters
* Restrict supported formats
* Prevent arbitrary command execution
* Avoid shell interpolation
* Isolate temporary files
* Validate AI-generated operations

AI agents must never receive unrestricted shell access.

---

# 29. Extensibility

New video capabilities should be implemented as structured operations.

Example:

```text
operations/
├── trim
├── crop
├── resize
├── rotate
├── speed
├── volume
├── subtitles
└── overlay
```

Each operation should define:

```text
Input Schema
Validation
Processing Logic
FFmpeg Representation
Output Expectations
```

This allows the Video Engine to grow without coupling the entire
application to FFmpeg command syntax.

---

# 30. Architectural Principle

The core principle is:

```text
Users do not control FFmpeg.

AI does not control FFmpeg.

Business Logic does not control FFmpeg directly.

Video Engine controls FFmpeg.
```

The final architecture is:

```text
User
 ↓
Frontend
 ↓
Editing Operation
 ↓
Backend
 ↓
Job
 ↓
Worker
 ↓
Video Engine
 ↓
FFmpeg
 ↓
Output
```

FFmpeg is an implementation detail of the Video Engine rather than a
business-domain API.
