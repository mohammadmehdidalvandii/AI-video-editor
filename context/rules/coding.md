# Coding Rules

## Purpose

This document defines the coding standards and development rules for the AI Video Editor.

The goal is to keep the codebase:

- Maintainable
- Readable
- Testable
- Scalable
- Consistent
- Modular
- Secure

---

# 1. General Principles

Code must prioritize:

1. Readability
2. Maintainability
3. Simplicity
4. Type safety
5. Testability
6. Performance
7. Security

Avoid unnecessary complexity.

Do not introduce abstractions without a clear reason.

---

# 2. TypeScript

The project uses TypeScript as the primary programming language for application code.

Prefer:

    TypeScript

over:

    JavaScript

Avoid:

    any

unless there is a strong technical reason.

When `any` is unavoidable, document why it is required.

---

# 3. Strict Type Safety

TypeScript strict mode should be enabled.

Avoid unsafe type assertions.

Prefer:

    type guards
    validation
    schemas
    typed interfaces

Example:

    const user = parseUser(input)

instead of:

    const user = input as User

---

# 4. Naming Conventions

Use descriptive names.

Good:

    createRenderJob
    getProjectById
    validateTimeline
    analyzeMedia

Avoid:

    fn
    data
    temp
    process
    x

unless the scope is extremely small.

---

# 5. Variables

Use:

    camelCase

Example:

    const projectId = "project-001";

---

# 6. Constants

Use descriptive constants.

Example:

    const MAX_WORKFLOW_RETRIES = 3;

For globally shared constants:

    UPPER_SNAKE_CASE

---

# 7. Classes

Use:

    PascalCase

Example:

    class WorkflowOrchestrator {}

---

# 8. Interfaces and Types

Use:

    PascalCase

Example:

    interface RenderJob {}

    type WorkflowStatus = ...

Do not prefix interfaces with:

    I

Avoid:

    IUser

Prefer:

    User

---

# 9. Functions

Functions should have one clear responsibility.

Good:

    validateClip()
    createRenderJob()
    getMediaMetadata()

Avoid large functions that perform multiple unrelated responsibilities.

---

# 10. Function Size

Prefer small functions.

If a function becomes too large:

    Extract smaller functions.

Example:

    processVideo()

should potentially become:

    validateInput()
    prepareMedia()
    buildCommand()
    executeRender()
    validateOutput()

---

# 11. Single Responsibility

Every module should have a clear responsibility.

Example:

    MediaService

handles media-related business operations.

It should not also:

    execute database migrations
    manage authentication
    build HTTP responses

---

# 12. Separation of Concerns

Separate:

    Controller
    Service
    Repository
    Domain
    Worker
    AI
    Tool
    Infrastructure

Each layer should have a clear responsibility.

---

# 13. Controllers

Controllers should remain thin.

Controller responsibilities:

    Receive request
    Validate request
    Call service
    Return response

Avoid putting business logic inside controllers.

Bad:

    Controller
        ↓
    complex business logic
        ↓
    database
        ↓
    FFmpeg

Preferred:

    Controller
        ↓
    Service
        ↓
    Repository / Worker / Domain

---

# 14. Services

Services contain application/business logic.

Example:

    ProjectService
    MediaService
    TimelineService
    RenderService
    WorkflowService

Services should not depend directly on HTTP-specific objects when avoidable.

---

# 15. Repositories

Repositories handle persistence.

Example:

    ProjectRepository
    MediaRepository
    WorkflowRepository

Repositories should abstract database access.

Business logic should not contain raw Sequelize queries everywhere.

---

# 16. Sequelize

The backend uses:

    Sequelize

as the ORM.

Database access should be centralized through models and repositories.

Avoid scattering:

    Model.findOne()
    Model.create()
    Model.update()

through unrelated business modules.

---

# 17. Database Transactions

Use transactions for operations that require atomicity.

Example:

    Create Order
        ↓
    Create Items
        ↓
    Update Inventory

All operations should succeed or fail together when required.

---

# 18. Async Code

Use:

    async / await

for asynchronous operations.

Avoid deeply nested Promise chains.

Good:

    const media = await mediaService.getById(id);

---

# 19. Error Handling

Never silently ignore errors.

Bad:

    try {
      await operation();
    } catch {}

Errors should either:

- Be handled
- Be transformed
- Be logged
- Be propagated

---

# 20. Custom Errors

Use structured application errors.

Example:

    NotFoundError
    ValidationError
    AuthorizationError
    ConflictError
    ProcessingError

Errors should contain stable error codes.

---

# 21. Error Codes

Prefer:

    PROJECT_NOT_FOUND

over relying only on human-readable messages.

Messages may change.

Error codes should remain stable.

---

# 22. Logging

Use structured logging.

Logs should include useful identifiers:

    requestId
    workflowId
    projectId
    jobId

Do not log:

    passwords
    API keys
    tokens
    private credentials

---

# 23. Comments

Write comments when they explain:

    Why

not simply:

    What

Bad:

    // Increment counter
    counter++;

Good:

    // Increment only after successful worker acknowledgement
    counter++;

---

# 24. TODO

TODO comments should contain actionable information.

Example:

    // TODO: Replace temporary implementation with distributed lock.

Avoid:

    // TODO: fix this

---

# 25. Magic Numbers

Avoid unexplained values.

Bad:

    if (retryCount > 3)

Prefer:

    const MAX_RETRIES = 3;

    if (retryCount > MAX_RETRIES)

---

# 26. Configuration

Environment-specific values must come from configuration.

Examples:

    Database URL
    Redis URL
    AI API keys
    Storage configuration
    Worker configuration

Never hardcode secrets.

---

# 27. Environment Variables

Use:

    .env

for local development.

Use:

    .env.example

to document required variables.

Never commit:

    .env

when it contains secrets.

---

# 28. API Keys

Never place API keys directly in source code.

Bad:

    const API_KEY = "secret";

Correct:

    process.env.OPENROUTER_API_KEY

---

# 29. Input Validation

All external input must be validated.

Sources include:

    HTTP requests
    AI outputs
    Queue jobs
    Webhooks
    User uploads

Never trust external data.

---

# 30. Schema Validation

Use schemas for runtime validation.

Possible technology:

    Zod

Schemas should be reused where appropriate.

---

# 31. AI Output

Never execute raw AI output.

Required flow:

    AI Output
        ↓
    Schema Validation
        ↓
    Authorization
        ↓
    Domain Validation
        ↓
    Execution

---

# 32. Tool Execution

AI Tools must expose explicit contracts.

Each Tool should define:

    Input
    Output
    Permissions
    Risk Level
    Errors

---

# 33. FFmpeg Commands

Never allow the AI to directly execute arbitrary shell commands.

Preferred:

    AI
      ↓
    Render Request
      ↓
    Rendering Service
      ↓
    FFmpeg

The Rendering Service controls the command construction.

---

# 34. Filesystem Access

Never allow arbitrary AI-generated filesystem paths.

Prefer:

    mediaId

instead of:

    /home/user/video.mp4

The backend resolves the actual resource.

---

# 35. File Uploads

Validate:

    MIME type
    File size
    Extension
    File signature when required

Do not trust the file extension alone.

---

# 36. Video Processing

Video processing should be isolated from HTTP request handling.

Bad:

    Express Request
        ↓
    FFmpeg for 20 minutes

Preferred:

    Express
        ↓
    Queue
        ↓
    Worker
        ↓
    FFmpeg

---

# 37. Workers

Workers should be responsible for CPU-intensive or long-running tasks.

Examples:

    FFmpeg
    FFprobe
    Transcoding
    Thumbnail generation
    Audio analysis
    Video analysis

---

# 38. Queue Jobs

Jobs should contain references instead of large payloads when possible.

Prefer:

    {
      "projectId": "project-001",
      "mediaId": "media-001"
    }

instead of embedding large video metadata or binary data.

---

# 39. Idempotency

Important operations should be idempotent when possible.

Example:

    createRenderJob

should prevent accidental duplicate rendering when the same request is retried.

---

# 40. Database Access

Avoid N+1 query problems.

Before loading related entities repeatedly:

    Analyze query strategy.

Use:

    eager loading
    batching
    optimized queries

when appropriate.

---

# 41. Performance

Do not optimize prematurely.

First:

    Measure
    Identify bottleneck
    Optimize
    Measure again

Performance decisions should be evidence-based.

---

# 42. Caching

Use caching only when necessary.

Possible cache:

    Redis

Good candidates:

    Frequently accessed metadata
    Session data
    Temporary workflow state
    Expensive computation results

Avoid caching data without an invalidation strategy.

---

# 43. Frontend State

Frontend state should be separated by responsibility.

Possible categories:

    Server State
    Client State
    Form State
    UI State

Use appropriate tools for each category.

---

# 44. React Components

Components should have focused responsibilities.

Avoid giant components containing:

    API calls
    Business logic
    State management
    Rendering
    Complex transformations

Extract logic into hooks/services when appropriate.

---

# 45. React Hooks

Custom hooks should encapsulate reusable behavior.

Example:

    useProject()
    useTimeline()
    useWorkflow()
    useRenderJob()

Hooks should not contain unrelated functionality.

---

# 46. API Client

Frontend API communication should be centralized.

Avoid scattering raw:

    fetch()

calls throughout components.

Prefer:

    API Client
        ↓
    Services / Hooks
        ↓
    Components

---

# 47. State Management

Use server-state tools for server data.

Example:

    TanStack Query

Use client-state tools for client-specific state.

Example:

    Zustand

Do not duplicate server state unnecessarily inside global client state.

---

# 48. Timeline State

Timeline state should have a clear source of truth.

Avoid having multiple independent copies of:

    clips
    tracks
    currentTime
    selection

without a synchronization strategy.

---

# 49. Immutable State

Prefer immutable state updates.

Avoid unexpected mutations of shared objects.

This is especially important for:

    Timeline
    Tracks
    Clips
    Workflow State

---

# 50. API Response Format

API responses should be consistent.

Example:

    {
      "success": true,
      "data": {}
    }

Errors:

    {
      "success": false,
      "error": {
        "code": "PROJECT_NOT_FOUND",
        "message": "Project not found."
      }
    }

---

# 51. HTTP Status Codes

Use appropriate status codes.

Examples:

    200 OK
    201 Created
    400 Bad Request
    401 Unauthorized
    403 Forbidden
    404 Not Found
    409 Conflict
    422 Unprocessable Entity
    500 Internal Server Error

---

# 52. API Versioning

Public APIs should be versioned when required.

Example:

    /api/v1/projects

Breaking API changes should introduce a new version.

---

# 53. Testing

Critical application logic must have tests.

Test categories:

    Unit Tests
    Integration Tests
    API Tests
    Worker Tests
    AI Tool Tests
    End-to-End Tests

---

# 54. Unit Tests

Use unit tests for:

    Domain logic
    Services
    Validators
    Utilities
    Timeline operations

---

# 55. Integration Tests

Integration tests should verify:

    Database
    Redis
    Queue
    Services
    API

when appropriate.

---

# 56. AI Tests

AI behavior can be variable.

Test deterministic boundaries instead:

    Schema Validation
    Tool Selection
    Tool Execution
    Workflow State
    Error Handling

---

# 57. FFmpeg Tests

Do not rely only on visual inspection.

Test:

    Command generation
    Input validation
    Output validation
    Metadata
    Exit codes

---

# 58. FFprobe Tests

Verify normalized metadata.

Example:

    duration
    width
    height
    fps
    codec
    audio channels

---

# 59. Test Naming

Tests should describe behavior.

Good:

    should reject invalid clip range

Avoid:

    test1

---

# 60. Git Commits

Commits should be small and focused.

Examples:

    feat: add timeline service

    fix: handle render worker timeout

    refactor: simplify workflow executor

    test: add clip validation tests

    docs: update architecture context

---

# 61. Pull Requests

Each pull request should ideally have one clear purpose.

Include:

    What changed
    Why it changed
    Testing performed
    Known limitations

---

# 62. Code Review

Review:

    Correctness
    Security
    Architecture
    Performance
    Testability
    Maintainability

Do not focus only on formatting.

---

# 63. Dependencies

Do not add dependencies without a reason.

Before adding a package:

    Check existing capabilities
    Check maintenance
    Check bundle impact
    Check security
    Check license
    Check compatibility

---

# 64. Dependency Versions

Avoid unnecessary dependency upgrades during unrelated work.

Keep upgrades isolated when possible.

---

# 65. Formatting

Use a consistent formatter.

Recommended:

    Prettier

Formatting should be automated.

---

# 66. Linting

Use:

    ESLint

Linting should run automatically during development and CI.

---

# 67. Pre-Commit Quality

Before committing:

    lint
    typecheck
    tests

Critical failures should block the commit or CI pipeline.

---

# 68. Architecture Boundaries

Do not bypass architectural layers without a strong reason.

Example:

    Controller
        ↓
    Service
        ↓
    Repository

Avoid:

    Controller
        ↓
    raw database query

---

# 69. Circular Dependencies

Avoid circular dependencies.

If modules depend on each other:

    A → B
    B → A

Refactor shared functionality into a separate module.

---

# 70. Dependency Direction

Prefer dependencies flowing toward stable abstractions.

Example:

    API
      ↓
    Application
      ↓
    Domain
      ↓
    Infrastructure

Infrastructure should not dictate business rules.

---

# 71. Domain Logic

Important video-editing rules belong in domain/application logic.

Examples:

    Clip cannot have negative duration
    End must be greater than start
    Track must exist
    Project version must match

Do not rely only on the frontend.

---

# 72. Security Boundary

Security-sensitive logic must be enforced on the backend.

Never trust:

    Frontend permissions
    AI decisions
    Client-provided ownership
    Client-provided roles

---

# 73. API Security

Protect APIs using:

    Authentication
    Authorization
    Rate Limiting
    Input Validation
    Secure Headers
    Logging

---

# 74. User Data

Only access data the authenticated user is authorized to access.

Every project-related operation should verify ownership or permission.

---

# 75. Media Security

Media resources should not become publicly accessible by default.

Use controlled access mechanisms.

---

# 76. Temporary Files

Temporary files created by workers should have cleanup policies.

Example:

    Render temporary files
        ↓
    Processing
        ↓
    Upload result
        ↓
    Cleanup

---

# 77. Resource Cleanup

Workers must clean up:

    Temporary files
    Processes
    Locks
    Queue resources

especially after failures.

---

# 78. Process Management

FFmpeg processes must have:

    Timeout
    Exit-code handling
    Error handling
    Cancellation support
    Cleanup

Never assume FFmpeg always succeeds.

---

# 79. Graceful Shutdown

Services and workers should support graceful shutdown.

On shutdown:

    Stop accepting new work
    Finish safe operations
    Release resources
    Close connections

---

# 80. Configuration

Configuration should be centralized.

Example:

    config/
        database
        redis
        ai
        storage
        worker

Avoid reading environment variables throughout the entire codebase.

---

# 81. Feature Flags

Large experimental features may use feature flags.

Example:

    ENABLE_AI_AUTO_EDITING=true

Feature flags should be temporary when possible.

---

# 82. Experimental Features

Experimental AI functionality should remain isolated.

Example:

    experimental/
        auto-editor

Do not allow experimental code to silently affect stable workflows.

---

# 83. Documentation

Important modules should have documentation.

Document:

    Purpose
    Responsibilities
    Inputs
    Outputs
    Dependencies
    Failure behavior

---

# 84. Architecture Documentation

When architecture changes significantly:

    Update context/architecture/

The context should remain synchronized with the actual implementation.

---

# 85. Context Documentation

When AI behavior changes:

    Update context/ai/

When video processing behavior changes:

    Update context/video/

When coding standards change:

    Update context/rules/

---

# 86. Source of Truth

The actual application code is the source of truth for implementation.

Context documents define:

    Intent
    Architecture
    Rules
    Constraints

If implementation and context diverge, update the context.

---

# 87. Backward Compatibility

Avoid breaking existing projects unnecessarily.

For breaking changes:

    Migration
    Versioning
    Documentation
    Tests

must be considered.

---

# 88. Refactoring

Refactoring should preserve behavior unless behavior changes are intentional.

Prefer small incremental refactors.

Avoid rewriting large portions of the project without measurable benefit.

---

# 89. Technical Debt

Technical debt should be documented.

Example:

    // TODO:
    // Replace temporary local queue implementation
    // with distributed BullMQ worker.

Do not allow temporary solutions to become invisible permanent architecture.

---

# 90. Simplicity Rule

The simplest solution that satisfies the architectural requirements should be preferred.

Avoid:

    Overengineering
    Premature abstraction
    Unnecessary microservices
    Unnecessary dependencies

---

# 91. Scalability Rule

The system should be designed for future scale, but the MVP should remain practical.

Prefer:

    Modular Monolith

before unnecessarily splitting the system into many microservices.

---

# 92. Worker Isolation

CPU-intensive workloads should remain isolated from API processes.

Example:

    Express API
        ↓
    Queue
        ↓
    Worker

---

# 93. AI Provider Independence

Core application logic must not depend directly on one AI provider.

Use:

    AI Provider Adapter
        ↓
    Internal AI Interface

This allows provider replacement.

---

# 94. Storage Independence

Application services should not tightly couple themselves to one storage provider.

Use:

    Storage Service

instead of directly accessing provider-specific APIs everywhere.

---

# 95. Observability

Production systems should provide:

    Logs
    Metrics
    Traces
    Health Checks

Important workflows should be traceable end-to-end.

---

# 96. Health Checks

Services should expose health information.

Examples:

    API health
    Database health
    Redis health
    Worker health

---

# 97. Performance Measurement

Performance changes should be measured.

Important metrics include:

    API latency
    Database latency
    Queue latency
    Worker duration
    FFmpeg processing time
    AI latency

---

# 98. Code Quality Priority

When choosing between implementations:

    Correctness
        >
    Security
        >
    Maintainability
        >
    Performance
        >
    Cleverness

Readable code is preferred over clever code.

---

# 99. Golden Rule

Every piece of code should answer:

    What is its responsibility?

    Who owns it?

    What does it depend on?

    How is it tested?

    What happens if it fails?

If these questions cannot be answered clearly, the design should be reconsidered.

---

# 100. Final Coding Principles

1. Keep code simple.
2. Keep modules focused.
3. Prefer TypeScript.
4. Avoid unnecessary `any`.
5. Validate external input.
6. Never trust AI output.
7. Keep controllers thin.
8. Keep business logic in services/domain.
9. Keep database access organized.
10. Use Sequelize consistently.
11. Use transactions when atomicity is required.
12. Isolate heavy processing in workers.
13. Use queues for long-running operations.
14. Never execute arbitrary AI-generated shell commands.
15. Never expose arbitrary filesystem access.
16. Keep secrets outside source code.
17. Use structured errors.
18. Use structured logging.
19. Write tests for critical logic.
20. Measure performance before optimizing.
21. Keep frontend and backend responsibilities separated.
22. Keep AI provider dependencies isolated.
23. Keep architecture documentation synchronized.
24. Prefer incremental changes.
25. Avoid unnecessary complexity.
26. Make important operations observable.
27. Make long-running operations cancellable.
28. Make retryable operations idempotent when possible.
29. Enforce security on the backend.
30. Treat maintainability as a core architectural requirement.