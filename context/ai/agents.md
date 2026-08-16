# AI Agents Context

## Purpose

This document defines the Agent architecture of the AI Video Editor.

Agents are responsible for understanding user intent, reasoning about the editing project, selecting appropriate tools, and coordinating operations.

Agents must not directly manipulate infrastructure or execute arbitrary system commands.

The AI architecture must remain separated from:

- Frontend
- Backend API
- Database
- FFmpeg
- FFprobe
- Storage
- Worker infrastructure

The Agent layer communicates with the application through controlled tools and structured schemas.

---

# 1. Agent Architecture

The basic architecture is:

    User
      ↓
    AI Interface
      ↓
    Orchestrator
      ↓
    Agent
      ↓
    Context
      ↓
    Tool
      ↓
    Application Service
      ↓
    Domain
      ↓
    Infrastructure

The Agent should reason about WHAT needs to happen.

The application determines HOW the operation is executed.

---

# 2. Why Agents?

A single large AI prompt becomes difficult to maintain as the product grows.

The system will eventually need to understand:

- Video
- Timeline
- Clips
- Tracks
- Audio
- Effects
- Rendering
- Media metadata
- User intent
- Project state

Agents provide a structured way to divide these responsibilities.

---

# 3. Agent Responsibilities

An Agent may be responsible for:

- Understanding user intent
- Analyzing project context
- Planning operations
- Selecting tools
- Calling tools
- Validating results
- Recovering from errors
- Communicating results

Agents should not directly modify database records or execute operating-system commands.

---

# 4. Agent vs Tool

An Agent reasons.

A Tool executes.

Example:

    User:
    "Remove the first 10 seconds."

    Agent:
    Understand request
        ↓
    Identify target timeline
        ↓
    Decide required operation
        ↓
    Call trim/delete tool

The Tool performs the actual operation.

---

# 5. Agent vs Service

Agent:

    Decides WHAT should happen.

Service:

    Executes business logic.

Example:

    Agent:
    "Delete the first 10 seconds of Clip A."

    Editing Service:
    Performs the timeline modification.

The Agent must never bypass the application service layer.

---

# 6. Agent Layers

The AI architecture may contain:

    Orchestrator
        ↓
    Planning Agent
        ↓
    Specialized Agents
        ↓
    Tools
        ↓
    Application Services

Specialized agents can be introduced gradually.

---

# 7. Initial MVP Agent

The first version should use a single primary agent.

Suggested name:

    VideoEditingAgent

Responsibilities:

- Understand user commands
- Read project context
- Create editing plans
- Select tools
- Execute safe editing operations
- Validate tool results

Do not create many agents before the basic architecture is stable.

---

# 8. Future Specialized Agents

The architecture may eventually support:

    VideoEditingAgent
    TimelineAgent
    MediaAnalysisAgent
    AudioAgent
    SubtitleAgent
    EffectsAgent
    RenderingAgent
    ProjectAssistantAgent

Each Agent should have a clearly defined responsibility.

---

# 9. Orchestrator

The Orchestrator coordinates Agents.

Conceptually:

    User Request
         ↓
    Orchestrator
         ↓
    Determine Agent
         ↓
    Execute Agent
         ↓
    Validate Result
         ↓
    Return Response

The Orchestrator should not contain domain-specific editing logic.

---

# 10. Agent Selection

The Orchestrator may classify requests.

Example:

    "Cut this clip"
        ↓
    VideoEditingAgent

    "Make the audio louder"
        ↓
    AudioAgent

    "Export in 1080p"
        ↓
    RenderingAgent

    "Analyze this video"
        ↓
    MediaAnalysisAgent

For the MVP, a single Agent may handle all operations.

---

# 11. Agent Context

An Agent should receive only the context necessary for the current task.

Possible context:

    Product Context
    Project Context
    Timeline Context
    Media Context
    Editing Rules
    Available Tools
    User Request

The entire project should not always be sent to the model.

---

# 12. Context Hierarchy

Suggested hierarchy:

    System Rules
        ↓
    Product Context
        ↓
    Architecture Context
        ↓
    Project Context
        ↓
    Timeline Context
        ↓
    Current Selection
        ↓
    User Request

Higher-level context defines constraints.

Lower-level context provides current state.

---

# 13. Agent Memory

Agent memory should be separated into different categories.

Possible categories:

    Short-Term Context
    Project Context
    Conversation Context
    User Preferences
    Long-Term Knowledge

The MVP should focus on:

    Conversation Context
    Project Context

Long-term memory can be introduced later.

---

# 14. Short-Term Context

Short-term context contains information required for the current operation.

Example:

    Current project:
    project-001

    Selected clip:
    clip-42

    Current timeline:
    00:00 → 02:30

This prevents unnecessary context expansion.

---

# 15. Project Context

Project context may contain:

- Project ID
- Project version
- Timeline duration
- Tracks
- Clips
- Media assets
- Current selection
- Active editing state

Example:

    {
      "projectId": "project-001",
      "version": 12,
      "duration": 150
    }

---

# 16. Selection Context

The currently selected entity is highly important.

Example:

    {
      "type": "clip",
      "id": "clip-42"
    }

The Agent can interpret:

    "Make this shorter."

as referring to the selected Clip.

---

# 17. Agent State

Agent state may include:

    currentTask
    currentPlan
    selectedEntities
    pendingToolCall
    toolResults
    validationState

Agent state should be explicit and structured.

Avoid storing important state only inside model-generated text.

---

# 18. Agent Task

A Task represents the current objective.

Example:

    {
      "type": "edit_video",
      "objective": "remove first 10 seconds",
      "target": "clip-42"
    }

The task should remain stable during execution.

---

# 19. Agent Planning

For complex requests, the Agent should create a plan before executing operations.

Example:

    User:
    "Remove the silence, add subtitles and export in 1080p."

Plan:

    1. Analyze audio
    2. Identify silent sections
    3. Remove silent sections
    4. Generate subtitles
    5. Add subtitle track
    6. Render 1080p

The plan should be structured.

---

# 20. Plan Execution

The Agent executes the plan step-by-step.

    Plan
      ↓
    Step 1
      ↓
    Tool
      ↓
    Result
      ↓
    Validate
      ↓
    Step 2
      ↓
    Tool
      ↓
    Result

If a step fails, the Agent should determine whether:

- Retry is appropriate
- Alternative tool is required
- Plan should be modified
- User clarification is required

---

# 21. Deterministic Operations

Operations that modify the project should be deterministic.

Example:

    deleteClip(clipId)

The Agent decides to call the tool.

The Tool performs the exact operation.

The model should not directly manipulate database state.

---

# 22. Agent Tool Calling

Conceptual flow:

    Agent
      ↓
    Tool Selection
      ↓
    Tool Arguments
      ↓
    Schema Validation
      ↓
    Tool Execution
      ↓
    Tool Result
      ↓
    Agent

Tool calls must use structured arguments.

---

# 23. Tool Safety

Every tool call must be validated.

Validation should check:

- Required arguments
- Argument types
- Entity ownership
- Project access
- Allowed operation
- Resource limits
- Current project state

The AI output must never be trusted automatically.

---

# 24. Agent Permissions

Agents should have explicit capabilities.

Example:

    VideoEditingAgent:

    allowed:
      - read_project
      - read_timeline
      - modify_clip
      - modify_track
      - create_render

    forbidden:
      - execute_shell
      - access_database_directly
      - access_other_users_projects
      - modify_system_configuration

---

# 25. Capability-Based Design

Instead of allowing an Agent to access everything, define capabilities.

Example:

    capabilities = [
      "timeline.read",
      "timeline.edit",
      "clip.read",
      "clip.edit",
      "render.create"
    ]

The Tool system checks capabilities before execution.

---

# 26. Agent Boundaries

Agents should never directly access:

    MySQL
    Redis
    Filesystem
    FFmpeg
    FFprobe
    Object Storage

Instead:

    Agent
      ↓
    Tool
      ↓
    Application Service
      ↓
    Repository / Infrastructure

---

# 27. Agent and Context Engineering

Context Engineering is responsible for deciding:

- What the Agent knows
- What the Agent does not know
- When context is loaded
- How context is structured
- How context is compressed
- How context is refreshed

The Agent should not receive uncontrolled project data.

---

# 28. Dynamic Context

Context should be loaded dynamically.

Example:

    User:
    "Make this clip slower."

System loads:

    Selected Clip
    Clip Properties
    Timeline Position
    Editing Rules
    Slow Motion Capability

There is no need to load:

    Entire Project History
    All Media Metadata
    All Previous Conversations

---

# 29. Context Retrieval

Possible flow:

    User Request
        ↓
    Context Resolver
        ↓
    Determine Required Context
        ↓
    Load Context
        ↓
    Build Agent Context
        ↓
    Agent

This makes the system more scalable.

---

# 30. Context Resolver

The Context Resolver determines which context is required.

Example:

    Request:
    "Export this as 4K."

Required:

    Project
    Current Version
    Render Configuration
    Available Render Presets

Not required:

    Detailed audio waveform
    Every historical edit
    All media metadata

---

# 31. Agent Input

Conceptual input:

    interface AgentInput {
      request: string;
      context: AgentContext;
      capabilities: AgentCapability[];
    }

The input should be structured before reaching the model.

---

# 32. Agent Output

Agent output should be structured.

Example:

    {
      "type": "tool_call",
      "tool": "trim_clip",
      "arguments": {
        "clipId": "clip-42",
        "start": 10,
        "end": 60
      }
    }

Avoid relying on free-form text for operations.

---

# 33. Agent Response Types

Possible response types:

    message
    tool_call
    plan
    clarification
    completion
    error

The Orchestrator should handle each type explicitly.

---

# 34. Agent Completion

When the requested operation is complete:

    {
      "type": "completion",
      "summary": "The first 10 seconds were removed."
    }

The frontend can display the user-friendly response.

---

# 35. Agent Clarification

Some requests may genuinely require clarification.

Example:

    User:
    "Make this faster."

If no target exists:

    Agent:
    "Which clip should I speed up?"

However, the system should avoid unnecessary questions when context already provides the answer.

---

# 36. Agent Error

Errors should be structured.

Example:

    {
      "type": "error",
      "code": "CLIP_NOT_FOUND",
      "message": "The selected clip no longer exists."
    }

The system should distinguish technical errors from user-facing explanations.

---

# 37. Agent Validation

After every important tool call:

    Tool Result
       ↓
    Validate
       ↓
    Update Context
       ↓
    Continue

Example:

    trim_clip
       ↓
    Result:
    clip duration = 20s
       ↓
    Validate
       ↓
    Continue

---

# 38. Agent Recovery

If a tool fails:

    Tool Failure
        ↓
    Analyze Error
        ↓
    Retry?
       / \
     Yes  No
     ↓     ↓
    Retry  Alternative / Failure

Retries must have limits.

---

# 39. Agent Retry Policy

Possible retry configuration:

    maxRetries = 2

The Agent should not repeatedly execute a destructive operation without checking state.

---

# 40. Idempotency

Tools should be designed to be idempotent where possible.

Example:

    setClipVolume(clipId, 0.5)

Calling it twice produces:

    volume = 0.5

rather than applying the operation twice.

This is safer for AI-driven execution.

---

# 41. Destructive Operations

Destructive operations require additional validation.

Examples:

- Delete Clip
- Delete Track
- Remove Media
- Clear Timeline

The Agent should verify:

- Correct target
- Correct project
- Correct version
- Operation is allowed

Undo support is strongly recommended.

---

# 42. Undo Architecture

AI operations should eventually produce reversible commands.

Example:

    AI Operation
        ↓
    Command
        ↓
    Execute
        ↓
    Store Inverse Command

Example:

    deleteClip
        ↓
    inverse:
    restoreClip

This allows user-controlled undo.

---

# 43. Command Pattern

AI editing operations should conceptually follow:

    User Request
        ↓
    Agent
        ↓
    Command
        ↓
    Command Handler
        ↓
    Domain
        ↓
    State Change

Example:

    TrimClipCommand

    {
      "clipId": "clip-42",
      "start": 10,
      "end": 60
    }

---

# 44. Agent and Commands

The Agent should generate commands indirectly through Tools.

Preferred:

    Agent
      ↓
    trim_clip Tool
      ↓
    TrimClipCommand
      ↓
    Command Handler

Avoid:

    Agent
      ↓
    Direct Database Update

---

# 45. Multi-Step Editing

Complex editing may require multiple operations.

Example:

    "Create a short video from this long video."

Possible process:

    Analyze Video
        ↓
    Identify Important Sections
        ↓
    Select Clips
        ↓
    Create Timeline
        ↓
    Add Transitions
        ↓
    Add Captions
        ↓
    Render

The Agent coordinates these operations.

---

# 46. Specialized Agent Example

A future:

    MediaAnalysisAgent

could handle:

- Scene detection
- Speech detection
- Silence detection
- Object detection
- Face detection
- Transcript analysis
- Highlight detection

It should return structured information to the main editing Agent.

---

# 47. Editing Agent

The Editing Agent handles:

- Clip operations
- Timeline operations
- Track operations
- Effects
- Transitions
- Audio editing
- Subtitle operations

It should use editing tools rather than implementing editing logic itself.

---

# 48. Rendering Agent

The Rendering Agent handles:

- Render preset selection
- Output configuration
- Render requests
- Render status
- Render cancellation

It should never execute FFmpeg directly.

---

# 49. Audio Agent

The Audio Agent may handle:

- Volume
- Mute
- Audio trimming
- Noise reduction
- Silence detection
- Music
- Audio mixing

Advanced audio processing can be added later.

---

# 50. Subtitle Agent

The Subtitle Agent may handle:

- Transcript processing
- Subtitle generation
- Subtitle timing
- Subtitle styling
- Subtitle tracks

Possible future integrations:

    Speech-to-Text
    Translation
    Caption generation

---

# 51. Agent Communication

Agents should communicate through structured messages.

Example:

    MediaAnalysisAgent
        ↓
    AnalysisResult
        ↓
    EditingAgent

Avoid uncontrolled natural-language communication between Agents.

---

# 52. Agent Result Schema

Example:

    {
      "agent": "MediaAnalysisAgent",
      "status": "completed",
      "result": {
        "scenes": [],
        "silenceSegments": []
      }
    }

Schemas provide predictable communication.

---

# 53. Multi-Agent Orchestration

Future architecture:

    User
      ↓
    Orchestrator
      ↓
    ┌─────────────────────┐
    │                     │
    ↓                     ↓
    Analysis Agent     Editing Agent
    │                     │
    ↓                     ↓
    Tools               Tools
    │                     │
    └──────────┬──────────┘
               ↓
          Rendering Agent
               ↓
             Tools

The Orchestrator controls the workflow.

---

# 54. Agent Isolation

Each Agent should have:

- Defined purpose
- Defined tools
- Defined context
- Defined capabilities
- Defined output schema

This prevents Agent responsibilities from becoming ambiguous.

---

# 55. Agent Prompt Structure

Each Agent should eventually have a dedicated system prompt.

Conceptual structure:

    Identity
    ↓
    Responsibilities
    ↓
    Constraints
    ↓
    Available Context
    ↓
    Available Tools
    ↓
    Output Rules
    ↓
    Safety Rules

Prompt definitions belong in:

    context/ai/prompts.md

---

# 56. Agent Rules

Agents must:

1. Follow system rules.
2. Respect project boundaries.
3. Use structured tools.
4. Never execute arbitrary commands.
5. Never access infrastructure directly.
6. Validate important operations.
7. Respect user permissions.
8. Avoid unnecessary tool calls.
9. Preserve project consistency.
10. Return structured results.

---

# 57. Agent Determinism

AI itself is probabilistic.

The application layer must provide deterministic boundaries.

Example:

    AI decides:
    trim clip

    Tool validates:
    clip exists

    Service executes:
    exact trim operation

This combination creates predictable behavior.

---

# 58. Agent Observability

Every Agent execution should ideally record:

- Request ID
- Project ID
- Agent
- Model
- Context version
- Tool calls
- Tool results
- Execution duration
- Token usage
- Errors

Sensitive data should not be logged unnecessarily.

---

# 59. Agent Execution Trace

Example:

    Request
      ↓
    VideoEditingAgent
      ↓
    Context Resolver
      ↓
    Tool: get_selected_clip
      ↓
    Tool Result
      ↓
    Tool: trim_clip
      ↓
    Tool Result
      ↓
    Validation
      ↓
    Completion

This trace is useful for debugging.

---

# 60. Agent Versioning

Agents should be versioned.

Example:

    VideoEditingAgent v1

When behavior changes:

    VideoEditingAgent v2

Versioning helps reproduce behavior and debug regressions.

---

# 61. Model Independence

The Agent architecture should not be tightly coupled to one AI provider.

Possible providers:

- OpenAI
- Anthropic
- OpenRouter
- Local Models
- Other compatible providers

The application should use an AI provider abstraction.

---

# 62. Model Adapter

Conceptually:

    Agent
      ↓
    LLM Provider Interface
      ↓
    Provider Adapter
      ↓
    Model

The Agent should not directly depend on provider-specific SDKs.

---

# 63. Agent Model Selection

Different tasks may use different models.

Example:

    Simple command
        ↓
    Fast model

    Complex editing plan
        ↓
    Reasoning model

    Media analysis
        ↓
    Specialized model

Model selection should be controlled by application policy.

---

# 64. Cost Control

The Agent system should track:

- Token usage
- Model usage
- Tool calls
- Execution duration

Possible strategies:

- Context compression
- Model routing
- Smaller models for simple tasks
- Caching
- Reusing existing analysis

---

# 65. Context Compression

Large projects may produce large context.

The Context Engine may compress:

    1000 Clips
        ↓
    Relevant 10 Clips

or:

    Full Timeline
        ↓
    Selected Region Summary

The Agent should receive the smallest useful context.

---

# 66. Agent Context Budget

Each Agent execution should have a context budget.

Possible dimensions:

    Token Budget
    Tool Call Budget
    Execution Time
    Context Size

The Orchestrator may terminate execution when limits are exceeded.

---

# 67. Agent Loop

Conceptual execution loop:

    Receive Request
        ↓
    Load Context
        ↓
    Reason
        ↓
    Tool Call?
      /     \
    Yes      No
     ↓        ↓
    Execute   Respond
     ↓
    Validate
     ↓
    Update Context
     ↓
    Reason Again

The loop must have a maximum number of iterations.

---

# 68. Agent Loop Limit

Example:

    maxIterations = 10

This protects the system from infinite reasoning/tool loops.

---

# 69. Tool Call Limit

Example:

    maxToolCalls = 20

Limits should be configurable according to task type.

---

# 70. Agent Timeout

Every Agent execution should have a timeout.

Example:

    Agent Timeout:
    60 seconds

Long-running tasks such as rendering should not remain inside the Agent execution loop.

The Agent should create a Render Job and return.

---

# 71. Long-Running Operations

For operations such as:

- Rendering
- Video analysis
- Transcription
- Proxy generation

The Agent should:

    Start Job
        ↓
    Receive Job ID
        ↓
    Return
        ↓
    Poll / Receive Event Later

The Agent should not block while the Worker performs the task.

---

# 72. Agent Job Pattern

Example:

    Agent
      ↓
    analyze_video
      ↓
    Job ID
      ↓
    Analysis Worker
      ↓
    Result

Then:

    Agent / Application
      ↓
    get_analysis_result
      ↓
    Result

---

# 73. Agent Consistency

The Agent must operate against a known project version.

Example:

    Agent sees:
    Project Version 20

If another operation changes the project to:

    Version 21

the Agent should detect the version mismatch before applying changes.

---

# 74. Optimistic Concurrency

Editing operations may use:

    expectedVersion

Example:

    {
      "projectId": "project-001",
      "expectedVersion": 20,
      "operation": "trim_clip"
    }

If current version is 21:

    Reject operation

This prevents accidental overwrites.

---

# 75. Agent Transaction Boundary

Multiple related editing operations may eventually be grouped into a transaction.

Example:

    Add Clip
    Add Subtitle
    Add Audio

If one critical operation fails, the application may roll back the entire operation group.

The Agent should not implement transaction logic itself.

---

# 76. Agent Testing

Agents should be tested at multiple levels.

### Unit Tests

Test:

- Context construction
- Tool selection logic
- Validation
- Error handling

### Integration Tests

Test:

- Agent → Tool
- Tool → Service
- Service → Domain

### Scenario Tests

Test complete user requests.

---

# 77. Agent Evaluation

AI behavior should be evaluated using predefined scenarios.

Examples:

    "Cut the first 10 seconds."

Expected:

    trim_clip

    "Make this louder."

Expected:

    set_volume

    "Export in 1080p."

Expected:

    create_render

Evaluation should measure:

- Correct tool
- Correct arguments
- Correct target
- No unsafe operation
- Successful completion

---

# 78. Agent Regression Testing

When prompts, models, or tools change, existing scenarios should be tested again.

Example dataset:

    100 editing requests
        ↓
    Agent v1
        ↓
    Results

After update:

    Agent v2
        ↓
    Results

Compare results to detect regressions.

---

# 79. Agent Architecture MVP

Initial MVP:

    User
      ↓
    Orchestrator
      ↓
    VideoEditingAgent
      ↓
    Context Resolver
      ↓
    Tool Registry
      ↓
    Application Services

No multi-agent architecture is required initially.

---

# 80. Agent Architecture Evolution

Phase 1:

    Single Agent

Phase 2:

    Single Agent
      +
    More Tools

Phase 3:

    Orchestrator
      +
    Specialized Agents

Phase 4:

    Multi-Agent Workflow

Phase 5:

    Autonomous Editing Workflows

The architecture should evolve incrementally.

---

# 81. Recommended Initial Agents

For the MVP:

    VideoEditingAgent

Later:

    MediaAnalysisAgent
    EditingAgent
    AudioAgent
    SubtitleAgent
    RenderingAgent

The names and boundaries may evolve based on actual product requirements.

---

# 82. Final Agent Architecture

The target architecture is:

    ┌─────────────────────┐
    │        User         │
    └──────────┬──────────┘
               ↓
    ┌─────────────────────┐
    │    Orchestrator     │
    └──────────┬──────────┘
               ↓
    ┌─────────────────────┐
    │       Agent         │
    │                     │
    │ Reasoning + Planning│
    └──────────┬──────────┘
               ↓
    ┌─────────────────────┐
    │   Context Engine    │
    └──────────┬──────────┘
               ↓
    ┌─────────────────────┐
    │    Tool Registry    │
    └──────────┬──────────┘
               ↓
    ┌─────────────────────┐
    │ Application Services│
    └──────────┬──────────┘
               ↓
    ┌─────────────────────┐
    │        Domain       │
    └──────────┬──────────┘
               ↓
    ┌─────────────────────┐
    │   Infrastructure    │
    │                     │
    │ DB / FFmpeg / Queue │
    │ Storage / Workers   │
    └─────────────────────┘

The core principle is:

    AI decides.
    Tools execute.
    Services enforce business rules.
    Domain owns application state.
    Infrastructure performs technical operations.

This separation is the foundation of the AI Video Editor's Context Engineering architecture.