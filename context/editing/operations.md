# Editing Operations Context

## Purpose

This document defines the operation system used by the AI Video Editor.

An Operation represents an intentional change to the editing state.

Operations are the bridge between:

- User actions
- AI actions
- Timeline state
- Editing domain
- Persistence
- Undo / Redo
- Rendering

The system must treat editing operations as structured, validated, and deterministic commands.

---

# 1. What Is an Editing Operation?

An Editing Operation describes a change that should happen to the project.

Examples:

- Create Clip
- Delete Clip
- Move Clip
- Trim Clip
- Split Clip
- Duplicate Clip
- Change Speed
- Add Effect
- Remove Effect
- Transform Clip
- Mute Clip
- Detach Audio

Conceptually:

User / AI
    ↓
Operation
    ↓
Validation
    ↓
Domain Logic
    ↓
Timeline State
    ↓
Persistence
    ↓
Render

---

# 2. Operation Principles

Operations must be:

- Explicit
- Structured
- Validatable
- Deterministic
- Serializable
- Auditable
- Reversible when possible
- Independent from UI
- Independent from FFmpeg

An Operation must describe what should happen, not how FFmpeg should execute it.

---

# 3. Operation vs Command

An Operation is a domain-level instruction.

Example:

    {
      "type": "setClipSpeed",
      "clipId": "clip-001",
      "speed": 2
    }

The operation does not contain:

- FFmpeg commands
- Shell commands
- File paths
- Database queries

The domain layer decides how the operation changes the project.

---

# 4. Operation Structure

Base operation:

    interface Operation {
      id: string;
      type: string;
      projectId: string;
      createdAt: string;
      payload: Record<string, unknown>;
    }

Example:

    {
      "id": "op-001",
      "type": "setClipSpeed",
      "projectId": "project-001",
      "createdAt": "2026-08-15T10:00:00Z",
      "payload": {
        "clipId": "clip-001",
        "speed": 2
      }
    }

---

# 5. Operation Identity

Every operation must have a unique ID.

Example:

    op-001

The operation ID is used for:

- Tracking
- Logging
- Undo
- Redo
- Debugging
- Auditing
- Idempotency

---

# 6. Operation Types

Initial operation types:

    createClip
    deleteClip
    moveClip
    trimClip
    splitClip
    duplicateClip
    setClipSpeed
    reverseClip
    addEffect
    removeEffect
    updateTransform
    setVolume
    muteClip
    unmuteClip
    detachAudio
    replaceSource
    enableClip
    disableClip
    lockClip
    unlockClip

The operation registry may grow as the editor grows.

---

# 7. Create Clip

Creates a new Clip and attaches it to a Track.

Example:

    {
      "type": "createClip",
      "payload": {
        "clipId": "clip-001",
        "sourceId": "media-001",
        "trackId": "track-001",
        "timelineStart": 0,
        "sourceStart": 0,
        "sourceEnd": 20
      }
    }

Validation:

- Source must exist
- Track must exist
- Source range must be valid
- Clip ID must be unique
- Timeline position must be valid

---

# 8. Delete Clip

Removes a Clip from the Timeline.

Example:

    {
      "type": "deleteClip",
      "payload": {
        "clipId": "clip-001"
      }
    }

Deleting a Clip must not delete the underlying Media Asset.

---

# 9. Move Clip

Changes the Timeline position of a Clip.

Example:

    {
      "type": "moveClip",
      "payload": {
        "clipId": "clip-001",
        "timelineStart": 30
      }
    }

The source range remains unchanged.

---

# 10. Trim Clip

Changes the source range used by a Clip.

Example:

    {
      "type": "trimClip",
      "payload": {
        "clipId": "clip-001",
        "sourceStart": 10,
        "sourceEnd": 30
      }
    }

Validation:

    sourceStart >= 0
    sourceEnd > sourceStart
    sourceEnd <= mediaDuration

---

# 11. Split Clip

Splits one Clip into two Clips.

Example:

    {
      "type": "splitClip",
      "payload": {
        "clipId": "clip-001",
        "splitTime": 10
      }
    }

Before:

    [──────────── Clip A ────────────]

After:

    [──── Clip A1 ────][──── Clip A2 ────]

The source Media Asset remains unchanged.

---

# 12. Split Operation Rules

A split operation must:

1. Validate the Clip
2. Validate the split position
3. Calculate source ranges
4. Calculate Timeline ranges
5. Create new Clip IDs
6. Create the resulting Clips
7. Remove or replace the original Clip
8. Persist the change atomically

The operation must not leave a partially split state.

---

# 13. Duplicate Clip

Creates a new Clip based on an existing Clip.

Example:

    {
      "type": "duplicateClip",
      "payload": {
        "clipId": "clip-001",
        "timelineStart": 50
      }
    }

The duplicated Clip receives a new ID.

---

# 14. Set Clip Speed

Changes playback speed.

Example:

    {
      "type": "setClipSpeed",
      "payload": {
        "clipId": "clip-001",
        "speed": 2
      }
    }

Validation:

    speed > 0

Timeline duration must be recalculated.

---

# 15. Reverse Clip

Enables or disables reverse playback.

Example:

    {
      "type": "reverseClip",
      "payload": {
        "clipId": "clip-001",
        "reverse": true
      }
    }

Reverse playback must not modify the source Media Asset.

---

# 16. Add Effect

Adds an effect to a Clip.

Example:

    {
      "type": "addEffect",
      "payload": {
        "clipId": "clip-001",
        "effect": {
          "type": "blur",
          "parameters": {
            "strength": 5
          }
        }
      }
    }

Effects are domain data.

FFmpeg filters are generated later by the Video Engine.

---

# 17. Remove Effect

Removes an effect from a Clip.

Example:

    {
      "type": "removeEffect",
      "payload": {
        "clipId": "clip-001",
        "effectId": "effect-001"
      }
    }

The operation must validate that the effect exists.

---

# 18. Update Transform

Changes visual transformation properties.

Example:

    {
      "type": "updateTransform",
      "payload": {
        "clipId": "clip-001",
        "transform": {
          "x": 100,
          "y": 50,
          "scaleX": 1,
          "scaleY": 1,
          "rotation": 15
        }
      }
    }

---

# 19. Audio Operations

Initial audio operations:

    setVolume
    muteClip
    unmuteClip
    detachAudio

Example:

    {
      "type": "setVolume",
      "payload": {
        "clipId": "clip-001",
        "volume": 0.5
      }
    }

---

# 20. Mute Clip

Example:

    {
      "type": "muteClip",
      "payload": {
        "clipId": "clip-001"
      }
    }

Muting is non-destructive.

The original audio remains unchanged.

---

# 21. Unmute Clip

Example:

    {
      "type": "unmuteClip",
      "payload": {
        "clipId": "clip-001"
      }
    }

---

# 22. Detach Audio

Detaches the audio representation from a video Clip.

Example:

    {
      "type": "detachAudio",
      "payload": {
        "clipId": "clip-001"
      }
    }

The operation may create a new Audio Clip.

The original Media Asset remains unchanged.

---

# 23. Replace Source

A Clip may replace its source Media Asset.

Example:

    {
      "type": "replaceSource",
      "payload": {
        "clipId": "clip-001",
        "sourceId": "media-002"
      }
    }

The system must validate the new source range against the new Media Asset.

---

# 24. Enable Clip

Example:

    {
      "type": "enableClip",
      "payload": {
        "clipId": "clip-001"
      }
    }

---

# 25. Disable Clip

Example:

    {
      "type": "disableClip",
      "payload": {
        "clipId": "clip-001"
      }
    }

Disabled Clips remain part of the project but do not participate in normal rendering.

---

# 26. Lock Clip

Example:

    {
      "type": "lockClip",
      "payload": {
        "clipId": "clip-001"
      }
    }

A locked Clip cannot be modified by normal editing operations.

---

# 27. Unlock Clip

Example:

    {
      "type": "unlockClip",
      "payload": {
        "clipId": "clip-001"
      }
    }

---

# 28. Operation Validation

Every operation must pass validation before execution.

Flow:

    Operation
        ↓
    Schema Validation
        ↓
    Authentication
        ↓
    Authorization
        ↓
    Domain Validation
        ↓
    Execute

Invalid operations must be rejected before modifying project state.

---

# 29. Schema Validation

Operation payloads must have strict schemas.

Example:

    interface SetClipSpeedOperation {
      type: "setClipSpeed";
      clipId: string;
      speed: number;
    }

The schema should validate:

- Required fields
- Data types
- Allowed values
- Value ranges
- Nested objects

---

# 30. Domain Validation

Schema validation is not enough.

The domain must validate business rules.

Example:

    speed > 0

But also:

- Clip must exist
- Clip must not be locked
- Track must exist
- Project must exist
- Source must exist
- User must have access

---

# 31. Operation Authorization

Authorization must occur before execution.

Flow:

    User
      ↓
    Authentication
      ↓
    Project Access
      ↓
    Operation Permission
      ↓
    Execute Operation

AI-generated operations must use the same authorization system.

---

# 32. Operation Execution

The operation executor is responsible for applying validated operations.

Conceptual interface:

    interface OperationExecutor {
      execute(
        operation: Operation
      ): Promise<OperationResult>;
    }

---

# 33. Operation Result

Example:

    interface OperationResult {
      success: boolean;
      operationId: string;
      affectedEntities: string[];
      changes?: Record<string, unknown>;
      error?: OperationError;
    }

---

# 34. Operation Handler

Each operation type should have its own handler.

Conceptually:

    Operation
        ↓
    Operation Router
        ↓
    Handler
        ↓
    Domain Service
        ↓
    Repository

Example:

    setClipSpeed
        ↓
    SetClipSpeedHandler
        ↓
    ClipService
        ↓
    ClipRepository

---

# 35. Operation Registry

The system may use an operation registry.

Example:

    OperationRegistry

    createClip       → CreateClipHandler
    deleteClip       → DeleteClipHandler
    moveClip         → MoveClipHandler
    trimClip         → TrimClipHandler
    splitClip        → SplitClipHandler
    setClipSpeed     → SetClipSpeedHandler

This keeps operation routing centralized.

---

# 36. Operation Service

The Operation Service coordinates execution.

Responsibilities:

- Validate operation
- Authorize operation
- Find handler
- Execute operation
- Persist changes
- Record history
- Return result

The Operation Service should not contain FFmpeg logic.

---

# 37. Operation and Repository

Operations interact with domain repositories through services.

Conceptually:

    Operation
        ↓
    Handler
        ↓
    Domain Service
        ↓
    Repository
        ↓
    Database

Handlers should not directly execute raw SQL.

---

# 38. Operation Transactions

Operations that modify multiple entities should run inside transactions.

Example:

    Split Clip

    BEGIN TRANSACTION

    Create Clip A
    Create Clip B
    Delete Original Clip
    Update Timeline

    COMMIT

If any step fails:

    ROLLBACK

---

# 39. Atomicity

An operation must either:

- Complete successfully
- Produce no persistent changes

Partial editing state must not be committed.

---

# 40. Operation History

Executed operations should be recorded.

Example:

    Operation History

    001 createClip
    002 moveClip
    003 trimClip
    004 setClipSpeed
    005 addEffect

This history can support:

- Undo
- Redo
- Debugging
- Auditing
- Collaboration
- AI reasoning

---

# 41. Operation History Model

Conceptual model:

    interface OperationRecord {
      id: string;
      projectId: string;
      type: string;
      payload: Record<string, unknown>;
      userId?: string;
      createdAt: string;
    }

The exact persistence model may evolve.

---

# 42. Undo

Undo reverses the latest applicable operation.

Example:

    Operation 1
        ↓
    Move Clip
        ↓
    Timeline Changed
        ↓
    Undo
        ↓
    Previous Timeline State

Undo should preferably be implemented through an inverse operation.

---

# 43. Inverse Operation

Example:

Original:

    moveClip
    timelineStart = 30

Previous:

    timelineStart = 10

Inverse:

    moveClip
    timelineStart = 10

The inverse operation restores the previous state.

---

# 44. Undo Metadata

An operation may store the information required to reverse itself.

Example:

    {
      "type": "moveClip",
      "payload": {
        "clipId": "clip-001",
        "timelineStart": 30
      },
      "previousState": {
        "timelineStart": 10
      }
    }

Sensitive information should not be stored unnecessarily.

---

# 45. Redo

Redo reapplies an operation that has been undone.

Conceptually:

    Operation
        ↓
    Undo
        ↓
    Redo
        ↓
    Execute Again

Redo must respect the current project state.

---

# 46. Undo / Redo Stack

Conceptually:

    Undo Stack:

    [Operation A]
    [Operation B]
    [Operation C]

    Redo Stack:

    [Operation D]

After a new operation, the Redo stack should normally be cleared.

---

# 47. Operation Idempotency

Operations should define whether they are idempotent.

Example:

    enableClip

Executing it twice produces the same state.

Some operations are naturally idempotent.

Others, such as:

    duplicateClip

are not.

Non-idempotent operations require unique operation IDs and execution tracking.

---

# 48. Idempotency Key

The system may use:

    operationId

as an idempotency key.

Before executing an operation:

    Check operationId
        ↓
    Already executed?
        ↓
    Return previous result

Otherwise:

    Execute
        ↓
    Store result

---

# 49. Operation Retry

If an operation fails because of a temporary infrastructure problem, it may be retried.

Retryable errors may include:

- Database connection failure
- Temporary worker failure
- Temporary queue failure

Domain validation errors should not be retried automatically.

---

# 50. Operation Errors

Possible error codes:

    OPERATION_INVALID
    OPERATION_UNAUTHORIZED
    OPERATION_NOT_FOUND
    CLIP_NOT_FOUND
    TRACK_NOT_FOUND
    SOURCE_NOT_FOUND
    CLIP_LOCKED
    TRACK_LOCKED
    INVALID_SOURCE_RANGE
    INVALID_TIMELINE_POSITION
    INVALID_SPEED
    INVALID_VOLUME
    INVALID_EFFECT
    OPERATION_CONFLICT
    OPERATION_ALREADY_EXECUTED

Errors should be machine-readable.

---

# 51. Operation Error Structure

Example:

    interface OperationError {
      code: string;
      message: string;
      details?: Record<string, unknown>;
    }

The frontend may map error codes to user-friendly messages.

---

# 52. AI Generated Operations

The AI Context Engine should never directly modify the Timeline.

Instead:

    User
      ↓
    AI Agent
      ↓
    Tool
      ↓
    Structured Operation
      ↓
    Validation
      ↓
    Operation Service
      ↓
    Timeline

This provides a safe boundary between AI and the editing engine.

---

# 53. AI Operation Example

User:

    "Make the second clip twice as fast."

AI determines:

    clipId = clip-002

AI generates:

    {
      "type": "setClipSpeed",
      "payload": {
        "clipId": "clip-002",
        "speed": 2
      }
    }

The AI does not execute the operation directly.

---

# 54. AI Operation Safety

AI-generated operations must be treated as untrusted input.

The system must validate:

- Operation type
- Operation schema
- Entity existence
- User permissions
- Domain rules
- Resource limits

AI output must never bypass validation.

---

# 55. Operation Confirmation

Certain operations may require explicit user confirmation.

Examples:

- Delete project
- Delete many Clips
- Replace large amounts of media
- Remove important content
- Large-scale Timeline modifications

The system may classify operations by risk.

---

# 56. Operation Risk Levels

Possible levels:

    LOW
    MEDIUM
    HIGH
    CRITICAL

Example:

    setVolume       → LOW
    moveClip        → LOW
    trimClip        → LOW
    deleteClip      → MEDIUM
    bulkDelete      → HIGH
    deleteProject   → CRITICAL

---

# 57. Bulk Operations

The system should support operations affecting multiple entities.

Example:

    {
      "type": "bulkOperation",
      "operations": [
        {
          "type": "deleteClip",
          "payload": {
            "clipId": "clip-001"
          }
        },
        {
          "type": "deleteClip",
          "payload": {
            "clipId": "clip-002"
          }
        }
      ]
    }

Bulk operations must have deterministic execution order.

---

# 58. Bulk Operation Transactions

Bulk operations may execute inside a single transaction.

Example:

    BEGIN

    Operation A
    Operation B
    Operation C

    COMMIT

If atomic behavior is required and one operation fails:

    ROLLBACK

The exact behavior should be configurable by operation type.

---

# 59. Operation Ordering

Operations may depend on previous operations.

Example:

    createClip
        ↓
    moveClip
        ↓
    trimClip
        ↓
    setClipSpeed

An operation referencing a non-existing Clip must fail.

---

# 60. Operation Dependencies

Future versions may support explicit dependencies.

Example:

    Operation B
        requires
    Operation A

The system must ensure dependency ordering before execution.

---

# 61. Operation Concurrency

Multiple operations may arrive at the same time.

Example:

    User A
       ↓
    moveClip

    User B
       ↓
    trimClip

The system must detect conflicts when operations affect overlapping state.

---

# 62. Optimistic Concurrency

A project version may be attached to an operation.

Example:

    {
      "projectId": "project-001",
      "projectVersion": 12
    }

If the current version is 13:

    Operation Conflict

The client may refresh the project and retry.

---

# 63. Operation Versioning

Operation schemas may evolve.

Example:

    setClipSpeed:v1
    setClipSpeed:v2

The backend should be able to recognize operation versions when compatibility is required.

---

# 64. Operation Serialization

Operations must be serializable.

They may be stored or transmitted as JSON.

Example:

    {
      "id": "op-001",
      "type": "moveClip",
      "projectId": "project-001",
      "payload": {
        "clipId": "clip-001",
        "timelineStart": 20
      }
    }

---

# 65. Operation Transport

Operations may travel through:

    Frontend
        ↓
    HTTP API
        ↓
    Backend
        ↓
    Operation Service

For asynchronous rendering:

    Operation
        ↓
    Project State
        ↓
    Render Job
        ↓
    Queue
        ↓
    Worker

---

# 66. Operation and Events

An executed operation may produce domain events.

Example:

    moveClip
        ↓
    ClipMoved

    trimClip
        ↓
    ClipTrimmed

    deleteClip
        ↓
    ClipDeleted

Events are useful for:

- Notifications
- Analytics
- Rendering
- Collaboration
- Cache invalidation

---

# 67. Operation vs Event

Operation:

    "Move this Clip."

Event:

    "This Clip was moved."

Operation represents intent.

Event represents a fact.

---

# 68. Operation and Rendering

Operations modify project state.

Rendering reads project state.

Conceptually:

    Operation
        ↓
    Timeline State
        ↓
    Render Planner
        ↓
    Render Plan
        ↓
    FFmpeg

The operation system must not directly execute FFmpeg commands.

---

# 69. Operation and Preview

The frontend may apply operations optimistically for immediate UI feedback.

Example:

    User moves Clip
        ↓
    Frontend updates UI
        ↓
    Operation sent to Backend
        ↓
    Backend validates
        ↓
    Success
        ↓
    State confirmed

If the operation fails:

    UI Rollback

---

# 70. Optimistic UI

Optimistic updates should be used carefully.

Suitable operations:

- Move Clip
- Change Volume
- Change Opacity
- Change Speed

More dangerous operations may require server confirmation.

---

# 71. Operation and Persistence

The operation system should separate:

- Domain Operation
- Database Model
- API DTO

Conceptually:

    API DTO
       ↓
    Domain Operation
       ↓
    Operation Handler
       ↓
    Persistence Model

This prevents infrastructure details from leaking into the domain.

---

# 72. Sequelize Integration

The backend uses Sequelize.

Sequelize models should represent persistence structures.

The domain operation should not depend directly on Sequelize.

Example:

    Operation Domain
          ↓
    Repository Interface
          ↓
    Sequelize Repository
          ↓
    MySQL

This keeps the domain layer independent from the ORM.

---

# 73. Operation Repository

Conceptual interface:

    interface OperationRepository {
      save(operation: OperationRecord): Promise<void>;

      findById(
        id: string
      ): Promise<OperationRecord | null>;

      findByProjectId(
        projectId: string
      ): Promise<OperationRecord[]>;
    }

---

# 74. Operation Service Structure

Suggested backend structure:

    operation/
    ├── domain/
    │   ├── Operation.ts
    │   ├── OperationType.ts
    │   └── OperationError.ts
    │
    ├── application/
    │   ├── OperationService.ts
    │   ├── OperationExecutor.ts
    │   └── OperationRegistry.ts
    │
    ├── handlers/
    │   ├── CreateClipHandler.ts
    │   ├── DeleteClipHandler.ts
    │   ├── MoveClipHandler.ts
    │   ├── TrimClipHandler.ts
    │   └── SplitClipHandler.ts
    │
    └── infrastructure/
        ├── OperationModel.ts
        └── SequelizeOperationRepository.ts

The exact folder structure may evolve as the project grows.

---

# 75. Operation Handler Responsibility

A Handler should:

- Validate operation-specific rules
- Call the appropriate domain service
- Return the result
- Avoid direct HTTP logic
- Avoid FFmpeg logic
- Avoid AI reasoning

Example:

    SetClipSpeedHandler
        ↓
    ClipService.setSpeed()
        ↓
    ClipRepository.update()

---

# 76. Operation Service Responsibility

The Operation Service should:

- Receive operation
- Validate schema
- Authenticate user
- Authorize project access
- Find handler
- Execute handler
- Manage transaction
- Record operation
- Return result

---

# 77. Operation Domain Boundary

The Operation system belongs to the editing domain.

It should not depend directly on:

- React
- Express request objects
- Sequelize models
- FFmpeg
- FFprobe
- OpenAI APIs
- OpenRouter
- File system implementation

Dependencies should point inward toward domain logic.

---

# 78. Operation Flow

Complete flow:

    User
      ↓
    React Frontend
      ↓
    API Request
      ↓
    Express Controller
      ↓
    DTO Validation
      ↓
    Operation Service
      ↓
    Authorization
      ↓
    Operation Handler
      ↓
    Domain Service
      ↓
    Repository
      ↓
    Sequelize
      ↓
    MySQL

---

# 79. AI Operation Flow

AI flow:

    User
      ↓
    AI Context Engine
      ↓
    Agent
      ↓
    Tool
      ↓
    Structured Operation
      ↓
    Backend API
      ↓
    Validation
      ↓
    Authorization
      ↓
    Operation Handler
      ↓
    Domain
      ↓
    Timeline

---

# 80. Operation to Render Flow

    User / AI
        ↓
    Operation
        ↓
    Validation
        ↓
    Timeline State
        ↓
    Render Request
        ↓
    Render Planner
        ↓
    Clip Render Plans
        ↓
    Track Render Plans
        ↓
    Timeline Composition
        ↓
    FFmpeg
        ↓
    Output Video

---

# 81. Operation Logging

Important operation events should be logged.

Log information may include:

- operationId
- projectId
- userId
- operationType
- executionTime
- success
- errorCode

Logs must not contain sensitive information unnecessarily.

---

# 82. Operation Metrics

Useful metrics:

- Operations per second
- Operation execution time
- Failed operations
- Validation failures
- Conflict rate
- Retry rate
- Undo rate
- AI-generated operation success rate

These metrics help identify editing engine problems.

---

# 83. Operation Testing

Every operation should have tests for:

- Valid input
- Invalid input
- Missing entity
- Unauthorized access
- Locked entity
- Boundary values
- Transaction failure
- Concurrent modification
- Undo
- Redo

Example:

    setClipSpeed

    should accept 2x

    should reject 0x

    should reject negative speed

    should reject missing Clip

---

# 84. Operation Unit Tests

Domain operation logic should be tested independently of:

- Express
- Sequelize
- MySQL
- FFmpeg
- React

This makes the editing engine easier to maintain.

---

# 85. Operation Integration Tests

Integration tests should verify:

    API
      ↓
    Operation Service
      ↓
    Sequelize
      ↓
    Database

Example:

    POST /projects/:projectId/operations

The test verifies that the project state changes correctly.

---

# 86. Operation E2E Tests

End-to-end tests may verify:

    User
      ↓
    Frontend
      ↓
    API
      ↓
    Operation
      ↓
    Database
      ↓
    Updated Timeline

These tests validate the complete editing flow.

---

# 87. Deterministic Operations

The same operation applied to the same valid state should produce the same result.

Example:

    Initial:
    Clip at 0s

    Operation:
    Move Clip to 10s

    Result:
    Clip at 10s

Determinism is important for:

- Undo
- Redo
- Collaboration
- Debugging
- AI
- Reproducible rendering

---

# 88. Non-Destructive Operations

Operations should modify project metadata rather than source media.

Example:

    Trim Clip
        ↓
    Update sourceStart/sourceEnd

Not:

    Trim Clip
        ↓
    Modify original.mp4

---

# 89. Operation Security

Never allow operation payloads to contain executable commands.

Forbidden concepts inside operation payloads:

- Shell commands
- Arbitrary SQL
- Arbitrary filesystem paths
- Arbitrary FFmpeg commands
- JavaScript code

Operations must use predefined schemas.

---

# 90. Operation Resource Limits

The system should protect against abusive operations.

Possible limits:

- Maximum Clips per bulk operation
- Maximum effects per Clip
- Maximum operation payload size
- Maximum Timeline duration
- Maximum project complexity

These limits should be configurable.

---

# 91. AI Bulk Operations

AI may eventually generate multiple operations.

Example:

    User:
    "Remove all silent clips."

AI:

    Find Clips
        ↓
    Analyze Clips
        ↓
    Generate Operations
        ↓
    Validate
        ↓
    Preview Changes
        ↓
    User Confirmation
        ↓
    Execute

Large AI-generated modifications should preferably support preview and confirmation.

---

# 92. Operation Preview

Before applying high-risk AI operations, the system may provide:

    Operation Preview

    DELETE clip-001
    DELETE clip-004
    MOVE clip-005 → 20s
    SET SPEED clip-006 → 2x

User:

    Confirm

Then:

    Execute Operations

---

# 93. Operation Rollback

If a transaction fails:

    Operation
        ↓
    Partial execution
        ↓
    Error
        ↓
    Rollback

The project must return to its previous valid state.

---

# 94. Operation Snapshot

For complex operations, the system may capture a lightweight state snapshot before execution.

Example:

    Before Operation
        ↓
    Snapshot
        ↓
    Execute
        ↓
    Result

Snapshots should be used selectively because large project snapshots can be expensive.

---

# 95. Operation and Project Version

Every successful operation may increment the project version.

Example:

    Project Version 10
          ↓
    Move Clip
          ↓
    Project Version 11

This helps with:

- Concurrency
- Caching
- Synchronization
- Collaboration

---

# 96. Operation and Collaboration

Future collaborative editing may use:

    User A
       ↓
    Operation A

    User B
       ↓
    Operation B

Operations can be ordered and synchronized through a central project state.

More advanced collaboration may later introduce:

- Operational Transformation
- CRDT
- Conflict Resolution

These are not required for the initial MVP.

---

# 97. Operation and Context Engineering

The Context Engine should understand operations as executable capabilities.

Context may describe:

    Available Operations

    createClip
    deleteClip
    moveClip
    trimClip
    splitClip
    setClipSpeed
    addEffect
    removeEffect
    updateTransform

The AI should only generate operations that are available to it.

---

# 98. Operation Tool Schema

Each AI tool should map to an operation.

Example:

    Tool:
    set_clip_speed

    Input:

    {
      "clipId": "clip-001",
      "speed": 2
    }

    Internal Operation:

    {
      "type": "setClipSpeed",
      "payload": {
        "clipId": "clip-001",
        "speed": 2
      }
    }

This separates AI tool design from internal domain representation.

---

# 99. Operation Execution Safety

The execution pipeline must be:

    AI Output
        ↓
    Parse
        ↓
    Schema Validation
        ↓
    Permission Validation
        ↓
    Domain Validation
        ↓
    Transaction
        ↓
    Execute
        ↓
    Persist
        ↓
    Record History
        ↓
    Return Result

No step should be skipped.

---

# 100. Final Architecture

The Editing Operation architecture is:

    User
      │
      ├───────────────┐
      │               │
      ↓               ↓
    Frontend         AI Agent
      │               │
      └───────┬───────┘
              ↓
        Structured Operation
              ↓
       Schema Validation
              ↓
        Authorization
              ↓
        Operation Service
              ↓
       Operation Registry
              ↓
         Operation Handler
              ↓
          Domain Service
              ↓
          Domain Model
              ↓
          Repository
              ↓
           Sequelize
              ↓
             MySQL
              │
              ↓
        Updated Project State
              │
              ├───────────────┐
              ↓               ↓
        Operation History   Render Planner
                                  ↓
                              Render Plan
                                  ↓
                               FFmpeg

The Editing Operation system is the central command layer of the editor.

It provides a safe and deterministic boundary between the frontend, AI Context Engine, backend domain, persistence layer, and rendering system.