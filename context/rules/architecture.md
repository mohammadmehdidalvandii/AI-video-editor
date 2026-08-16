# Architecture Rules

## Purpose

This document defines the architectural rules for the AI Video Editor.

The goal is to keep the system:

- Modular
- Scalable
- Maintainable
- Testable
- Secure
- Extensible
- AI-ready

---

# 1. Core Architecture

The project follows a combination of architectural patterns:

    Modular Monolith
    Layered Architecture
    Domain-Based Architecture
    Event-Driven Architecture
    Asynchronous Architecture
    Queue-Based Architecture
    Worker-Based Architecture
    Stateless API Architecture

These patterns are combined rather than used independently.

---

# 2. Modular Monolith

The initial application should remain a modular monolith.

Major modules may include:

    Project
    Media
    Timeline
    Editing
    Rendering
    AI
    Workflow
    User
    Storage

Modules should have clear boundaries.

---

# 3. Avoid Premature Microservices

Do not split the application into microservices without a measurable reason.

The initial architecture should prefer:

    Modular Monolith
        +
    Background Workers
        +
    Queue

Microservices may be introduced later when scale or organizational requirements justify them.

---

# 4. Module Independence

Each module should have a clear responsibility.

Example:

    Media Module

is responsible for:

    Upload
    Metadata
    Media lifecycle

It should not contain:

    Authentication
    Timeline editing logic
    AI orchestration

---

# 5. Domain Boundaries

Important business concepts should remain separated.

Examples:

    Project
    Timeline
    Track
    Clip
    Media
    Workflow
    RenderJob

Do not create one giant service responsible for all domains.

---

# 6. Layered Architecture

Backend modules should generally follow:

    API
      ↓
    Application
      ↓
    Domain
      ↓
    Infrastructure

---

# 7. API Layer

Responsibilities:

    HTTP
    Routing
    Request parsing
    Request validation
    Authentication
    Response formatting

The API layer should not contain complex business logic.

---

# 8. Application Layer

Responsibilities:

    Use cases
    Orchestration
    Workflow coordination
    Transaction boundaries
    Service coordination

Example:

    CreateProject
    TrimClip
    StartRender
    ExecuteWorkflow

---

# 9. Domain Layer

Responsibilities:

    Business rules
    Domain entities
    Domain validation
    Domain operations

The domain layer should remain independent from HTTP.

---

# 10. Infrastructure Layer

Responsibilities:

    Sequelize
    MySQL
    Redis
    BullMQ
    FFmpeg
    FFprobe
    Object Storage
    AI Providers

Infrastructure implements technical details required by the application.

---

# 11. Dependency Direction

Dependencies should generally flow inward.

Preferred:

    API
      ↓
    Application
      ↓
    Domain

Infrastructure should implement interfaces required by the application.

Avoid allowing infrastructure details to leak throughout the domain.

---

# 12. Controllers

Controllers must remain thin.

Preferred:

    Controller
        ↓
    Use Case
        ↓
    Domain / Repository / Worker

Avoid:

    Controller
        ↓
    Complex business logic
        ↓
    Database
        ↓
    FFmpeg

---

# 13. Services

Services should represent meaningful application operations.

Examples:

    ProjectService
    MediaService
    TimelineService
    EditingService
    RenderService
    WorkflowService

Avoid creating services only to wrap one trivial function without a clear architectural benefit.

---

# 14. Repository Pattern

Database access should be abstracted where it improves maintainability.

Example:

    ProjectRepository

Responsibilities:

    findById
    create
    update
    delete

Business logic should not depend directly on raw database queries throughout the codebase.

---

# 15. Sequelize Boundary

Sequelize should remain primarily inside the infrastructure/data-access layer.

Avoid exposing Sequelize-specific implementation details to every module.

Preferred:

    Service
      ↓
    Repository
      ↓
    Sequelize
      ↓
    MySQL

---

# 16. Database Models

Database models represent persistence structures.

They should not automatically become the complete domain model.

Keep a distinction between:

    Database Model

and:

    Domain Model

when the complexity of the domain requires it.

---

# 17. Transactions

Transactions should be controlled at the application/service boundary.

Example:

    Create Timeline
        ↓
    Create Tracks
        ↓
    Create Clips

If the operation must be atomic, use one transaction.

---

# 18. Domain Rules

Important business rules must be enforced on the backend.

Examples:

    Clip cannot have negative duration.
    Track must belong to the timeline.
    Clip must belong to a valid track.
    Project version must match.
    User must have permission.

Never rely only on frontend validation.

---

# 19. Stateless API

The Express API should remain stateless whenever possible.

Do not store request-specific session state in API memory.

State should be stored in:

    MySQL
    Redis
    Object Storage
    Queue
    External Services

as appropriate.

---

# 20. Horizontal Scaling

Stateless API instances should be horizontally scalable.

Architecture:

    Load Balancer
        ↓
    ┌───────────┐
    │           │
    API 1     API 2
    │           │
    └─────┬─────┘
          ↓
      Shared State

---

# 21. Shared State

Shared state should not live only inside one API instance.

Avoid:

    API Memory
        ↓
    Critical Application State

Prefer:

    Redis / MySQL / Storage

---

# 22. Caching

Redis may be used for:

    Cache
    Distributed Locks
    Queue
    Temporary Workflow State
    Rate Limiting

Do not use Redis as the primary persistent database.

---

# 23. Queue-Based Architecture

Long-running tasks should use queues.

Example:

    API
      ↓
    Queue
      ↓
    Worker

Possible technology:

    BullMQ
    Redis

---

# 24. Worker Architecture

Workers should handle CPU-intensive or long-running operations.

Examples:

    FFmpeg
    FFprobe
    Transcoding
    Audio Processing
    Video Analysis
    Rendering

The API should not perform these operations synchronously.

---

# 25. Worker Isolation

Workers should be independently scalable.

Example:

    API Servers: 3

    Render Workers: 5

The number of workers can scale independently from API servers.

---

# 26. Rendering Architecture

Rendering should follow:

    Render Request
        ↓
    Render Service
        ↓
    Queue
        ↓
    Render Worker
        ↓
    FFmpeg
        ↓
    Output Validation
        ↓
    Storage

---

# 27. FFmpeg Boundary

FFmpeg must remain behind a dedicated service or adapter.

Preferred:

    RenderService
        ↓
    FFmpegAdapter
        ↓
    FFmpeg

Do not spread raw FFmpeg commands throughout the application.

---

# 28. FFprobe Boundary

FFprobe should be isolated behind a media-analysis service.

Preferred:

    MediaService
        ↓
    FFprobeAdapter
        ↓
    FFprobe

Raw FFprobe output should be normalized before entering the domain.

---

# 29. Storage Architecture

Media files should not normally be stored directly inside the application server filesystem.

Preferred:

    Application
        ↓
    Storage Service
        ↓
    Object Storage

Examples:

    Original Video
    Proxy Video
    Thumbnail
    Rendered Video
    Audio
    Subtitle

---

# 30. Media Lifecycle

Media should have a controlled lifecycle.

Example:

    Upload
      ↓
    Validation
      ↓
    Storage
      ↓
    FFprobe
      ↓
    Metadata
      ↓
    Processing
      ↓
    Ready

---

# 31. Original vs Derived Media

Keep original media separate from generated assets.

Example:

    Original
    Proxy
    Thumbnail
    Preview
    Rendered Output

Never overwrite the original media during editing.

---

# 32. Non-Destructive Editing

The editing engine should prefer non-destructive operations.

Instead of modifying the original video:

    Original Media
        ↓
    Timeline Operations
        ↓
    Render

The original remains unchanged.

---

# 33. Timeline as Source of Truth

The timeline represents the editing state.

The rendered video is a generated artifact.

Architecture:

    Media
      ↓
    Timeline
      ↓
    Render
      ↓
    Output

Do not treat the rendered file as the primary editing state.

---

# 34. Timeline Domain

Timeline should contain:

    Tracks
    Clips
    Transitions
    Effects
    Overlays
    Subtitles

The exact model should remain extensible.

---

# 35. Editing Operations

Editing operations should be represented as structured commands.

Example:

    trim
    split
    move
    delete
    duplicate
    crop
    resize
    rotate
    speed
    volume

Avoid directly modifying arbitrary database fields from the frontend.

---

# 36. Command-Based Editing

Prefer:

    Edit Command
        ↓
    Validation
        ↓
    Domain Operation
        ↓
    State Update

This makes editing operations easier to:

    Validate
    Undo
    Redo
    Log
    Replay
    Test

---

# 37. Undo / Redo

The architecture should allow future support for:

    Undo
    Redo

Possible approaches include:

    Command History
    Operation Log
    Versioned Timeline

The initial implementation does not need to implement all of them immediately.

---

# 38. Versioned Projects

Projects should have versions.

Example:

    Project v1
    Project v2
    Project v3

This supports:

    Concurrency
    Recovery
    Undo
    Audit
    Workflow consistency

---

# 39. Optimistic Concurrency

Editing operations should optionally include:

    expectedProjectVersion

Example:

    expectedVersion = 12

If current version is:

    13

return:

    VERSION_CONFLICT

---

# 40. AI Architecture

AI must remain an application capability, not the application itself.

Architecture:

    User
      ↓
    Context Engine
      ↓
    AI Agent
      ↓
    Structured Plan
      ↓
    Validation
      ↓
    Tools
      ↓
    Application Services

---

# 41. AI Provider Abstraction

The core application must not be tightly coupled to one AI provider.

Preferred:

    AIProvider
        ↓
    OpenRouter
    Anthropic
    OpenAI
    Local Model
    Other Provider

---

# 42. AI Context Engine

The Context Engine is responsible for selecting relevant information.

It should not blindly send the entire database state to the model.

Flow:

    Project State
        ↓
    Context Builder
        ↓
    Relevant Context
        ↓
    AI

---

# 43. Context Separation

Context should be separated into:

    Product
    Architecture
    Video
    Editing
    AI
    Rules

This prevents one massive context document.

---

# 44. AI Agent Responsibilities

Agents may:

    Understand user intent
    Analyze context
    Create plans
    Select tools
    Interpret results
    Replan when necessary

Agents should not:

    Access the database directly
    Execute shell commands directly
    Bypass authorization
    Modify files arbitrarily

---

# 45. Tool Architecture

AI Tools should be controlled application interfaces.

Example:

    trim_clip
    split_clip
    move_clip
    analyze_media
    generate_subtitles
    create_render_job

Tools should call application services rather than directly manipulating infrastructure whenever possible.

---

# 46. Tool Execution

Preferred:

    AI
      ↓
    Tool
      ↓
    Application Service
      ↓
    Domain
      ↓
    Infrastructure

---

# 47. Workflow Architecture

Complex AI tasks should use workflows.

Example:

    AI Agent
        ↓
    Workflow Plan
        ↓
    Workflow Orchestrator
        ↓
    Tools / Workers
        ↓
    Context Refresh
        ↓
    Next Step

---

# 48. Workflow State

Workflow state must be persisted for long-running processes.

Possible persistence:

    MySQL

Temporary coordination:

    Redis

---

# 49. Event-Driven Architecture

Important state changes may produce events.

Examples:

    project.created
    media.uploaded
    media.analyzed
    timeline.updated
    render.started
    render.completed
    workflow.completed

---

# 50. Events vs Commands

Commands request an action.

Example:

    StartRender

Events describe something that happened.

Example:

    RenderCompleted

Do not confuse the two.

---

# 51. Event Consumers

Consumers may perform secondary operations.

Example:

    render.completed
        ↓
    Update Workflow
        ↓
    Notify User
        ↓
    Update Project State

---

# 52. Event Reliability

Important events should not rely solely on in-memory events.

For critical workflows, use durable mechanisms.

Possible approaches:

    Database Event Log
    Queue
    Outbox Pattern

---

# 53. Outbox Pattern

For operations requiring database + event consistency:

    Database Transaction
        ↓
    Save Domain Change
        ↓
    Save Outbox Event
        ↓
    Commit
        ↓
    Event Publisher
        ↓
    Queue

This prevents lost events.

---

# 54. Asynchronous Architecture

Use asynchronous processing for:

    Rendering
    Transcoding
    AI analysis
    Transcription
    Large uploads
    Video processing

Avoid blocking HTTP requests for long operations.

---

# 55. Request Lifecycle

Simple operation:

    HTTP Request
        ↓
    Controller
        ↓
    Service
        ↓
    Database
        ↓
    Response

Long operation:

    HTTP Request
        ↓
    Controller
        ↓
    Service
        ↓
    Queue
        ↓
    Response: Job Created

---

# 56. Job Status

Long-running operations should return a job identifier.

Example:

    {
      "jobId": "job-001",
      "status": "queued"
    }

Frontend can then track the job.

---

# 57. Real-Time Updates

Frontend may receive updates through:

    WebSocket

or:

    Server-Sent Events

Example:

    job.progress
    job.completed
    job.failed

---

# 58. API and Worker Separation

The API and Worker should be deployable independently.

Example:

    api/
    worker/

They may share:

    Domain
    Schemas
    Utilities

but should have separate runtime responsibilities.

---

# 59. Monorepo Structure

The project should use a monorepo structure.

Example:

    apps/
        web/
        api/
        worker/

    packages/
        shared/
        schemas/
        ai/
        config/

    context/

---

# 60. Frontend Architecture

Frontend responsibilities:

    UI
    Timeline
    Editor
    Project Management
    Workflow Visualization
    API Communication

Frontend should not contain server-side business rules.

---

# 61. Backend Architecture

Backend responsibilities:

    Authentication
    Authorization
    Projects
    Media
    Timeline
    Editing
    Workflows
    AI
    Jobs
    Storage

---

# 62. Worker Architecture

Worker responsibilities:

    Video Processing
    FFmpeg
    FFprobe
    Rendering
    Transcoding
    Heavy AI processing

---

# 63. Shared Packages

Shared packages may contain:

    Types
    Schemas
    Constants
    API Contracts

Avoid placing business logic into generic shared packages without a clear reason.

---

# 64. Dependency Isolation

Frontend should not depend on:

    Sequelize
    FFmpeg
    Node filesystem APIs

Worker should not depend on:

    React
    Browser APIs

Keep runtime boundaries clean.

---

# 65. Security Boundary

Architecture must enforce security at:

    API
    Service
    Domain
    Worker

Do not rely on frontend security.

---

# 66. Authorization

Every project-related operation must verify:

    Authentication
    User identity
    Project access
    Required permission

---

# 67. Worker Security

Workers must validate job input.

Never assume queue jobs are trusted.

Validate:

    Project ID
    Media ID
    File references
    Operation
    Parameters

---

# 68. AI Security

AI-generated data must be treated as untrusted input.

Never allow AI to:

    Execute arbitrary commands
    Access arbitrary files
    Query arbitrary databases
    Modify permissions
    Bypass authorization

---

# 69. Failure Isolation

Failure in one subsystem should not bring down the entire platform.

Example:

    FFmpeg Worker Failure

should not crash:

    Express API

Workers should fail independently.

---

# 70. Retry Isolation

Retries should occur at the appropriate layer.

Example:

    Network error
        ↓
    Provider Retry

Worker failure:

    Queue Retry

Domain validation failure:

    No automatic retry

---

# 71. Timeouts

Every external or long-running operation should have appropriate timeouts.

Examples:

    AI request timeout
    Database timeout
    Redis timeout
    FFmpeg timeout
    Storage timeout

---

# 72. Observability

The architecture should support:

    Logging
    Metrics
    Tracing
    Health Checks

Important IDs:

    requestId
    workflowId
    jobId
    projectId

should be traceable across services.

---

# 73. Configuration Boundary

Environment configuration should be centralized.

Example:

    config/
        app
        database
        redis
        ai
        storage
        worker

Avoid scattered:

    process.env.*

throughout the codebase.

---

# 74. Testing Architecture

Each architectural layer should be testable independently.

Examples:

    Domain → Unit Tests
    Services → Unit/Integration Tests
    API → API Tests
    Worker → Worker Tests
    AI Tools → Contract Tests
    Frontend → Component/E2E Tests

---

# 75. Contract Testing

Important boundaries should have contracts.

Examples:

    API ↔ Frontend
    AI ↔ Tools
    Workflow ↔ Workers
    Service ↔ Repository

Schemas can define these contracts.

---

# 76. API Contract

API contracts should define:

    Request
    Response
    Errors
    Authentication
    Version

---

# 77. Tool Contract

Every AI Tool should define:

    Name
    Description
    Input Schema
    Output Schema
    Permissions
    Risk Level

---

# 78. Worker Contract

Queue jobs should define:

    Job Name
    Input
    Output
    Retry Policy
    Timeout
    Failure Behavior

---

# 79. Architecture Evolution

Architecture should evolve incrementally.

Recommended sequence:

    MVP
      ↓
    Modular Monolith
      ↓
    Queue + Workers
      ↓
    Horizontal Scaling
      ↓
    Service Extraction when justified

Do not begin with unnecessary distributed complexity.

---

# 80. Scaling Strategy

Scale independently:

    API
    Workers
    Queue
    Database
    Storage
    AI Provider

The bottleneck determines which component should scale.

---

# 81. Database Scaling

Possible future strategies:

    Read Replicas
    Index Optimization
    Connection Pooling
    Partitioning
    Sharding

Do not introduce these before measuring actual bottlenecks.

---

# 82. Worker Scaling

Rendering workers can scale based on:

    Queue Length
    CPU
    Memory
    Processing Time

Example:

    Queue ↑
        ↓
    Worker Count ↑

---

# 83. AI Scaling

AI workload can scale through:

    Provider Routing
    Model Selection
    Request Queues
    Caching
    Batching where applicable

---

# 84. Storage Scaling

Media storage should be independently scalable from application servers.

Large files should not pass unnecessarily through API servers.

Preferred:

    Client
      ↓
    Object Storage

with controlled upload mechanisms.

---

# 85. CDN

Large media delivery may eventually use a CDN.

Architecture:

    Object Storage
        ↓
    CDN
        ↓
    User

The API should not proxy large media files unless required.

---

# 86. Preview Architecture

The editor may use lower-resolution proxy media for smooth editing.

Example:

    Original
        ↓
    Proxy Generator
        ↓
    Proxy Video
        ↓
    Frontend Timeline

Final rendering uses original media.

---

# 87. Rendering Source

Final rendering should use:

    Original Media
    Timeline State
    Editing Operations

not:

    Low-resolution Preview

---

# 88. Architecture Rule for Proxies

Proxy media is a performance optimization.

It must never become the canonical source of truth.

---

# 89. Long-Term Architecture

Potential future architecture:

    ┌───────────────┐
    │   Frontend    │
    └───────┬───────┘
            ↓
    ┌───────────────┐
    │ Load Balancer │
    └───────┬───────┘
            ↓
    ┌─────────────────────┐
    │   Express API       │
    │   Modular Monolith  │
    └───────┬─────────────┘
            ↓
      ┌─────┴─────┐
      ↓           ↓
    MySQL       Redis
                  ↓
                Queue
                  ↓
        ┌─────────┴─────────┐
        ↓                   ↓
   Video Workers        AI Workers
        ↓                   ↓
     FFmpeg            AI Providers
        ↓
 Object Storage
        ↓
       CDN

---

# 90. Golden Rules

1. Start with a modular monolith.
2. Keep domain boundaries clear.
3. Keep controllers thin.
4. Keep business logic outside controllers.
5. Isolate infrastructure.
6. Use Sequelize through controlled data-access boundaries.
7. Keep APIs stateless.
8. Use Redis for shared temporary state and queues.
9. Use BullMQ for asynchronous jobs.
10. Isolate CPU-intensive processing in workers.
11. Keep FFmpeg behind a rendering boundary.
12. Keep FFprobe behind a media-analysis boundary.
13. Use non-destructive editing.
14. Treat the timeline as the editing source of truth.
15. Use structured editing commands.
16. Track project versions.
17. Use optimistic concurrency where necessary.
18. Keep AI provider-independent.
19. Treat AI output as untrusted.
20. Never allow arbitrary AI execution.
21. Use schemas between system boundaries.
22. Use workflows for complex AI operations.
23. Persist long-running workflow state.
24. Make important operations observable.
25. Prefer asynchronous processing for heavy workloads.
26. Keep frontend and backend responsibilities separate.
27. Scale APIs and workers independently.
28. Avoid premature microservices.
29. Measure before introducing advanced scaling techniques.
30. Evolve architecture incrementally.