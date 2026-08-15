# Clips Context

## Purpose

This document defines the structure, behavior, lifecycle, and rules of Clips inside the AI Video Editor.

A Clip is a timeline representation of a media asset.

A Clip does not contain the original media file. It references a media asset and defines how that asset is used inside the Timeline.

---

# 1. What Is a Clip?

A Clip represents a portion of a media asset placed on a Timeline.

A Clip may represent:

- Video
- Audio
- Image
- Subtitle
- Text
- Overlay
- Generated media

Conceptually:

Media Asset
    ↓
  Clip
    ↓
 Timeline

---

# 2. Clip Responsibilities

A Clip is responsible for:

- Referencing source media
- Defining source time range
- Defining timeline position
- Defining duration
- Defining playback speed
- Defining transformations
- Defining clip-level effects
- Defining audio properties
- Defining visual properties

A Clip must not contain FFmpeg commands.

---

# 3. Clip Identity

Every Clip must have a unique ID.

Example:

    interface Clip {
      id: string;
    }

The Clip ID must remain stable throughout the Clip lifecycle.

---

# 4. Clip Model

Conceptual model:

    interface Clip {
      id: string;
      sourceId: string;
      trackId: string;

      timelineStart: number;
      sourceStart: number;
      sourceEnd: number;

      speed: number;

      volume?: number;
      opacity?: number;

      effects?: ClipEffect[];
      transform?: Transform;

      muted?: boolean;
      enabled?: boolean;
      reverse?: boolean;
    }

The model may evolve as the editor grows.

---

# 5. Source Media

A Clip references a source media asset.

Conceptually:

Media Asset
    │
    ├── Original File
    ├── Metadata
    ├── Duration
    ├── Resolution
    └── Codec Information
             ↓
            Clip

The same Media Asset may be referenced by multiple Clips.

---

# 6. Media Asset vs Clip

Media Asset:

    Original.mp4

Clip:

    Source:
    00:30 → 01:00

    Timeline:
    00:10 → 00:40

The Media Asset represents the actual media.

The Clip represents its usage inside the project.

---

# 7. Source Time

Source Time represents the position inside the original media.

Conceptually:

Source:

0s ───────────────────────── 120s
       ↑               ↑
   sourceStart      sourceEnd

Example:

    {
      "sourceStart": 30,
      "sourceEnd": 60
    }

---

# 8. Timeline Time

Timeline Time represents where the Clip appears in the project.

Conceptually:

Timeline:

0s ───────────────────────── 60s
          ↑           ↑
      timelineStart  timelineEnd

Example:

    {
      "timelineStart": 10
    }

---

# 9. Source Duration

    sourceDuration =
    sourceEnd - sourceStart

Example:

    sourceStart = 30
    sourceEnd   = 60

    duration = 30 seconds

---

# 10. Timeline Duration

Without speed changes:

    timelineDuration = sourceDuration

With playback speed:

    timelineDuration = sourceDuration / speed

Example:

    Source Duration = 20s
    Speed = 2x

    Timeline Duration = 10s

Timeline duration should preferably be derived rather than stored as an independent source of truth.

---

# 11. Clip Time Mapping

A Clip maps Source Time to Timeline Time.

Conceptually:

Source Time
    │
    ↓
Clip Mapping
    │
    ↓
Timeline Time

Example:

Source:
30s ───────────── 60s

Timeline:
10s ───────────── 40s

---

# 12. Clip Position

A Clip has a Timeline position.

    interface ClipPosition {
      start: number;
    }

The end position can be derived:

    timelineEnd =
    timelineStart + timelineDuration

---

# 13. Clip Speed

A Clip may have a playback speed.

Common values:

- 0.25x
- 0.5x
- 1x
- 1.25x
- 1.5x
- 2x
- 4x

Example:

    {
      "speed": 2
    }

Speed must be greater than zero.

---

# 14. Speed Mapping

Source:

0 ───────────── 20s

Speed:

2x

Timeline:

0 ─────── 10s

Speed changes must preserve synchronization between video and audio when both belong to the same source.

---

# 15. Reverse Playback

A Clip may support reverse playback.

    {
      "reverse": true
    }

Conceptually:

Normal:

A → B → C → D

Reverse:

D → C → B → A

Reverse playback is a rendering concern and must not modify the original media.

---

# 16. Trim

Trim changes the source range used by a Clip.

Before:

Source:
0 ───────────────────── 60

After:

Clip:
10 ───────────── 40

Example:

    {
      "sourceStart": 10,
      "sourceEnd": 40
    }

Trim must preserve the Timeline start position unless explicitly changed by another operation.

---

# 17. Split

A Clip can be split into multiple Clips.

Before:

[──────────── Clip A ────────────]

After:

[──── Clip A1 ────][──── Clip A2 ────]

The source media remains unchanged.

---

# 18. Split Rules

Given:

    Clip:
    0 → 20

    Split:
    8

Result:

    Clip A:
    0 → 8

    Clip B:
    8 → 20

Both Clips reference the same Media Asset.

Each resulting Clip receives a unique ID.

---

# 19. Move

Moving a Clip changes its Timeline position.

Before:

[ Clip ]
0s

After:

          [ Clip ]
          10s

The source range remains unchanged.

---

# 20. Copy

Copying a Clip creates a new Clip ID while preserving the source reference.

    Clip A
    sourceId = media-001

    Copy

    Clip B
    sourceId = media-001

The Clips can have independent Timeline positions and properties.

---

# 21. Duplicate

Duplicate is equivalent to creating a new Clip with:

- New ID
- Same source asset
- Same source range
- Same effects
- Same transformation
- Same playback properties
- New Timeline position

---

# 22. Clip Transformation

A Clip may contain visual transformations.

    interface Transform {
      x: number;
      y: number;
      scaleX: number;
      scaleY: number;
      rotation: number;
      anchorX: number;
      anchorY: number;
    }

Example:

    {
      "x": 100,
      "y": 50,
      "scaleX": 0.8,
      "scaleY": 0.8,
      "rotation": 15,
      "anchorX": 0.5,
      "anchorY": 0.5
    }

---

# 23. Clip Opacity

Video and overlay Clips may define opacity.

    {
      "opacity": 0.5
    }

Valid range:

    0.0 → 1.0

---

# 24. Clip Volume

Audio-enabled Clips may define volume.

    {
      "volume": 0.8
    }

The value must be validated before rendering.

The exact volume representation may later be replaced by decibel-based audio configuration.

---

# 25. Clip Effects

A Clip may contain effects.

Conceptually:

Clip
 │
 ├── Crop
 ├── Color
 ├── Blur
 └── Transform

Effects are represented as structured data.

They are converted to FFmpeg filters during rendering.

---

# 26. Clip Effect Model

    interface ClipEffect {
      id: string;
      type: string;
      enabled: boolean;
      order: number;
      parameters: Record<string, unknown>;
    }

---

# 27. Effect Ordering

Effects have an explicit order.

Clip
 ↓
Crop
 ↓
Scale
 ↓
Color
 ↓
Blur
 ↓
Output

Changing effect order may change the final result.

The Rendering Engine must preserve deterministic effect ordering.

---

# 28. Clip Audio

A video Clip may contain an audio stream.

Conceptually:

Clip
├── Video
└── Audio

The system must allow:

- Original Audio
- Muted Audio
- Detached Audio
- Replaced Audio

---

# 29. Audio Detachment

Audio may be detached from a Video Clip.

Before:

Video Clip
├── Video
└── Audio

After:

Video Clip
└── Video

Audio Clip
└── Audio

Both Clips may continue referencing the same Media Asset.

---

# 30. Clip Muting

A Clip may mute its own audio.

    {
      "muted": true
    }

This is non-destructive.

The source audio remains unchanged.

---

# 31. Clip Keyframes

Future versions may support keyframes.

Example:

0s       5s       10s

Opacity
1.0 ───── 0.5 ───── 1.0

Possible animated properties:

- Position
- Scale
- Rotation
- Opacity
- Volume
- Blur
- Color

Keyframes should remain compatible with the existing Clip property model.

---

# 32. Clip Markers

A Clip may contain local markers.

Example:

    {
      "time": 3.5,
      "label": "Important"
    }

Clip marker time is relative to the Clip.

---

# 33. Clip Metadata

Clip metadata may include:

- id
- sourceId
- trackId
- createdAt
- updatedAt

Metadata must remain separate from media-processing configuration.

---

# 34. Clip State

A Clip may contain:

    interface ClipState {
      enabled: boolean;
      muted: boolean;
      locked: boolean;
    }

UI-only selection state should remain outside persistent Clip data.

---

# 35. Clip Locking

A locked Clip cannot be modified by normal editing operations.

Possible restrictions:

- Move
- Trim
- Split
- Delete
- Transform
- Effects
- Speed

Locking is non-destructive.

---

# 36. Clip Enable / Disable

Disabled Clips remain in the Timeline but do not participate in normal preview or rendering.

Example:

    {
      "enabled": false
    }

---

# 37. Clip Collision

Clips may overlap depending on Track rules.

Conceptually:

Track

[──── Clip A ────]
       [──── Clip B ────]

The Track determines whether this is valid.

---

# 38. Clip Gap

A Clip may be moved away from adjacent Clips.

Conceptually:

[ Clip A ]

          [ Clip B ]

The gap is valid Timeline space.

---

# 39. Clip Source Validation

Before rendering, validate:

- sourceId
- sourceStart
- sourceEnd
- sourceDuration
- Media Availability
- Media Type

The Clip must never reference an invalid source range.

Validation rules include:

    sourceStart >= 0
    sourceEnd > sourceStart
    sourceEnd <= mediaDuration

---

# 40. Clip Source Resolution

The Rendering Engine resolves:

Clip
 ↓
sourceId
 ↓
Media Asset
 ↓
Physical Media File

The Clip should not store absolute filesystem paths.

---

# 41. Clip and Media Asset

Relationship:

Media Asset
     │
     ├── Clip A
     ├── Clip B
     └── Clip C

One Media Asset may be reused by many Clips.

---

# 42. Clip and Track

Relationship:

Timeline
   ↓
Track
   ↓
Clip
   ↓
Media Asset

A Clip belongs to exactly one Track at a time.

---

# 43. Clip and Timeline

The Timeline determines:

- timelineStart
- timelineEnd
- track

The Clip determines:

- sourceStart
- sourceEnd
- speed
- reverse
- effects
- transform
- audio properties

---

# 44. Clip Rendering

Rendering flow:

Clip
 ↓
Resolve Media
 ↓
Extract Source Range
 ↓
Apply Speed
 ↓
Apply Reverse
 ↓
Apply Transform
 ↓
Apply Effects
 ↓
Apply Audio Processing
 ↓
Track Composition

---

# 45. Clip → Filter Graph

A Clip may produce a filter graph.

Conceptually:

Source
  ↓
Trim
  ↓
SetPTS
  ↓
Speed
  ↓
Scale
  ↓
Transform
  ↓
Effects
  ↓
Output

The exact FFmpeg syntax belongs to the Video Engine.

---

# 46. Clip Render Representation

Conceptual model:

    interface ClipRenderPlan {
      clipId: string;
      sourceId: string;

      sourceStart: number;
      sourceEnd: number;

      timelineStart: number;

      speed: number;
      reverse: boolean;

      filters: RenderFilter[];

      video?: VideoRenderConfig;
      audio?: AudioRenderConfig;
    }

---

# 47. Clip Operations

Common Clip operations:

- Create Clip
- Delete Clip
- Move Clip
- Trim Clip
- Split Clip
- Duplicate Clip
- Resize Clip
- Change Speed
- Reverse Clip
- Mute Clip
- Detach Audio
- Add Effect
- Remove Effect
- Update Transform
- Update Volume
- Update Opacity
- Enable Clip
- Disable Clip
- Lock Clip
- Unlock Clip

---

# 48. Clip Operations and Undo

Every Clip modification should be represented as an operation.

Move Clip
    ↓
Operation
    ↓
Timeline State

Undo:

Undo Operation
    ↓
Previous Timeline State

---

# 49. AI and Clips

AI should operate on Clips through structured tools.

Example:

User:

Speed up the second clip to 2x.

AI:

    {
      "operation": "setClipSpeed",
      "clipId": "clip-002",
      "speed": 2
    }

The Editing Engine validates the operation.

---

# 50. AI Clip Discovery

AI tools may include:

- listClips()
- getClip()
- findClip()
- findClipsByTrack()
- findClipsByTime()
- findClipsBySource()

Example:

User
 ↓
"Remove the intro clip"
 ↓
AI
 ↓
Find Clip
 ↓
Generate Delete Operation
 ↓
Validation
 ↓
Apply

---

# 51. AI Clip Modification Rules

AI must not directly modify:

- Database records
- Media files
- FFmpeg commands
- Filesystem paths

AI must generate structured editing operations.

AI
 ↓
Tool
 ↓
Operation
 ↓
Validation
 ↓
Editing Engine
 ↓
Timeline

---

# 52. Clip Validation

Validate:

- Clip ID
- Source ID
- Track ID
- Source Range
- Timeline Position
- Speed
- Volume
- Opacity
- Transform
- Effects

Additional validation:

    speed > 0
    opacity >= 0
    opacity <= 1
    sourceStart >= 0
    sourceEnd > sourceStart
    timelineStart >= 0

Invalid Clips must not reach the Rendering Engine.

---

# 53. Clip Persistence

Clips are persisted as part of project state.

Conceptually:

Project
 ↓
Timeline
 ↓
Tracks
 ↓
Clips

Sequelize models should remain separate from domain entities.

---

# 54. Clip Repository

Conceptual interface:

    interface ClipRepository {
      create(clip: Clip): Promise<Clip>;
      findById(id: string): Promise<Clip | null>;
      update(clip: Clip): Promise<Clip>;
      delete(id: string): Promise<void>;
    }

---

# 55. Clip Transactions

Operations affecting multiple Clips should be transactional.

Example:

Split Clip
 ↓
Create Clip A
 ↓
Create Clip B
 ↓
Update Timeline
 ↓
Transaction

Partial state must not be persisted.

---

# 56. Clip Performance

Large projects may contain thousands of Clips.

The system should support:

- Efficient Clip lookup
- Indexed database queries
- Timeline virtualization
- Memoized UI components
- Incremental updates
- Lazy media metadata loading

---

# 57. Clip Security

Clip operations must pass:

Authentication
      ↓
Authorization
      ↓
Operation Validation
      ↓
Domain Validation
      ↓
Persistence

AI-generated Clip operations must follow the same security and authorization rules as user-generated operations.

---

# 58. Clip Versioning

Clip changes should be represented through Timeline operations.

Example:

Version 1
   ↓
Move Clip
   ↓
Version 2
   ↓
Trim Clip
   ↓
Version 3

The system should prefer operation history over duplicating complete project states whenever practical.

---

# 59. Non-Destructive Editing

Clip operations must not modify the original Media Asset.

Original Media
      │
      ├── Clip
      ├── Trim
      ├── Speed
      ├── Effects
      └── Transform
             ↓
        Render Process

The original source remains unchanged.

---

# 60. Clip and Rendering Boundary

The Clip domain model must remain independent from FFmpeg.

The Clip contains:

- Source
- Timing
- Position
- Speed
- Effects
- Transform
- Audio Properties

The Video Engine is responsible for converting these properties into:

- FFmpeg Inputs
- FFmpeg Filters
- FFmpeg Mapping
- FFmpeg Encoding
- FFmpeg Output

---

# 61. Clip and FFprobe

FFprobe is used to obtain source information such as:

- Duration
- Width
- Height
- Frame Rate
- Codec
- Pixel Format
- Audio Streams
- Video Streams
- Sample Rate
- Channels

This metadata is associated with the Media Asset rather than being duplicated unnecessarily inside every Clip.

---

# 62. Clip Media Compatibility

Before using a Media Asset in a Clip, the system should validate:

- Media Exists
- Media Is Supported
- Media Has Required Stream
- Source Range Is Valid
- Codec Is Supported

The Video Engine may normalize unsupported media formats.

---

# 63. Clip Lifecycle

The Clip lifecycle is:

Create
  ↓
Attach to Track
  ↓
Edit
  ↓
Transform
  ↓
Apply Effects
  ↓
Preview
  ↓
Validate
  ↓
Render
  ↓
Export

---

# 64. Clip Deletion

Deleting a Clip removes its Timeline representation.

It must not delete the underlying Media Asset unless the Media Asset has no remaining references and an explicit asset cleanup process determines that deletion is safe.

---

# 65. Clip References

A Media Asset may have multiple references:

Media Asset
   │
   ├── Clip A
   ├── Clip B
   ├── Clip C
   └── Clip D

Deleting one Clip must not affect the other Clips.

---

# 66. Clip Replacement

A Clip may replace its source Media Asset.

Before:

Clip
 ↓
Media A

After:

Clip
 ↓
Media B

The Timeline position and editing properties may remain unchanged unless the replacement media requires recalculation.

---

# 67. Clip Duration After Replacement

When replacing a source asset, validate:

- sourceStart
- sourceEnd

against the new Media Asset duration.

If the new media is shorter, the replacement operation must either:

- Reject the operation
- Automatically clamp the range
- Request user confirmation

The selected behavior must be deterministic.

---

# 68. Clip Preview

The frontend may generate preview representations from the Clip state.

Clip State
   ↓
Preview Request
   ↓
Preview Renderer
   ↓
Preview Frame / Segment

Preview rendering should not modify persistent project state.

---

# 69. Clip Proxy Media

For performance, the system may use proxy media.

Original Media
      ↓
Proxy Generator
      ↓
Proxy Media
      ↓
Preview

Final rendering must use the appropriate original-quality media unless the user explicitly chooses another output source.

---

# 70. Clip Thumbnails

A Clip may have generated thumbnails for UI display.

Thumbnail generation is an infrastructure concern.

The Clip should reference generated preview resources rather than embedding image data.

---

# 71. Clip Waveforms

Audio Clips may have waveform data.

Audio Clip
    ↓
Waveform Generator
    ↓
Waveform Data
    ↓
Timeline UI

Waveform data should be treated as derived data.

---

# 72. Clip Analysis

AI systems may analyze Clips for:

- Scenes
- Objects
- Faces
- Speech
- Silence
- Music
- Keywords
- Motion
- Transitions
- Important Moments

Analysis results should remain separate from the core Clip model.

---

# 73. AI Clip Metadata

AI analysis may produce:

    {
      "clipId": "clip-001",
      "analysis": {
        "scene": "office",
        "speech": true,
        "important": true
      }
    }

AI metadata should not directly alter the Clip unless an explicit editing operation is generated.

---

# 74. Clip Search

The system should eventually support semantic Clip search.

Examples:

- Find clips containing a person.
- Find clips with silence.
- Find clips containing an office scene.
- Find clips longer than 10 seconds.
- Find clips with speech.

Search results should return Clip IDs and relevant metadata.

---

# 75. Clip and Context Engineering

The AI Context Engine should expose only the information required for the current task.

Example:

Project Context
      ↓
Timeline Context
      ↓
Track Context
      ↓
Relevant Clip Context
      ↓
AI Agent

The AI should not receive unnecessary raw media data.

---

# 76. Clip Context Representation

AI-facing Clip context may look like:

    {
      "id": "clip-001",
      "trackId": "video-1",
      "sourceId": "media-001",
      "timeline": {
        "start": 10,
        "end": 25
      },
      "source": {
        "start": 30,
        "end": 45
      },
      "speed": 1,
      "enabled": true
    }

---

# 77. Clip Operation Schema

AI operations should use strict schemas.

Example:

    interface SetClipSpeedOperation {
      type: "setClipSpeed";
      clipId: string;
      speed: number;
    }

Another example:

    interface TrimClipOperation {
      type: "trimClip";
      clipId: string;
      sourceStart: number;
      sourceEnd: number;
    }

---

# 78. Operation Validation

Before execution:

AI Output
   ↓
Schema Validation
   ↓
Authorization
   ↓
Clip Validation
   ↓
Track Validation
   ↓
Timeline Validation
   ↓
Execute

Invalid operations must be rejected.

---

# 79. Clip Error Handling

Possible errors:

- CLIP_NOT_FOUND
- SOURCE_NOT_FOUND
- TRACK_NOT_FOUND
- INVALID_SOURCE_RANGE
- INVALID_TIMELINE_POSITION
- INVALID_SPEED
- INVALID_OPACITY
- INVALID_VOLUME
- CLIP_LOCKED
- TRACK_LOCKED
- UNSUPPORTED_MEDIA

Errors should use stable machine-readable codes.

---

# 80. Clip Concurrency

Future collaborative editing may result in multiple users modifying the same Clip.

The system should eventually support:

- Optimistic Concurrency
- Version Checking
- Conflict Detection
- Operation Ordering

These mechanisms are not required for the initial MVP.

---

# 81. Architectural Principles

The core principles are:

1. A Clip represents media usage inside a Timeline.

2. Media Assets represent source media.

3. Clips reference Media Assets.

4. Original media is immutable.

5. Source Time and Timeline Time are separate concepts.

6. Clip duration should be derived whenever possible.

7. Editing is non-destructive.

8. Clip changes are represented as structured operations.

9. Clips must remain independent from FFmpeg.

10. Rendering converts Clips into executable media-processing plans.

11. AI interacts with Clips through validated tools.

12. Clip persistence must be isolated from the domain model.

13. Clip operations must support undo and redo.

14. Clip design must support future keyframes and advanced effects.

15. Derived data such as thumbnails, waveforms, and AI analysis should not become the source of truth for the Clip.

16. Media Asset lifecycle must remain independent from Clip lifecycle.

---

# 82. Final Architecture

Media Asset
     │
     ↓
   Clip
     │
     ├───────────────┬────────────────┐
     ↓               ↓                ↓
Source Range   Timeline Position   Properties
                                     │
                          ┌──────────┼──────────┐
                          ↓          ↓          ↓
                        Speed     Effects    Transform
                                     │
                                     ↓
                                   Track
                                     ↓
                                  Timeline
                                     ↓
                             Editing Operations
                                     ↓
                              Render Planner
                                     ↓
                                Render Plan
                             ÷        ↓
                                Video Engine
                                     ↓
                                   FFmpeg
                                     ↓
                                  Output

A Clip is the fundamental domain object that connects source media with the editable Timeline while remaining independent from the underlying media-processing engine.