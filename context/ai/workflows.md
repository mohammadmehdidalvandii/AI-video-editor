# AI Workflows Context

## Purpose

This document defines how AI-driven workflows operate inside the AI Video Editor.

A workflow represents a sequence of AI reasoning, context retrieval, tool execution, validation, and asynchronous processing required to complete a complex task.

The workflow system must be:

- Structured
- Observable
- Resumable
- Fault-tolerant
- Version-aware
- Context-aware
- Tool-driven
- Asynchronous when required

---

# 1. Workflow Architecture

The general workflow architecture is:

    User Request
        ↓
    Intent Detection
        ↓
    Context Retrieval
        ↓
    Planning
        ↓
    Validation
        ↓
    Tool Execution
        ↓
    State Update
        ↓
    Context Refresh
        ↓
    Next Step
        ↓
    Verification
        ↓
    Final Response

---

# 2. Workflow Definition

A workflow is a structured execution process composed of multiple steps.

Example:

    Create Short Video

    1. Analyze video
    2. Detect important segments
    3. Remove unnecessary sections
    4. Arrange clips
    5. Generate subtitles
    6. Add transitions
    7. Render
    8. Verify output

---

# 3. Workflow Principles

Workflows must:

- Have a clear goal
- Have explicit steps
- Track execution state
- Validate every operation
- Handle failures
- Support retries
- Support cancellation
- Track progress
- Maintain context
- Avoid unnecessary AI calls

---

# 4. Simple vs Complex Workflows

Not every request requires a workflow.

Simple:

    "Mute this clip."

Execution:

    set_volume

Complex:

    "Create a 60-second YouTube Short from this video."

Execution:

    Analyze
    ↓
    Select segments
    ↓
    Edit
    ↓
    Subtitle
    ↓
    Render

Complex tasks should use workflows.

---

# 5. Workflow Lifecycle

A workflow can have:

    created
    planning
    queued
    running
    paused
    waiting
    completed
    failed
    cancelled

Example:

    created
        ↓
    planning
        ↓
    queued
        ↓
    running
        ↓
    completed

---

# 6. Workflow State

Conceptual structure:

    {
      "workflowId": "workflow-001",
      "type": "video_editing",
      "status": "running",
      "currentStep": "step-03",
      "progress": 45
    }

The state must be persisted for long-running workflows.

---

# 7. Workflow ID

Every workflow must have a unique identifier.

Example:

    workflowId = "workflow-001"

The ID is used for:

- Tracking
- Logging
- Debugging
- Resuming
- Cancellation
- Monitoring

---

# 8. Workflow Types

Possible workflow types:

    video_analysis
    automatic_editing
    subtitle_generation
    short_generation
    rendering
    media_processing
    content_repurposing

---

# 9. Workflow Steps

Each workflow contains ordered steps.

Example:

    Step 1:
    analyze_media

    Step 2:
    detect_silence

    Step 3:
    remove_silence

    Step 4:
    render

---

# 10. Workflow Step Schema

Conceptual:

    {
      "id": "step-01",
      "type": "tool_call",
      "action": "analyze_media",
      "status": "pending"
    }

Possible fields:

    id
    type
    action
    status
    input
    output
    error
    startedAt
    completedAt

---

# 11. Step Types

Possible step types:

    ai
    tool
    worker
    validation
    condition
    approval
    notification

Example:

    AI Step
        ↓
    Tool Step
        ↓
    Worker Step
        ↓
    Validation Step

---

# 12. AI Step

An AI step performs reasoning or analysis.

Example:

    Analyze important moments in the video.

The AI may produce:

    {
      "segments": [
        {
          "start": 10,
          "end": 25
        }
      ]
    }

The result must be schema-validated.

---

# 13. Tool Step

A Tool step executes an application operation.

Example:

    trim_clip

Tool execution must pass through:

    Schema Validation
        ↓
    Authorization
        ↓
    Domain Validation
        ↓
    Tool

---

# 14. Worker Step

Heavy operations should run through workers.

Examples:

    FFmpeg rendering
    Video transcoding
    Audio analysis
    Transcription
    Thumbnail generation

Architecture:

    Workflow
        ↓
    Queue
        ↓
    Worker
        ↓
    Processing
        ↓
    Result

---

# 15. Validation Step

Validation steps verify workflow results.

Example:

    Expected:

    Clip duration = 30 seconds

    Actual:

    Clip duration = 30 seconds

Result:

    valid

---

# 16. Condition Step

A workflow may branch based on conditions.

Example:

    Is speech detected?

        YES → Generate subtitles

        NO → Skip subtitles

---

# 17. Approval Step

High-risk workflows may require human approval.

Example:

    AI generates editing plan
        ↓
    Approval Required
        ↓
    User approves
        ↓
    Execute plan

---

# 18. Notification Step

The system may notify the user when:

    Workflow completes
    Workflow fails
    Approval is required
    Rendering finishes

---

# 19. Workflow Planning

The AI may generate a plan.

Example:

    User:
    "Make a short video from this recording."

AI Plan:

    1. Analyze media
    2. Detect important segments
    3. Create clips
    4. Arrange clips
    5. Generate subtitles
    6. Render

The plan must be validated before execution.

---

# 20. Planning vs Execution

Planning:

    AI decides what should happen.

Execution:

    Application decides whether the operation is valid.

This separation is critical.

---

# 21. Workflow Orchestrator

The Workflow Orchestrator manages execution.

Responsibilities:

- Create workflows
- Load workflow state
- Execute steps
- Track progress
- Handle failures
- Retry steps
- Pause workflows
- Resume workflows
- Cancel workflows
- Update context

The Orchestrator should not contain media-processing implementation details.

---

# 22. Workflow Engine

Conceptual architecture:

    Workflow API
        ↓
    Workflow Orchestrator
        ↓
    Workflow State
        ↓
    Step Executor
        ↓
    AI / Tools / Workers
        ↓
    Result
        ↓
    State Update

---

# 23. Workflow State Persistence

Long-running workflows must persist state.

Possible storage:

    MySQL

Additional temporary state may use:

    Redis

Example:

    MySQL:
    Persistent workflow state

    Redis:
    Locks
    Temporary state
    Queue metadata

---

# 24. Workflow Queue

Heavy workflows should use a queue.

Architecture:

    API
      ↓
    Workflow
      ↓
    Redis / Queue
      ↓
    Worker
      ↓
    FFmpeg / AI / Processing

BullMQ can be used for queue management.

---

# 25. Queue Jobs

A workflow may create jobs.

Example:

    Workflow:
    workflow-001

    Job:
    job-001

Relationship:

    Workflow
        ↓
    Job
        ↓
    Worker

---

# 26. Job vs Workflow

A Job represents one executable unit.

A Workflow represents the complete business process.

Example:

    Workflow:
    Create YouTube Short

    Jobs:

    analyze_video
    generate_transcript
    render_video

---

# 27. Workflow Context

Every workflow should have context.

Example:

    {
      "projectId": "project-001",
      "timelineId": "timeline-001",
      "selectedMediaId": "media-001"
    }

The Context Engine may update this state as the workflow progresses.

---

# 28. Context Refresh

After a state-changing operation:

    Tool Execution
        ↓
    Database Update
        ↓
    Context Refresh
        ↓
    Next Workflow Step

This prevents stale AI decisions.

---

# 29. Workflow Version

The workflow should track project version.

Example:

    projectVersion = 12

After modification:

    projectVersion = 13

This helps detect concurrent changes.

---

# 30. Optimistic Concurrency

Example:

    Workflow expects version 12.

Another user changes project.

Current version:

    13

Workflow attempts:

    modify version 12

Result:

    VERSION_CONFLICT

The workflow should refresh context before continuing.

---

# 31. Workflow Idempotency

Workflow steps should be idempotent where possible.

Example:

    generate_thumbnail

If the same job is accidentally executed twice, the system should avoid creating duplicate resources when possible.

---

# 32. Idempotency Key

Example:

    idempotencyKey:
    workflow-001-step-03

This can prevent duplicate execution.

---

# 33. Retry Strategy

Retry only operations that are safe to retry.

Safe:

    Temporary network failure
    Worker timeout
    Temporary provider error

Potentially unsafe:

    Delete operations
    Publish operations
    Payment operations

---

# 34. Retry Limits

Every retryable step should have a maximum retry count.

Example:

    maxRetries = 3

After the limit:

    Step → failed

The workflow may then:

    Retry manually
    Recover
    Abort
    Request user intervention

---

# 35. Exponential Backoff

Temporary failures should use backoff.

Example:

    Retry 1:
    1 second

    Retry 2:
    2 seconds

    Retry 3:
    4 seconds

Exact values should be configurable.

---

# 36. Workflow Failure

If a critical step fails:

    Step Failed
        ↓
    Workflow Failed

If a non-critical step fails:

    Step Failed
        ↓
    Recovery / Skip
        ↓
    Continue

The workflow definition determines which behavior applies.

---

# 37. Partial Failure

Example:

    Analyze Video → success
    Generate Subtitles → success
    Add Transition → failed
    Render → blocked

The workflow should preserve previous successful state.

---

# 38. Compensation

Some operations may require compensation.

Example:

    Step 1:
    Create temporary clips

    Step 2:
    Processing fails

    Compensation:

    Remove temporary clips

Compensation should be explicitly defined.

---

# 39. Workflow Cancellation

Users should be able to cancel long-running workflows.

Example:

    User:
    Cancel rendering.

Workflow:

    running
        ↓
    cancellation_requested
        ↓
    cancelling
        ↓
    cancelled

Workers should respect cancellation signals.

---

# 40. Workflow Pause

Some workflows may pause for:

    User approval
    External dependency
    Resource availability

Example:

    waiting_for_approval

The workflow resumes after the required condition is satisfied.

---

# 41. Workflow Resume

A paused workflow should resume from persisted state.

Example:

    Step 1 completed
    Step 2 completed
    Step 3 waiting

After approval:

    Step 3 continues

Completed steps should not be unnecessarily repeated.

---

# 42. Workflow Progress

Progress can be represented as:

    0–100%

Example:

    Analyze:
    20%

    Editing:
    50%

    Rendering:
    90%

    Completed:
    100%

Progress should be approximate unless exact progress is available.

---

# 43. Workflow Events

Possible events:

    workflow.created
    workflow.started
    workflow.step.started
    workflow.step.completed
    workflow.step.failed
    workflow.paused
    workflow.resumed
    workflow.cancelled
    workflow.completed
    workflow.failed

Events can support observability and notifications.

---

# 44. Event-Driven Architecture

The workflow system can use events:

    Workflow
        ↓
    Event
        ↓
    Event Handler

Example:

    render.completed
        ↓
    workflow.step.completed
        ↓
    workflow.next_step

---

# 45. Workflow State Machine

A workflow should behave like a state machine.

Example:

    CREATED
       ↓
    PLANNING
       ↓
    QUEUED
       ↓
    RUNNING
       ↓
    COMPLETED

Failure:

    RUNNING
       ↓
    FAILED

Cancellation:

    RUNNING
       ↓
    CANCELLING
       ↓
    CANCELLED

---

# 46. Invalid State Transitions

The system must reject invalid transitions.

Example:

    COMPLETED
        ↓
    RUNNING

This should not happen unless an explicit workflow restart mechanism exists.

---

# 47. Workflow Timeout

Every workflow may have a timeout.

Example:

    maximumDuration = 2 hours

If exceeded:

    Workflow → timeout

Long-running rendering jobs may have different limits.

---

# 48. Step Timeout

Individual steps may also have timeouts.

Example:

    AI Analysis:
    2 minutes

    FFmpeg Render:
    30 minutes

Timeouts should be configurable.

---

# 49. Resource Limits

Workflows should have resource limits.

Examples:

    Maximum rendering duration
    Maximum file size
    Maximum number of clips
    Maximum AI calls
    Maximum workflow duration

This prevents uncontrolled resource consumption.

---

# 50. AI Call Limits

A workflow may have:

    maxModelCalls

Example:

    maximum = 10

If exceeded:

    workflow → paused/failed

This protects against infinite AI loops.

---

# 51. Tool Call Limits

Similarly:

    maxToolCalls

Example:

    maximum = 100

This prevents runaway execution.

---

# 52. Workflow Loop Prevention

AI workflows must avoid infinite loops.

Bad:

    AI
      ↓
    Tool
      ↓
    AI
      ↓
    Tool
      ↓
    AI
      ↓
    ...

The workflow engine must track:

    step count
    tool calls
    AI calls
    execution time

---

# 53. Workflow Observability

Track:

    workflowId
    projectId
    userId
    status
    currentStep
    duration
    AI calls
    Tool calls
    Worker jobs
    errors

Sensitive data must not be logged.

---

# 54. Workflow Logging

Example:

    workflow.started

    workflowId:
    workflow-001

    type:
    automatic_editing

    projectId:
    project-001

Logs should be structured.

---

# 55. Workflow Metrics

Useful metrics:

    Workflow Success Rate
    Workflow Failure Rate
    Average Duration
    Average AI Calls
    Average Tool Calls
    Average Render Time
    Retry Rate
    Cancellation Rate

---

# 56. Workflow Tracing

A workflow should have a correlation ID.

Example:

    correlationId:
    req-123456

This allows tracing:

    API Request
        ↓
    Workflow
        ↓
    AI Call
        ↓
    Tool
        ↓
    Queue
        ↓
    Worker
        ↓
    FFmpeg

---

# 57. Workflow Security

Every workflow must enforce:

    Authentication
    Authorization
    Project Ownership
    Tool Permissions
    Resource Limits

The AI cannot bypass these controls.

---

# 58. Workflow Isolation

A workflow must only access resources belonging to its authorized project.

Example:

    workflow.projectId = project-001

It must not access:

    project-002

even if the AI generates the ID.

---

# 59. Workflow Input Validation

Workflow input must be validated before creation.

Example:

    CreateShortWorkflowRequest

    {
      "projectId": "project-001",
      "mediaId": "media-001",
      "duration": 60
    }

---

# 60. Workflow Output

A completed workflow should produce a structured result.

Example:

    {
      "workflowId": "workflow-001",
      "status": "completed",
      "result": {
        "projectId": "project-001",
        "renderJobId": "job-001"
      }
    }

---

# 61. Workflow Result Validation

The final result should be validated.

Example:

    Expected:
    renderJobId

Actual:

    missing

Result:

    workflow validation failure

The system should not report successful completion incorrectly.

---

# 62. Workflow Templates

Common workflows should be reusable.

Example:

    workflows/
        create-short
        remove-silence
        generate-subtitles
        auto-edit
        render-video

Each workflow template defines:

    Steps
    Inputs
    Outputs
    Conditions
    Retry rules
    Permissions

---

# 63. Workflow Definition

Conceptual:

    {
      "name": "remove-silence",
      "version": "1.0.0",
      "steps": [
        {
          "id": "analyze",
          "type": "worker"
        },
        {
          "id": "edit",
          "type": "tool"
        }
      ]
    }

---

# 64. Workflow Versioning

Workflows must be versioned.

Example:

    remove-silence v1.0
    remove-silence v1.1

Existing workflows should continue using their original definition where necessary.

---

# 65. Workflow Migration

If workflow structure changes while a workflow is running, the system must define whether:

    Continue with old version

or:

    Migrate to new version

Do not automatically change running workflows without a migration strategy.

---

# 66. Workflow Composition

Large workflows may contain sub-workflows.

Example:

    Create Short
        ↓
    Video Analysis
        ↓
    Subtitle Generation
        ↓
    Rendering

Each component can be independently developed.

---

# 67. Sub-Workflow

Example:

    Main Workflow:
    create_short

    Sub-Workflow:
    generate_subtitles

The parent workflow waits for the child workflow result.

---

# 68. Workflow Dependencies

Steps may depend on previous steps.

Example:

    generate_subtitles

requires:

    transcribe_audio

Dependency:

    transcribe_audio
        ↓
    generate_subtitles

---

# 69. Parallel Execution

Independent steps may execute in parallel.

Example:

    Analyze Video
        ├── Audio Analysis
        ├── Scene Detection
        └── Object Detection

These can execute concurrently.

---

# 70. Sequential Execution

Dependent operations remain sequential.

Example:

    Detect Silence
        ↓
    Remove Silence
        ↓
    Render

---

# 71. Parallel Workflow Safety

Parallel operations must not create conflicting modifications.

Bad:

    Agent A → modify timeline
    Agent B → modify same timeline

Better:

    Analyze operations in parallel

Then:

    Apply edits sequentially

---

# 72. Workflow Locking

When necessary, the system may use project-level or resource-level locks.

Example:

    project-001
        ↓
    workflow-001 holds editing lock

Locks should be short-lived and recoverable.

---

# 73. Workflow and Redis

Redis can support:

    Queue
    Distributed Locks
    Temporary State
    Pub/Sub
    Progress Events

Persistent workflow state should remain in MySQL.

---

# 74. Workflow and Sequelize

The backend can persist workflow entities using Sequelize.

Possible models:

    Workflow
    WorkflowStep
    WorkflowEvent
    WorkflowJob

Example relationships:

    Workflow
        hasMany
    WorkflowStep

    Workflow
        hasMany
    WorkflowEvent

---

# 75. Workflow Database Concept

Possible tables:

    workflows
    workflow_steps
    workflow_events
    workflow_jobs

These tables should remain separate from AI prompt definitions.

---

# 76. Workflow API

Possible endpoints:

    POST /workflows

    GET /workflows/:id

    POST /workflows/:id/cancel

    POST /workflows/:id/pause

    POST /workflows/:id/resume

    GET /workflows/:id/events

The exact API design belongs to the backend architecture.

---

# 77. Workflow WebSocket / SSE

The frontend may receive workflow updates through:

    WebSocket

or:

    Server-Sent Events

Example:

    workflow.progress
    workflow.completed
    workflow.failed

This allows real-time UI updates.

---

# 78. Workflow and Frontend

The frontend should display:

    Current step
    Progress
    Status
    Errors
    Pending approval
    Render progress

The frontend does not execute workflow logic.

---

# 79. Workflow and AI Context

The Context Engine should provide the workflow state to the Agent when required.

Example:

    Workflow:
    create_short

    Current Step:
    subtitle_generation

The Agent receives only relevant subtitle context.

---

# 80. Workflow and Agents

Possible architecture:

    Orchestrator Agent
        ↓
    Video Analysis Agent
        ↓
    Editing Agent
        ↓
    Subtitle Agent
        ↓
    Rendering Agent

The first MVP should preferably use fewer Agents.

---

# 81. MVP Workflow Strategy

Initial MVP should implement:

    One AI Agent
    One Workflow Engine
    Tool Execution
    Context Refresh
    Queue
    Worker
    Basic Retry
    Basic Error Handling

Avoid premature multi-agent complexity.

---

# 82. First AI Workflow

Recommended first workflow:

    User:
    "Remove silence from this video."

Flow:

    User Request
        ↓
    Video Editing Agent
        ↓
    Analyze Audio
        ↓
    Detect Silence
        ↓
    Generate Edit Plan
        ↓
    Validate Plan
        ↓
    Apply Timeline Changes
        ↓
    Verify Timeline
        ↓
    Return Result

---

# 83. Rendering Workflow

Recommended rendering flow:

    User:
    "Export this video as 1080p MP4."

    Agent
        ↓
    Validate Project
        ↓
    Create Render Job
        ↓
    Queue
        ↓
    FFmpeg Worker
        ↓
    Render
        ↓
    Validate Output
        ↓
    Store Result
        ↓
    Notify User

---

# 84. Workflow Recovery

If worker crashes:

    Worker
        ↓
    Job interrupted

Queue system detects failure.

Possible behavior:

    Retry job

or:

    Workflow → failed

depending on retry policy.

---

# 85. Workflow Checkpointing

Long workflows should save checkpoints.

Example:

    Step 1 completed
    Checkpoint saved

    Step 2 completed
    Checkpoint saved

If failure occurs:

    Resume from last valid checkpoint.

---

# 86. Workflow Checkpoint

Example:

    {
      "workflowId": "workflow-001",
      "completedSteps": [
        "analyze",
        "detect-silence"
      ],
      "currentStep": "apply-edits"
    }

---

# 87. Workflow Determinism

Whenever possible, workflow execution should be deterministic.

AI may introduce variability.

Therefore:

    AI
      ↓
    Structured Plan
      ↓
    Validated Execution

Once the plan is accepted, execution should rely on deterministic application logic.

---

# 88. AI Replanning

If execution fails because state changed:

    Tool Failure
        ↓
    Context Refresh
        ↓
    AI Replanning
        ↓
    New Plan
        ↓
    Validation
        ↓
    Execution

Replanning should be limited to avoid loops.

---

# 89. Replanning Limit

Example:

    maxReplans = 2

If the workflow still cannot proceed:

    workflow → failed

The user should receive a meaningful explanation.

---

# 90. Workflow Human-in-the-Loop

Some workflows should support human decisions.

Example:

    AI analyzes video
        ↓
    Creates editing proposal
        ↓
    User reviews
        ↓
    Approve
        ↓
    Execute

This is especially useful for creative editing.

---

# 91. Creative Workflow

Example:

    "Make this video more engaging."

Workflow:

    Analyze
        ↓
    Detect weak sections
        ↓
    Generate suggestions
        ↓
    Create editing plan
        ↓
    User approval
        ↓
    Apply edits
        ↓
    Preview
        ↓
    Render

---

# 92. Preview Before Render

For complex editing workflows:

    Plan
        ↓
    Apply Timeline Changes
        ↓
    Generate Preview
        ↓
    User Review
        ↓
    Final Render

This reduces expensive failed renders.

---

# 93. Workflow Cost Awareness

The Workflow Engine should understand expensive operations.

Examples:

    AI inference
    Transcription
    Rendering
    Video analysis

Cheap operations:

    Read metadata
    Read clip
    Read timeline

The system should optimize accordingly.

---

# 94. Workflow Model Selection

Different workflow stages may use different models.

Example:

    Intent Classification
        ↓
    Small Model

    Complex Editing Plan
        ↓
    Strong Reasoning Model

    Simple Tool Selection
        ↓
    Fast Model

Model routing should remain configurable.

---

# 95. Workflow Context Optimization

Do not send the entire workflow history to every AI call.

Instead:

    Current Step
    Relevant Previous Results
    Required Context
    Relevant Tools

This reduces token usage.

---

# 96. Workflow Memory

The workflow may maintain:

    Plan
    Decisions
    Tool Results
    Important Findings

This information should be structured rather than stored as an uncontrolled conversation.

---

# 97. Workflow Decision Log

Example:

    {
      "decision": "remove_silence",
      "reason": "Detected pauses longer than 1.5 seconds."
    }

Decision logs help debugging and explainability.

---

# 98. Workflow Explainability

For user-facing workflows, the system may expose:

    What the AI plans to do
    What has been completed
    What failed
    What requires approval

Avoid exposing private internal reasoning or chain-of-thought.

Expose concise decisions and actions instead.

---

# 99. Workflow Security Boundaries

The workflow engine must never allow:

    Arbitrary shell execution
    Arbitrary SQL
    Arbitrary filesystem access
    Unauthorized project access
    Unlimited resource usage

All execution must pass through controlled services.

---

# 100. Workflow Architecture

Final architecture:

    ┌───────────────────────┐
    │      User Request     │
    └───────────┬───────────┘
                ↓
    ┌───────────────────────┐
    │     Context Engine    │
    └───────────┬───────────┘
                ↓
    ┌───────────────────────┐
    │       AI Agent        │
    └───────────┬───────────┘
                ↓
    ┌───────────────────────┐
    │     Workflow Plan     │
    └───────────┬───────────┘
                ↓
    ┌───────────────────────┐
    │  Schema Validation    │
    └───────────┬───────────┘
                ↓
    ┌───────────────────────┐
    │ Workflow Orchestrator │
    └───────────┬───────────┘
                ↓
        ┌───────┼────────┐
        ↓       ↓        ↓
      Tools    Queue      AI
        ↓       ↓
    Services  Workers
                ↓
             FFmpeg
                ↓
        ┌──────────────┐
        │ Project State│
        └───────┬──────┘
                ↓
        Context Refresh
                ↓
          Next Step
                ↓
         Verification
                ↓
         Final Response

---

# 101. Core Principles

1. Simple tasks should remain simple.
2. Complex tasks should use workflows.
3. AI creates plans; application code executes them.
4. Every workflow must have explicit state.
5. Long-running operations must be asynchronous.
6. Heavy processing belongs in workers.
7. Workflow state must be persisted.
8. Steps should be idempotent where possible.
9. Retries must be controlled.
10. Failed workflows must be recoverable when possible.
11. Workflows must support cancellation.
12. Long-running workflows should support checkpoints.
13. Project versions must be tracked.
14. Context must be refreshed after state changes.
15. AI calls must have limits.
16. Tool calls must have limits.
17. Infinite workflow loops must be prevented.
18. High-risk operations may require human approval.
19. Parallel execution must avoid conflicting state changes.
20. Workflow execution must be observable.
21. Workflow definitions must be versioned.
22. Security must be enforced outside the AI model.
23. Expensive operations should be queued.
24. AI reasoning must remain separate from deterministic execution.
25. The workflow system must remain modular and extensible.