# AI Schemas Context

## Purpose

This document defines the schemas used by the AI Context Engine.

Schemas provide structured contracts between:

- AI Models
- Agents
- Context Engine
- Tools
- Workflows
- Backend Services
- Editing Engine

The AI must never be trusted as the source of truth.

All AI-generated data must be validated against schemas before execution.

---

# 1. Schema Architecture

The general flow is:

    User Request
        ↓
    AI Agent
        ↓
    Structured Output
        ↓
    Schema Validation
        ↓
    Business Validation
        ↓
    Tool Execution
        ↓
    Application State

Schema validation is the first validation layer.

Business validation remains responsible for enforcing domain rules.

---

# 2. Schema Principles

Schemas must be:

- Explicit
- Strict
- Versioned
- Predictable
- Machine-readable
- Validatable
- Backward-compatible when possible

Avoid accepting arbitrary AI-generated structures.

---

# 3. Schema Layers

Schemas are divided into:

    Context Schemas
    Agent Schemas
    Tool Schemas
    Editing Schemas
    Workflow Schemas
    Response Schemas
    Error Schemas

Each schema has a specific responsibility.

---

# 4. Context Schema

Context represents information provided to the AI.

Example:

    Project Context
    Timeline Context
    Media Context
    Selection Context
    Workflow Context

Conceptual structure:

    {
      "project": {},
      "timeline": {},
      "selection": {},
      "media": {},
      "workflow": {}
    }

Only relevant context should be included.

---

# 5. Project Context

Example:

    {
      "projectId": "project-001",
      "name": "My Video",
      "version": 12,
      "duration": 120
    }

Important fields:

    projectId
    version
    duration

The version is required for optimistic concurrency control.

---

# 6. Timeline Context

Example:

    {
      "timelineId": "timeline-001",
      "duration": 120,
      "tracks": []
    }

Timeline context may contain:

    tracks
    clips
    transitions
    effects

Large timelines should be summarized instead of sending the complete structure to the model.

---

# 7. Selection Context

The frontend may provide the current selection.

Example:

    {
      "type": "clip",
      "id": "clip-42"
    }

Possible types:

    project
    timeline
    track
    clip
    media
    effect
    subtitle

---

# 8. Media Context

Example:

    {
      "mediaId": "media-001",
      "filename": "video.mp4",
      "duration": 120,
      "width": 1920,
      "height": 1080,
      "fps": 30,
      "videoCodec": "h264",
      "audioCodec": "aac"
    }

Metadata should normally originate from FFprobe and backend services.

---

# 9. AI Action Schema

An AI Action represents an operation requested by the model.

Conceptual:

    {
      "type": "tool_call",
      "tool": "trim_clip",
      "arguments": {}
    }

Required fields:

    type
    tool
    arguments

---

# 10. AI Action Types

Possible action types:

    tool_call
    response
    clarification
    plan

Example:

    {
      "type": "clarification",
      "message": "Which clip should I trim?"
    }

---

# 11. Tool Call Schema

Example:

    {
      "type": "tool_call",
      "tool": "trim_clip",
      "arguments": {
        "clipId": "clip-42",
        "start": 10,
        "end": 40
      }
    }

The arguments must be validated before execution.

---

# 12. Tool Result Schema

All Tools should return predictable structures.

Success:

    {
      "success": true,
      "data": {}
    }

Failure:

    {
      "success": false,
      "error": {}
    }

---

# 13. Tool Error Schema

Example:

    {
      "success": false,
      "error": {
        "code": "CLIP_NOT_FOUND",
        "message": "The requested clip does not exist."
      }
    }

Errors must use stable error codes.

---

# 14. Error Codes

Examples:

    INVALID_ARGUMENT
    UNAUTHORIZED
    FORBIDDEN
    PROJECT_NOT_FOUND
    TIMELINE_NOT_FOUND
    TRACK_NOT_FOUND
    CLIP_NOT_FOUND
    MEDIA_NOT_FOUND
    VERSION_CONFLICT
    TOOL_UNAVAILABLE
    JOB_NOT_FOUND
    RENDER_FAILED
    INTERNAL_ERROR

---

# 15. Editing Operation Schema

Editing operations should use explicit types.

Example:

    {
      "operation": "trim",
      "clipId": "clip-42",
      "parameters": {
        "start": 10,
        "end": 30
      }
    }

Possible operations:

    trim
    split
    move
    delete
    duplicate
    resize
    crop
    rotate
    change_speed
    change_volume
    add_transition
    add_effect

---

# 16. Timeline Position Schema

Timeline positions should use seconds.

Example:

    {
      "time": 43.5
    }

Use numeric values.

Avoid:

    "00:00:43.500"

inside internal domain operations unless explicitly required by a presentation layer.

---

# 17. Time Range Schema

Example:

    {
      "start": 10.5,
      "end": 35.75
    }

Rules:

    start >= 0
    end > start

Additional validation must ensure that the range is valid for the selected media or clip.

---

# 18. Clip Schema

Conceptual:

    {
      "id": "clip-42",
      "mediaId": "media-001",
      "trackId": "track-01",
      "sourceStart": 10,
      "sourceEnd": 40,
      "timelineStart": 20,
      "duration": 30
    }

---

# 19. Track Schema

Example:

    {
      "id": "track-01",
      "type": "video",
      "index": 0
    }

Possible track types:

    video
    audio
    subtitle
    overlay

---

# 20. Media Schema

Example:

    {
      "id": "media-001",
      "type": "video",
      "path": "...",
      "duration": 120,
      "metadata": {}
    }

The actual filesystem path should not normally be exposed to the AI.

---

# 21. Rendering Schema

Render requests should be structured.

Example:

    {
      "type": "render",
      "projectId": "project-001",
      "format": "mp4",
      "resolution": {
        "width": 1920,
        "height": 1080
      },
      "fps": 30
    }

---

# 22. Render Job Schema

Rendering is asynchronous.

Example:

    {
      "jobId": "job-001",
      "type": "render",
      "status": "queued"
    }

Possible statuses:

    queued
    processing
    completed
    failed
    cancelled

---

# 23. Workflow Schema

Complex AI tasks require workflow state.

Example:

    {
      "workflowId": "workflow-001",
      "type": "video_editing",
      "status": "running",
      "currentStep": "remove_silence"
    }

---

# 24. Workflow Step Schema

Example:

    {
      "id": "step-01",
      "type": "tool_call",
      "tool": "analyze_audio",
      "status": "completed"
    }

Possible statuses:

    pending
    running
    completed
    failed
    skipped

---

# 25. Agent Plan Schema

For complex tasks:

    {
      "type": "plan",
      "steps": [
        {
          "id": "step-01",
          "action": "analyze_media"
        },
        {
          "id": "step-02",
          "action": "remove_silence"
        },
        {
          "id": "step-03",
          "action": "render"
        }
      ]
    }

Plans must be validated before execution.

---

# 26. Agent Response Schema

Example:

    {
      "type": "response",
      "message": "The clip was trimmed successfully."
    }

Responses should not claim success unless the Tool execution succeeded.

---

# 27. Clarification Schema

Example:

    {
      "type": "clarification",
      "message": "Which track should be modified?"
    }

Clarification is appropriate when required information cannot be safely inferred.

---

# 28. Suggestion Schema

Example:

    {
      "type": "suggestion",
      "actions": [
        {
          "type": "tool_call",
          "tool": "trim_clip"
        }
      ]
    }

Suggestions must not modify project state.

---

# 29. Confirmation Schema

High-risk operations may require confirmation.

Example:

    {
      "type": "confirmation_required",
      "action": "delete_clip",
      "target": "clip-42",
      "message": "This will permanently remove the clip."
    }

---

# 30. Validation Layers

AI output passes through:

    AI Output
        ↓
    Schema Validation
        ↓
    Authorization
        ↓
    Domain Validation
        ↓
    State Validation
        ↓
    Tool Execution

All layers are required.

---

# 31. Schema Validation

Schema validation checks:

    Required fields
    Field types
    Allowed values
    Nested structures
    Array structures
    Basic constraints

Example:

    start: number
    end: number

---

# 32. Domain Validation

Domain validation checks application rules.

Example:

    start >= 0
    end > start
    clip exists
    track exists
    timeline position is valid

Schema validation alone is not sufficient.

---

# 33. Authorization Validation

Before executing an AI-generated operation:

    User
      ↓
    Permission Check
      ↓
    Tool
      ↓
    Operation

AI must never bypass authorization.

---

# 34. Version Validation

Write operations should include project version when required.

Example:

    {
      "projectId": "project-001",
      "version": 12,
      "operation": "trim"
    }

If current version is 13:

    VERSION_CONFLICT

The Agent should refresh context before retrying.

---

# 35. Schema Versioning

Schemas should be versioned.

Example:

    ToolCall v1
    ToolCall v2

Breaking changes require a new major version.

---

# 36. Backward Compatibility

When possible:

    v2

should accept valid:

    v1

structures.

Compatibility should be handled intentionally rather than assumed.

---

# 37. JSON Schema

The project should eventually use JSON Schema or an equivalent validation library.

Possible stack:

    TypeScript
    Zod
    JSON Schema

Since the backend uses Node.js and Express, schemas should be reusable across API, AI, and Tool layers.

---

# 38. Zod Strategy

For the backend, Zod can define runtime schemas.

Conceptual:

    const TrimClipSchema = z.object({
      clipId: z.string(),
      start: z.number().min(0),
      end: z.number().positive()
    });

The schema can validate AI Tool arguments before execution.

---

# 39. Schema Reuse

The same schema should ideally be reused for:

    AI Tool validation
    API validation
    Service validation

However, API DTOs and domain models should remain conceptually separate.

---

# 40. AI Output Validation

Never execute raw AI output.

Incorrect:

    modelResponse
        ↓
    executeTool()

Correct:

    modelResponse
        ↓
    parse
        ↓
    validate
        ↓
    authorize
        ↓
    executeTool()

---

# 41. Invalid AI Output

Example:

    {
      "tool": "trim_clip",
      "arguments": {
        "clipId": 123
      }
    }

Expected:

    clipId = string

Result:

    Schema Validation Error

The operation must not execute.

---

# 42. Unknown Tool

Example:

    {
      "tool": "delete_everything"
    }

If the Tool does not exist:

    TOOL_NOT_FOUND

The request must be rejected.

---

# 43. Unknown Fields

Strict schemas should reject unexpected fields when appropriate.

Example:

    {
      "clipId": "clip-42",
      "start": 10,
      "deleteDatabase": true
    }

The unexpected field should not be trusted.

---

# 44. Enum Validation

Example:

    trackType:

        video
        audio
        subtitle
        overlay

Invalid:

    "database"

The schema must reject it.

---

# 45. Numeric Validation

Important numeric values must have limits.

Example:

    volume:

        min = 0
        max = 2

    speed:

        min = 0.1
        max = 10

Actual limits are defined by domain rules.

---

# 46. String Validation

IDs should have predictable formats.

Example:

    projectId
    timelineId
    trackId
    clipId
    mediaId
    jobId

The application should validate identifier formats.

---

# 47. Array Validation

Operations involving multiple clips should validate arrays.

Example:

    {
      "clipIds": [
        "clip-01",
        "clip-02"
      ]
    }

The schema may enforce:

    minItems
    maxItems
    uniqueItems

---

# 48. Batch Operations

Batch operations require additional safeguards.

Example:

    delete_clips

must validate:

    number of clips
    authorization
    project ownership
    current state

Large operations may require confirmation.

---

# 49. Context Schema Size

Avoid sending the entire database schema to the model.

Use AI-specific representations.

Example:

    Database:
    50 tables

    AI Context:
    Project
    Timeline
    Tracks
    Clips
    Media

---

# 50. Context Summaries

Large entities should have summaries.

Example:

    TimelineSummary:

    {
      "duration": 120,
      "trackCount": 4,
      "clipCount": 38
    }

Detailed data should only be loaded when needed.

---

# 51. Schema for Context References

Instead of embedding large data:

    {
      "clipId": "clip-42"
    }

The Agent can request:

    get_clip

This keeps context small.

---

# 52. Schema for Tool Definitions

A Tool should expose:

    name
    description
    inputSchema
    outputSchema
    permissions
    riskLevel

Example:

    {
      "name": "trim_clip",
      "riskLevel": "medium",
      "inputSchema": {},
      "outputSchema": {}
    }

---

# 53. Tool Risk Levels

Possible levels:

    low
    medium
    high
    critical

Example:

    get_clip:
    low

    trim_clip:
    medium

    delete_project:
    critical

---

# 54. Schema and Security

Schemas must never replace authorization.

Valid schema:

    delete_project({
      projectId: "project-1"
    })

does not mean:

    user is allowed to delete project

Authorization must still be checked.

---

# 55. Schema and File Operations

AI should not receive arbitrary filesystem paths.

Prefer:

    mediaId

instead of:

    /home/user/videos/project/input.mp4

The backend resolves media IDs to internal resources.

---

# 56. Schema and FFmpeg

AI should not receive raw FFmpeg command schemas.

Preferred:

    RenderRequest

instead of:

    FFmpegCommand

The Rendering Service translates domain requests into FFmpeg operations.

---

# 57. Schema and FFprobe

FFprobe output should be transformed into normalized application metadata.

Raw FFprobe:

    large technical output

Normalized:

    MediaMetadata

The AI receives the normalized schema.

---

# 58. Schema and AI Context Engine

The Context Engine should build:

    ContextSchema

from:

    Project
    Timeline
    Media
    Selection
    Workflow

Then provide only relevant fields to the Agent.

---

# 59. Schema and Model Output

The AI Model should return structured data whenever possible.

Example:

    {
      "type": "tool_call",
      "tool": "split_clip",
      "arguments": {
        "clipId": "clip-42",
        "time": 32.5
      }
    }

The application validates the structure.

---

# 60. Schema and Multiple Providers

Schemas should remain independent of:

    OpenAI
    Anthropic
    Google
    OpenRouter
    Local Models

Provider adapters translate model-specific outputs into internal schemas.

---

# 61. Provider Adapter

Architecture:

    Model Provider
        ↓
    Provider Adapter
        ↓
    Internal AI Schema
        ↓
    Validation
        ↓
    Agent Runtime

This keeps the core system provider-independent.

---

# 62. Schema Registry

The system may eventually have:

    Schema Registry

Responsibilities:

    Register schemas
    Retrieve schemas
    Validate payloads
    Track versions

Example:

    ToolCallSchema v1
    ToolCallSchema v2
    RenderRequest v1

---

# 63. Schema Naming

Use descriptive names.

Good:

    TrimClipInput
    TrimClipOutput
    RenderRequest
    RenderJob
    TimelineContext
    AgentAction

Avoid:

    Data
    Input
    Result
    Object

---

# 64. Schema Organization

Future application structure:

    src/
    └── ai/
        └── schemas/
            ├── context/
            ├── agents/
            ├── tools/
            ├── editing/
            ├── workflows/
            └── common/

---

# 65. Common Schemas

Common schemas may include:

    ID
    Timestamp
    Version
    Pagination
    Error
    JobStatus

These can be shared across the application.

---

# 66. ID Schema

Conceptual:

    {
      "id": "clip-42"
    }

Different entity IDs should remain distinguishable.

---

# 67. Timestamp Schema

Internal timestamps should use a consistent format.

Recommended:

    ISO 8601

Example:

    2026-08-16T12:00:00Z

Video timeline positions remain numeric seconds.

---

# 68. Duration Schema

Video durations should use:

    seconds: number

Example:

    43.52

Do not mix duration representation formats inside domain logic.

---

# 69. Resolution Schema

Example:

    {
      "width": 1920,
      "height": 1080
    }

---

# 70. Frame Rate Schema

Example:

    {
      "fps": 29.97
    }

Fractional frame rates must be supported.

---

# 71. Audio Schema

Example:

    {
      "sampleRate": 48000,
      "channels": 2
    }

---

# 72. Codec Schema

Example:

    {
      "videoCodec": "h264",
      "audioCodec": "aac"
    }

The application should normalize codec identifiers where necessary.

---

# 73. Render Format Schema

Example:

    {
      "container": "mp4",
      "videoCodec": "h264",
      "audioCodec": "aac"
    }

Rendering services decide how this maps to FFmpeg configuration.

---

# 74. Schema Testing

Every important schema should have tests.

Test:

    valid input
    missing field
    invalid type
    invalid enum
    invalid range
    unknown field
    malformed nested object

---

# 75. Schema Test Example

Test case:

    TrimClipInput

Valid:

    {
      clipId: "clip-42",
      start: 10,
      end: 30
    }

Invalid:

    {
      clipId: "clip-42",
      start: 30,
      end: 10
    }

Expected:

    ValidationError

---

# 76. Schema Evolution

When domain requirements change:

    Old Schema
        ↓
    Migration
        ↓
    New Schema

Do not silently change meaning of existing fields.

---

# 77. Schema Documentation

Each schema should document:

    Purpose
    Fields
    Types
    Constraints
    Example
    Version
    Dependencies

---

# 78. Schema Security Rules

Never trust:

    AI-generated IDs
    AI-generated paths
    AI-generated permissions
    AI-generated ownership
    AI-generated SQL
    AI-generated shell commands

The application must determine these values.

---

# 79. Schema Reliability

The schema layer provides:

    Structure

The domain layer provides:

    Correctness

The authorization layer provides:

    Permission

The execution layer provides:

    Action

All four layers are required.

---

# 80. Complete AI Execution Pipeline

    User Request
        ↓
    Context Engine
        ↓
    AI Agent
        ↓
    Structured Output
        ↓
    Schema Validation
        ↓
    Authorization
        ↓
    Domain Validation
        ↓
    State Validation
        ↓
    Tool Execution
        ↓
    Service Layer
        ↓
    Database / Worker / FFmpeg
        ↓
    Result
        ↓
    Updated Context
        ↓
    AI Response

---

# 81. Final Principles

1. Never execute raw AI output.
2. Validate every AI action.
3. Keep schemas explicit.
4. Keep schemas versioned.
5. Separate schema validation from domain validation.
6. Keep authorization outside AI.
7. Never expose arbitrary filesystem paths.
8. Never trust AI-generated IDs.
9. Use structured Tool calls.
10. Normalize FFprobe metadata.
11. Hide FFmpeg implementation details behind services.
12. Keep provider-specific formats outside the domain.
13. Use strict schemas for critical operations.
14. Test schemas independently.
15. Keep Context schemas lightweight.
16. Use references instead of unnecessarily large objects.
17. Support version-aware editing operations.
18. Treat schema changes as architectural changes.
19. Keep AI output deterministic where possible.
20. Make the schema layer the contract between AI reasoning and application execution.