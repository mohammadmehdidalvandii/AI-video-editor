# Rendering Context

## Purpose

This document defines the rendering architecture of the AI Video Editor.

Rendering is the process of converting the editable project state into executable media-processing instructions and finally generating an output media file.

The rendering system connects:

- Project
- Timeline
- Tracks
- Clips
- Effects
- Audio
- Render Planner
- Render Jobs
- Workers
- FFmpeg
- FFprobe
- Storage

The rendering system must remain independent from the frontend and AI reasoning layers.

---

# 1. What Is Rendering?

Rendering converts an editable Timeline into a final media output.

Conceptually:

    Project
      ↓
    Timeline
      ↓
    Tracks
      ↓
    Clips
      ↓
    Render Plan
      ↓
    Render Job
      ↓
    Worker
      ↓
    FFmpeg
      ↓
    Output Media

---

# 2. Rendering Principles

The Rendering System must be:

- Deterministic
- Non-destructive
- Asynchronous
- Fault tolerant
- Observable
- Scalable
- Reproducible
- Independent from the frontend
- Independent from AI decision making

The rendering system must never modify the original source media.

---

# 3. Rendering Boundary

The Editing Domain describes what the user wants.

The Rendering Engine determines how to produce it.

Example:

    Clip:
      speed = 2
      opacity = 0.5
      blur = 5

The Rendering Engine converts this into media-processing instructions.

The domain should not contain raw FFmpeg commands.

---

# 4. Rendering Pipeline

Complete pipeline:

    Project
       ↓
    Validate Project
       ↓
    Resolve Media
       ↓
    Analyze Timeline
       ↓
    Build Render Plan
       ↓
    Validate Render Plan
       ↓
    Create Render Job
       ↓
    Queue Job
       ↓
    Worker
       ↓
    FFmpeg
       ↓
    Output
       ↓
    Storage
       ↓
    Render Result

---

# 5. Render Request

A Render Request asks the system to generate an output.

Conceptual model:

    interface RenderRequest {
      projectId: string;
      version: number;
      output: RenderOutputConfig;
    }

Example:

    {
      "projectId": "project-001",
      "version": 42,
      "output": {
        "format": "mp4",
        "resolution": "1920x1080"
      }
    }

---

# 6. Render Version

Every render should reference a specific project version.

Example:

    Project Version:
    42

    Render:
    project-001:v42

This ensures that the render is reproducible.

If the project changes after the render starts, the running render should continue using the selected version.

---

# 7. Render Output

Output configuration may include:

- Format
- Container
- Video Codec
- Audio Codec
- Resolution
- Frame Rate
- Bitrate
- Quality
- Audio Sample Rate
- Audio Channels

Example:

    {
      "format": "mp4",
      "videoCodec": "h264",
      "audioCodec": "aac",
      "width": 1920,
      "height": 1080,
      "fps": 30
    }

---

# 8. Render Presets

The system should support predefined presets.

Examples:

    YouTube 1080p
    YouTube 4K
    Instagram Reels
    TikTok
    YouTube Shorts
    Preview
    Draft

A preset should define output configuration.

---

# 9. Render Preset Example

Conceptual preset:

    {
      "name": "youtube-1080p",
      "width": 1920,
      "height": 1080,
      "fps": 30,
      "videoCodec": "h264",
      "audioCodec": "aac",
      "container": "mp4"
    }

Presets should be versioned when required.

---

# 10. Render Plan

A Render Plan is an executable representation of the project.

It describes:

- Inputs
- Clips
- Timing
- Filters
- Audio
- Track composition
- Output configuration

Conceptual model:

    interface RenderPlan {
      projectId: string;
      version: number;
      inputs: RenderInput[];
      tracks: RenderTrack[];
      output: RenderOutputConfig;
    }

---

# 11. Render Plan Responsibility

The Render Plan should answer:

- Which media files are required?
- Which time ranges are required?
- Which filters are required?
- Which tracks exist?
- How should clips be composed?
- How should audio be mixed?
- What should the output format be?

It should contain enough information for the Worker to execute rendering.

---

# 12. Render Input

A Render Input represents a source media input.

Conceptual model:

    interface RenderInput {
      id: string;
      sourceId: string;
      path: string;
      mediaType: "video" | "audio" | "image";
      duration: number;
    }

The exact filesystem or object-storage implementation should remain outside the domain.

---

# 13. Source Resolution

Rendering resolves:

    Clip
      ↓
    sourceId
      ↓
    Media Asset
      ↓
    Storage Object
      ↓
    Worker Input

The Clip should not directly contain storage implementation details.

---

# 14. FFprobe in Rendering

FFprobe provides media metadata.

The rendering pipeline may use FFprobe to determine:

- Duration
- Width
- Height
- Frame Rate
- Video Codec
- Audio Codec
- Pixel Format
- Audio Sample Rate
- Audio Channels
- Stream Information

FFprobe is primarily an inspection tool.

FFmpeg performs the actual media processing.

---

# 15. Media Validation

Before rendering, validate all required media.

Checks include:

- File exists
- Media is readable
- Required streams exist
- Codec is supported
- Duration is valid
- Source range is valid
- Resolution is valid

Invalid media should stop the render before expensive processing begins.

---

# 16. Render Validation

The Render Planner must validate:

- Project exists
- Project version exists
- Timeline is valid
- Tracks are valid
- Clips are valid
- Media sources exist
- Source ranges are valid
- Effects are valid
- Output configuration is valid

---

# 17. Render Plan Generation

The Render Planner converts domain state into a Render Plan.

Flow:

    Project
      ↓
    Timeline
      ↓
    Tracks
      ↓
    Clips
      ↓
    Effects
      ↓
    Audio
      ↓
    Render Planner
      ↓
    Render Plan

---

# 18. Render Planner Responsibility

The Render Planner is responsible for:

- Resolving clips
- Calculating timing
- Preparing filter definitions
- Preparing audio processing
- Preparing track composition
- Preparing output configuration
- Validating the resulting plan

The Render Planner must not execute FFmpeg.

---

# 19. Clip Render Plan

Each Clip may generate a render representation.

Conceptual model:

    interface ClipRenderPlan {
      clipId: string;
      inputId: string;

      sourceStart: number;
      sourceEnd: number;

      timelineStart: number;

      speed: number;
      reverse: boolean;

      filters: RenderFilter[];
    }

---

# 20. Clip Timing

A Clip has two timing systems:

Source Time:

    sourceStart
    sourceEnd

Timeline Time:

    timelineStart
    timelineEnd

The Render Planner maps source timing to Timeline timing.

---

# 21. Trim Rendering

A trimmed Clip requires selecting a source range.

Conceptually:

    Source:
    0 ───────────────────────── 60

    Clip:
         10 ─────────── 30

Render:

    Source 10 → 30

The original source remains unchanged.

---

# 22. Speed Rendering

For:

    speed = 2

The Render Planner must generate the appropriate timing transformation.

Conceptually:

    Source Duration = 20s

    Speed = 2x

    Output Duration = 10s

Video and audio timing must remain synchronized where applicable.

---

# 23. Reverse Rendering

If:

    reverse = true

The Render Planner must generate a reverse processing strategy.

Conceptually:

    A → B → C → D

becomes:

    D → C → B → A

Reverse processing must not modify source media.

---

# 24. Transform Rendering

Transform properties may include:

- Position
- Scale
- Rotation
- Anchor
- Crop

The Render Planner converts these properties into render filters.

Example:

    Clip
      ↓
    Transform
      ↓
    Render Filter
      ↓
    FFmpeg Filter Graph

---

# 25. Effect Rendering

Domain effect:

    {
      "type": "blur",
      "parameters": {
        "strength": 5
      }
    }

Render representation:

    RenderFilter

The Video Engine translates the RenderFilter into the appropriate FFmpeg implementation.

---

# 26. Filter Abstraction

The domain should not contain raw FFmpeg filter syntax.

Instead:

    interface RenderFilter {
      type: string;
      parameters: Record<string, unknown>;
      order: number;
    }

Example:

    {
      "type": "blur",
      "parameters": {
        "strength": 5
      },
      "order": 2
    }

---

# 27. Filter Ordering

Filters must execute in deterministic order.

Example:

    Input
      ↓
    Trim
      ↓
    Scale
      ↓
    Crop
      ↓
    Color
      ↓
    Blur
      ↓
    Output

Changing filter order may change the output.

---

# 28. Track Rendering

Each Track produces a render representation.

Conceptually:

    Track
      ↓
    Clips
      ↓
    Clip Render Plans
      ↓
    Track Composition

A Track may contain:

- Video Clips
- Audio Clips
- Text
- Overlays
- Effects

---

# 29. Video Track Composition

Example:

    Track 1:
    [Clip A]────[Clip B]

    Track 2:
          [Overlay C]

Final composition:

    Track 1 + Track 2

The Render Planner determines how layers are composed.

---

# 30. Layer Ordering

Visual tracks may have an explicit order.

Example:

    Top Layer
       ↓
    Overlay
       ↓
    Video
       ↓
    Background

Higher layers may visually cover lower layers.

---

# 31. Track Opacity

Tracks may eventually support global opacity.

Example:

    Track Opacity = 0.5

The Render Planner must apply the track-level property during composition.

---

# 32. Audio Rendering

Audio rendering may include:

- Volume
- Mute
- Speed
- Audio trimming
- Audio mixing
- Channel mapping
- Sample rate conversion

Audio processing should remain synchronized with Timeline timing.

---

# 33. Audio Mixing

Multiple audio sources may exist:

    Voice
      +
    Music
      +
    Original Audio
      ↓
    Audio Mixer
      ↓
    Final Audio

The Render Planner creates the required audio composition.

---

# 34. Audio Synchronization

Video and audio must use the same Timeline reference.

Example:

    Video Clip:
    0s → 10s

    Audio Clip:
    0s → 10s

Both must align correctly in the final output.

---

# 35. Detached Audio

Detached Audio becomes an independent Timeline entity.

Conceptually:

    Video Clip
        │
        └── Video

    Audio Clip
        │
        └── Audio

The renderer processes them independently and mixes them during final composition.

---

# 36. Text Rendering

Text may eventually be represented as a renderable layer.

Conceptually:

    Text Clip
      ↓
    Text Render Plan
      ↓
    Text Layer
      ↓
    Composition

Text rendering may use FFmpeg filters or a separate rendering mechanism.

---

# 37. Image Rendering

Images may be treated as timeline media.

Example:

    image.jpg
        ↓
    Clip
        ↓
    Duration
        ↓
    Scale
        ↓
    Timeline

The renderer must generate the required frame sequence or equivalent processing.

---

# 38. Subtitle Rendering

Subtitle Tracks may support:

- SRT
- WebVTT
- ASS
- Burned-in subtitles
- External subtitle tracks

The Render Planner determines whether subtitles are:

- Embedded
- Burned into video
- Exported separately

---

# 39. Render Graph

Complex projects can be represented as a graph.

Conceptually:

    Input A ─────┐
                 ├── Filter ──┐
    Input B ─────┘            │
                              ├── Composite ── Output
    Input C ───── Filter ─────┘

The graph represents media-processing dependencies.

---

# 40. Filter Graph

The Video Engine converts the Render Plan into an FFmpeg filter graph.

Conceptually:

    Render Plan
       ↓
    Filter Graph Builder
       ↓
    FFmpeg Filter Graph
       ↓
    FFmpeg

The Filter Graph Builder is infrastructure-specific.

---

# 41. FFmpeg Boundary

FFmpeg should be isolated behind a Video Engine interface.

Conceptual interface:

    interface VideoEngine {
      render(
        plan: RenderPlan
      ): Promise<RenderResult>;
    }

The rest of the application should not depend directly on FFmpeg process execution.

---

# 42. FFmpeg Adapter

Implementation:

    VideoEngine
        ↓
    FFmpegAdapter
        ↓
    FFmpeg Process

The adapter is responsible for:

- Building commands
- Executing processes
- Capturing logs
- Handling exit codes
- Reporting progress
- Managing temporary files

---

# 43. FFmpeg Command Generation

The system should generate FFmpeg commands from structured render data.

Flow:

    RenderPlan
       ↓
    Command Builder
       ↓
    FFmpeg Arguments
       ↓
    Process Runner
       ↓
    FFmpeg

The system should prefer argument arrays over unsafe shell string concatenation.

---

# 44. FFmpeg Security

Never directly execute arbitrary AI-generated FFmpeg commands.

AI:

    "Make this clip blurry."

AI generates:

    addEffect

The Rendering Engine determines the FFmpeg implementation.

AI must never generate:

    ffmpeg -i ... -vf ...

as executable input.

---

# 45. Render Job

Rendering should be asynchronous.

Conceptual model:

    interface RenderJob {
      id: string;
      projectId: string;
      version: number;
      status: RenderJobStatus;
      progress: number;
    }

Possible states:

    queued
    preparing
    rendering
    processing
    completed
    failed
    cancelled

---

# 46. Render Queue

The backend should submit render jobs to a queue.

Flow:

    API
      ↓
    Render Service
      ↓
    Queue
      ↓
    Worker
      ↓
    FFmpeg

Redis + BullMQ may be used for queue management.

---

# 47. Why Rendering Must Be Asynchronous

Rendering can take:

- Seconds
- Minutes
- Hours

depending on:

- Video length
- Resolution
- Effects
- Codec
- Hardware
- Number of clips

The API must not remain blocked during rendering.

---

# 48. Worker Architecture

The Worker is responsible for executing Render Jobs.

Conceptually:

    Render Queue
        ↓
      Worker
        ↓
    Prepare Files
        ↓
    Execute FFmpeg
        ↓
    Monitor Progress
        ↓
    Store Output
        ↓
    Update Job

---

# 49. Worker Isolation

Workers should be isolated from the API process.

API:

    Request handling
    Authentication
    Project management
    Editing operations

Worker:

    Rendering
    FFmpeg
    Media processing

This prevents heavy rendering tasks from blocking API requests.

---

# 50. Worker Scaling

Multiple workers may process multiple Render Jobs.

Example:

    Queue
      │
      ├── Worker 1
      ├── Worker 2
      ├── Worker 3
      └── Worker 4

Worker count can scale according to CPU, GPU, and workload.

---

# 51. Render Concurrency

Rendering concurrency must be controlled.

Example:

    Server:
    8 CPU cores

Possible:

    2 concurrent FFmpeg jobs

The correct concurrency depends on:

- CPU
- RAM
- Storage
- Codec
- Resolution
- FFmpeg workload

---

# 52. Render Job Priority

Jobs may have priorities.

Example:

    Preview      → HIGH
    User Export  → HIGH
    Background   → LOW

The queue may process higher-priority jobs first.

---

# 53. Preview Rendering

Preview rendering is different from final rendering.

Preview should prioritize:

- Speed
- Low latency
- Smaller resolution
- Lower quality

Final rendering prioritizes:

- Quality
- Correctness
- Target output configuration

---

# 54. Draft Rendering

Draft rendering may use:

- Lower resolution
- Lower bitrate
- Faster codecs
- Proxy media
- Reduced effects

Draft output should never replace the original project state.

---

# 55. Final Rendering

Final rendering should use:

- Original media
- Requested resolution
- Requested codec
- Requested quality
- Full effect configuration
- Full audio configuration

---

# 56. Render Progress

FFmpeg progress should be converted into normalized progress.

Example:

    0%
      ↓
    Preparing

    25%
      ↓
    Processing clips

    75%
      ↓
    Encoding

    100%
      ↓
    Completed

The exact progress calculation depends on the render plan.

---

# 57. Progress Reporting

Progress may be stored and exposed through:

- REST API
- WebSocket
- Server-Sent Events

Example:

    Client
      ↓
    GET /render-jobs/:id

or:

    Client
      ↓
    WebSocket
      ↓
    Render Progress

---

# 58. Render Logs

Every Render Job should produce structured logs.

Useful information:

- Job ID
- Project ID
- Project version
- Worker ID
- FFmpeg version
- Start time
- End time
- Exit code
- Error
- Processing duration

Raw FFmpeg logs may also be stored for debugging.

---

# 59. Render Error Handling

Possible errors:

    RENDER_INVALID_PROJECT
    RENDER_SOURCE_NOT_FOUND
    RENDER_UNSUPPORTED_CODEC
    RENDER_INVALID_FILTER
    RENDER_FFMPEG_FAILED
    RENDER_STORAGE_FAILED
    RENDER_TIMEOUT
    RENDER_CANCELLED
    RENDER_OUT_OF_MEMORY
    RENDER_DISK_FULL

Errors should use stable machine-readable codes.

---

# 60. FFmpeg Exit Code

FFmpeg process completion must be checked.

Example:

    Exit Code = 0

means successful execution.

Non-zero exit codes must be treated as failures unless the adapter explicitly handles a known exception.

---

# 61. Render Retry

Failed jobs may be retried when the error is temporary.

Retryable examples:

- Temporary storage failure
- Worker crash
- Temporary network failure

Non-retryable examples:

- Invalid source
- Invalid filter
- Unsupported media
- Invalid project state

---

# 62. Render Timeout

Long-running jobs should have configurable timeout policies.

The timeout should depend on:

- Output duration
- Resolution
- Complexity
- Worker resources

A timeout should terminate the worker task safely.

---

# 63. Render Cancellation

Users may cancel a running render.

Flow:

    User
      ↓
    Cancel Request
      ↓
    Render Service
      ↓
    Queue / Worker
      ↓
    FFmpeg Termination
      ↓
    Cleanup
      ↓
    Job = cancelled

Cancellation must clean temporary resources.

---

# 64. Temporary Files

Rendering may require temporary files.

Examples:

- Intermediate videos
- Extracted audio
- Proxy files
- Generated images
- Filter intermediates

Temporary files must have lifecycle management.

---

# 65. Temporary Storage

Suggested structure:

    /tmp
      /render
        /job-001
        /job-002
        /job-003

Each Render Job should have an isolated working directory.

---

# 66. Cleanup

After completion or failure:

    Render Job
        ↓
    Cleanup
        ↓
    Temporary Files Removed

Cleanup should also happen after worker crashes when possible.

A periodic cleanup process may handle orphaned temporary files.

---

# 67. Output Storage

Final outputs should be stored separately from temporary files.

Conceptually:

    Worker
      ↓
    Render Output
      ↓
    Object Storage
      ↓
    Media Asset / Render Result

Storage may use:

- Local filesystem
- S3-compatible storage
- Cloud object storage

The storage implementation must remain abstracted.

---

# 68. Render Result

Conceptual model:

    interface RenderResult {
      jobId: string;
      status: "completed" | "failed";
      outputId?: string;
      duration?: number;
      size?: number;
      error?: RenderError;
    }

---

# 69. Render Asset

The generated output should be represented as a media resource.

Example:

    Render Job
       ↓
    Output File
       ↓
    Media Asset
       ↓
    Project Export

This allows the application to manage generated outputs consistently.

---

# 70. Render Cache

Identical Render Plans may be cached.

Conceptually:

    Render Plan
       ↓
    Hash
       ↓
    Cache Lookup

If a matching completed render exists:

    Return Existing Output

Otherwise:

    Start Render Job

---

# 71. Render Hash

A render hash may be calculated from:

- Project version
- Render configuration
- Media versions
- Effect configuration
- Renderer version

Example:

    hash(
      projectVersion,
      renderConfig,
      mediaVersions,
      rendererVersion
    )

Changing any important input should produce a different hash.

---

# 72. Renderer Version

The rendering engine should have a version.

Example:

    rendererVersion = 1.4.0

This helps ensure reproducibility.

A project rendered with Renderer Version 1 may produce different output from Renderer Version 2.

---

# 73. Reproducible Rendering

The same:

    Project Version
    +
    Render Configuration
    +
    Media Versions
    +
    Renderer Version

should produce the same intended result.

This is important for debugging and auditing.

---

# 74. Render Security

The rendering system must protect against:

- Path traversal
- Arbitrary command execution
- Malicious media files
- Resource exhaustion
- Unauthorized project access
- Unsafe temporary file handling

Workers should operate with restricted permissions whenever practical.

---

# 75. Resource Limits

Workers should have limits for:

- CPU
- Memory
- Disk
- Job duration
- Input file size
- Output file size

This protects the system from runaway renders.

---

# 76. Render Isolation

For stronger security and reliability, rendering may eventually run inside isolated environments.

Possible technologies:

- Docker
- Containers
- Dedicated worker processes
- Sandboxed execution

The first MVP may use isolated Node.js worker processes or Docker workers.

---

# 77. Render Observability

Important metrics:

- Render duration
- Queue wait time
- CPU usage
- Memory usage
- FFmpeg failures
- Worker failures
- Render success rate
- Average processing ratio
- Output size
- Queue depth

---

# 78. Render Processing Ratio

Useful metric:

    Processing Ratio =
    Render Time / Output Duration

Example:

    Output = 60 seconds
    Render Time = 30 seconds

    Ratio = 0.5

Lower is generally better.

---

# 79. Render Architecture

Conceptual architecture:

    Express API
        │
        ↓
    Render Service
        │
        ├──────────────→ Database
        │
        ↓
    Redis / Queue
        │
        ↓
    Render Worker
        │
        ├── FFprobe
        │
        ├── FFmpeg
        │
        └── Storage
        │
        ↓
    Render Result

---

# 80. Render Service

The Render Service is responsible for:

- Receiving render requests
- Validating project state
- Creating Render Jobs
- Generating Render Plans
- Submitting jobs to the queue
- Tracking job state
- Returning job status

It should not perform heavy FFmpeg processing inside the API process.

---

# 81. Render Planner vs Worker

Render Planner:

    Determines WHAT should happen.

Worker:

    Performs the actual processing.

Example:

    Planner:
    "Clip 1 should be trimmed to 10–30 seconds."

    Worker:
    Executes the corresponding media-processing pipeline.

---

# 82. Render Planner vs FFmpeg

Render Planner:

    Domain-aware
    Timeline-aware
    Clip-aware

FFmpeg:

    Media-processing engine
    Codec-aware
    Filter-aware
    Encoding-aware

The Render Planner prepares structured instructions.

The FFmpeg Adapter converts them into executable processing.

---

# 83. Backend Rendering Modules

Suggested structure:

    backend/
    └── src/
        ├── modules/
        │   └── rendering/
        │       ├── domain/
        │       │   ├── RenderJob.ts
        │       │   ├── RenderPlan.ts
        │       │   └── RenderResult.ts
        │       │
        │       ├── application/
        │       │   ├── RenderService.ts
        │       │   └── RenderPlanner.ts
        │       │
        │       ├── infrastructure/
        │       │   ├── FFmpegAdapter.ts
        │       │   ├── FFprobeAdapter.ts
        │       │   └── StorageAdapter.ts
        │       │
        │       └── workers/
        │           └── RenderWorker.ts

The structure may evolve as implementation becomes more complex.

---

# 84. Queue Structure

Suggested queue:

    render-queue

Possible job types:

    render.preview
    render.draft
    render.final

Future queues:

    media.probe
    media.thumbnail
    media.waveform
    media.proxy
    ai.analysis

---

# 85. Render Job Lifecycle

The lifecycle is:

    created
       ↓
    queued
       ↓
    preparing
       ↓
    rendering
       ↓
    processing
       ↓
    completed

Failure path:

    rendering
       ↓
    failed

Cancellation path:

    rendering
       ↓
    cancelled

---

# 86. Render State Machine

Conceptually:

    CREATED
       ↓
    QUEUED
       ↓
    PREPARING
       ↓
    RENDERING
       ↓
    PROCESSING
       ↓
    COMPLETED

Failure:

    any active state
       ↓
    FAILED

Cancellation:

    QUEUED / PREPARING / RENDERING
       ↓
    CANCELLED

Invalid state transitions must be rejected.

---

# 87. Render Job Persistence

Render Job state should be persisted.

Possible database fields:

    id
    projectId
    projectVersion
    status
    progress
    preset
    outputFormat
    outputId
    errorCode
    createdAt
    startedAt
    completedAt

Sequelize can be used for persistence.

---

# 88. Render Job API

Possible API endpoints:

    POST /projects/:projectId/renders

    GET /render-jobs/:jobId

    POST /render-jobs/:jobId/cancel

    GET /render-jobs/:jobId/logs

The exact API design may evolve.

---

# 89. Render API Response

Example:

    {
      "jobId": "render-001",
      "status": "queued",
      "progress": 0
    }

The API should return quickly after queueing the job.

---

# 90. Render Status

Example:

    {
      "jobId": "render-001",
      "status": "rendering",
      "progress": 57
    }

The frontend can poll or subscribe to updates.

---

# 91. Render Notifications

When rendering completes, the system may notify:

- Frontend
- User
- Project
- Notification service

Possible mechanisms:

- WebSocket
- Server-Sent Events
- Queue events
- Database state polling

---

# 92. Render and AI

AI should be able to request rendering through tools.

Example:

User:

    "Export this video in 1080p."

AI:

    renderProject({
      projectId,
      preset: "youtube-1080p"
    })

The AI should not invoke FFmpeg directly.

---

# 93. AI Render Tool

Conceptual tool:

    render_project

Input:

    {
      "projectId": "project-001",
      "preset": "youtube-1080p"
    }

Output:

    {
      "jobId": "render-001",
      "status": "queued"
    }

The tool returns the Render Job state rather than blocking until completion.

---

# 94. AI Render Status

AI may use another tool:

    get_render_status

Input:

    {
      "jobId": "render-001"
    }

Output:

    {
      "status": "completed",
      "progress": 100
    }

---

# 95. AI Rendering Safety

AI-generated rendering requests must be validated.

The AI must not be able to:

- Access another user's project
- Select arbitrary files
- Execute arbitrary FFmpeg commands
- Change worker configuration
- Consume unlimited resources

---

# 96. Rendering and Context Engineering

The Context Engine should expose only the rendering information required by the AI.

Example:

    Project Context
       ↓
    Render Capabilities
       ↓
    Available Presets
       ↓
    Render Tool
       ↓
    Render Job

The AI does not need the complete internal FFmpeg architecture.

---

# 97. Rendering Context for AI

Example:

    {
      "renderCapabilities": {
        "presets": [
          "youtube-1080p",
          "youtube-4k",
          "instagram-reels"
        ],
        "formats": [
          "mp4"
        ]
      }
    }

This keeps the AI context compact.

---

# 98. Rendering Rules

The rendering system must follow:

1. Never modify original media.
2. Never trust AI-generated FFmpeg commands.
3. Validate project state before rendering.
4. Render a specific project version.
5. Keep rendering asynchronous.
6. Isolate heavy processing from the API.
7. Validate all media sources.
8. Validate all filters.
9. Track render progress.
10. Store render results separately.
11. Clean temporary files.
12. Record render failures.
13. Support cancellation.
14. Support retry for transient failures.
15. Keep Renderer Version information.
16. Make rendering reproducible where practical.

---

# 99. Rendering Folder Structure

Suggested backend architecture:

    rendering/
    ├── domain/
    │   ├── RenderJob.ts
    │   ├── RenderPlan.ts
    │   ├── RenderInput.ts
    │   ├── RenderTrack.ts
    │   ├── RenderFilter.ts
    │   └── RenderResult.ts
    │
    ├── application/
    │   ├── RenderService.ts
    │   ├── RenderPlanner.ts
    │   ├── RenderValidator.ts
    │   └── RenderJobService.ts
    │
    ├── infrastructure/
    │   ├── FFmpegAdapter.ts
    │   ├── FFprobeAdapter.ts
    │   ├── StorageAdapter.ts
    │   └── ProcessRunner.ts
    │
    ├── workers/
    │   ├── RenderWorker.ts
    │   └── RenderJobProcessor.ts
    │
    └── repositories/
        └── RenderJobRepository.ts

---

# 100. Rendering and FFmpeg Separation

The architecture must preserve this boundary:

    Editing Domain
          ↓
    Render Plan
          ↓
    Rendering Application
          ↓
    Video Engine
          ↓
    FFmpeg Adapter
          ↓
    FFmpeg

The Editing Domain must not depend on FFmpeg implementation details.

---

# 101. Rendering and FFprobe Separation

FFprobe belongs to the infrastructure layer.

Conceptually:

    Media Asset
        ↓
    FFprobe Adapter
        ↓
    Media Metadata
        ↓
    Media Service
        ↓
    Domain

FFprobe should not be called directly from React or AI agents.

---

# 102. Rendering and Storage Separation

The renderer should not assume a specific storage provider.

Use:

    StorageAdapter

Conceptual interface:

    interface StorageAdapter {
      getInput(sourceId: string): Promise<StorageObject>;
      saveOutput(file: string): Promise<StorageObject>;
      deleteTemporary(path: string): Promise<void>;
    }

This allows future migration between local storage and object storage.

---

# 103. Rendering and Worker Separation

The API creates jobs.

The Worker executes jobs.

Conceptually:

    API
      ↓
    Render Service
      ↓
    Queue
      ↓
    Worker
      ↓
    FFmpeg

This separation is essential for scalability.

---

# 104. Rendering and Docker

The rendering worker may run inside Docker.

Example:

    Render Worker Container
        │
        ├── Node.js
        ├── FFmpeg
        ├── FFprobe
        └── Worker Runtime

This makes the rendering environment more reproducible.

---

# 105. Rendering Scalability

The rendering system should support horizontal scaling.

Example:

    Redis Queue
         │
    ┌────┼────┐
    ↓    ↓    ↓
 Worker Worker Worker
    1      2      3

The API does not need to know which Worker processes a job.

---

# 106. Rendering Bottlenecks

Potential bottlenecks:

- CPU
- GPU
- RAM
- Disk I/O
- Network
- Storage
- FFmpeg encoding
- Concurrent jobs

Monitoring should identify the limiting resource.

---

# 107. Rendering MVP

The initial MVP should implement:

1. Render Request
2. Render Job
3. Render Plan
4. Media Resolution
5. Basic Clip Rendering
6. Basic Video Track
7. Basic Audio Track
8. FFmpeg Adapter
9. FFprobe Adapter
10. Worker
11. Queue
12. Output Storage
13. Progress
14. Error Handling

Advanced features can be implemented later.

---

# 108. MVP Rendering Flow

Initial implementation:

    Frontend
       ↓
    POST /renders
       ↓
    Express
       ↓
    Render Service
       ↓
    Render Planner
       ↓
    Render Job
       ↓
    BullMQ
       ↓
    Worker
       ↓
    FFmpeg
       ↓
    Output File
       ↓
    Storage
       ↓
    Render Result

---

# 109. Advanced Rendering Features

Future features:

- GPU acceleration
- Distributed rendering
- Render caching
- Proxy rendering
- Partial rendering
- Segment rendering
- Parallel clip processing
- Smart re-rendering
- Incremental rendering
- Background rendering
- Collaborative rendering
- Advanced compositing

---

# 110. Partial Rendering

Instead of rendering the complete Timeline, the system may render only the changed region.

Example:

    Timeline:

    [A][B][C][D][E]

Only B changed.

Possible future strategy:

    Re-render B
        ↓
    Reuse A
    Reuse C
    Reuse D
    Reuse E
        ↓
    Compose Final Output

This can significantly reduce render time.

---

# 111. Incremental Rendering

The renderer may detect unchanged portions.

Conceptually:

    Project Version 10

    A = unchanged
    B = changed
    C = unchanged

Only B requires expensive processing.

Incremental rendering is an advanced optimization and should not be required for the MVP.

---

# 112. Render Cache Strategy

Potential cache layers:

    Media Cache
    ↓
    Clip Cache
    ↓
    Track Cache
    ↓
    Timeline Cache
    ↓
    Final Render Cache

The initial implementation should start with final render caching only if necessary.

---

# 113. Render Determinism

Rendering should record:

- Project version
- Renderer version
- FFmpeg version
- FFprobe version
- Render preset
- Media versions
- Render configuration

This allows debugging differences between outputs.

---

# 114. Render Audit

A Render Job should allow investigation of:

    Who requested it?
    Which project version?
    Which preset?
    Which renderer version?
    Which worker?
    Which FFmpeg version?
    When did it start?
    When did it finish?
    Why did it fail?

This is important for production systems.

---

# 115. Final Architecture

The complete rendering architecture is:

    ┌──────────────────────┐
    │      Frontend        │
    └──────────┬───────────┘
               │
               ↓
    ┌──────────────────────┐
    │     Express API      │
    └──────────┬───────────┘
               │
               ↓
    ┌──────────────────────┐
    │   Render Service     │
    └──────────┬───────────┘
               │
        ┌──────┴───────┐
        ↓              ↓
   Render Planner    Database
        │
        ↓
    Render Plan
        │
        ↓
    Render Job
        │
        ↓
    Redis / BullMQ
        │
        ↓
    ┌──────────────────────┐
    │    Render Worker     │
    └──────────┬───────────┘
               │
       ┌───────┼────────┐
       ↓       ↓        ↓
    FFprobe  FFmpeg   Storage
       │       │        │
       └───────┼────────┘
               ↓
          Render Result
               ↓
          Output Media

The Rendering System is the execution layer that transforms the declarative editing state of the Timeline into actual media.

The architecture must preserve a strict separation between:

    Editing Intent
          ↓
    Render Plan
          ↓
    Media Processing
          ↓
    Output

This separation allows the same editing model to be controlled by both humans and AI while keeping the actual media-processing system deterministic, secure, scalable, and independent from the AI layer.