# Context Engine Architecture

## Purpose

This document defines the internal architecture of the Context Engine.

The Context Engine is responsible for transforming multiple context sources into a controlled, structured, validated input for AI agents.

---

# 1. Architecture Overview

The Context Engine follows this pipeline:

    Request
      ↓
    Context Resolver
      ↓
    Context Loader
      ↓
    Context Selector
      ↓
    Context Priority Resolver
      ↓
    Context Validator
      ↓
    Context Builder
      ↓
    Context Output
      ↓
    AI Agent

---

# 2. Main Components

The Context Engine consists of:

    Context Resolver
    Context Loader
    Context Selector
    Context Priority Resolver
    Context Validator
    Context Builder
    Context Cache
    Context Version Manager

Each component has a single primary responsibility.

---

# 3. Context Resolver

The Context Resolver determines what type of context is required for the current request.

Input:

    User Request
    Agent Type
    Project State

Output:

    Context Requirements

Example:

    User:
    "Trim the first 10 seconds of the clip."

Resolver:

    Domain:
        editing

    Required Context:
        timeline
        clips
        operations
        ffmpeg
        ai-rules

---

# 4. Context Loader

The Context Loader loads context sources identified by the resolver.

Sources may include:

    Markdown Files
    JSON
    Database
    Redis
    Project State
    Tool Definitions

The loader must not decide whether a context is relevant.

Selection belongs to the Context Selector.

---

# 5. Context Selector

The Context Selector determines which loaded context should be used.

Selection can be based on:

    Agent
    Task
    Domain
    Project
    Dependencies
    Context Priority

Example:

    Editing Task

selects:

    editing/*
    video/*
    rules/ai-rules.md

while ignoring unrelated product context.

---

# 6. Context Priority Resolver

The Priority Resolver determines the order in which context is presented.

Default priority:

    Security
        ↓
    Architecture
        ↓
    Domain
        ↓
    Project
        ↓
    User Request
        ↓
    External Content

Priority must be deterministic.

---

# 7. Context Validator

The validator checks the selected context before it reaches the AI.

Validation includes:

    Required Context
    Context Structure
    Context Version
    Dependencies
    Conflicts
    Project State
    Resource References

Invalid context must not be silently ignored.

---

# 8. Context Builder

The Context Builder creates the final AI context.

Example:

    {
      "rules": {},
      "architecture": {},
      "domain": {},
      "project": {},
      "task": {}
    }

The builder must preserve context hierarchy and priority.

---

# 9. Context Output

The final Context Output should be independent of the AI provider.

The output can later be converted into:

    OpenAI Format
    Anthropic Format
    OpenRouter Format
    Local Model Format

The Context Engine must not depend on a specific provider.

---

# 10. Static Context Pipeline

Static context follows:

    Markdown
      ↓
    Loader
      ↓
    Parser
      ↓
    Metadata
      ↓
    Cache
      ↓
    Selector

Static context should not be re-read unnecessarily.

---

# 11. Dynamic Context Pipeline

Dynamic context follows:

    Application State
      ↓
    Context Adapter
      ↓
    Normalization
      ↓
    Context Selector
      ↓
    Context Builder

Dynamic context must reflect current state.

---

# 12. Context Adapters

Adapters normalize external data into Context Engine structures.

Examples:

    ProjectContextAdapter
    TimelineContextAdapter
    MediaContextAdapter
    WorkflowContextAdapter
    ToolContextAdapter

---

# 13. Context Document

Every context document should conceptually contain:

    Metadata
    Content
    Dependencies
    Priority
    Version

Example:

    {
      "id": "editing.timeline",
      "category": "editing",
      "version": "1.0.0",
      "priority": 70,
      "dependencies": [],
      "content": "..."
    }

---

# 14. Context Categories

The initial categories are:

    product
    architecture
    video
    editing
    ai
    rules

The architecture must allow new categories later.

---

# 15. Priority Model

Context priority should use explicit values.

Example:

    security       = 100
    architecture   = 90
    domain         = 80
    project        = 70
    task           = 60
    external       = 10

The exact numeric values may evolve.

The important requirement is deterministic ordering.

---

# 16. Context Dependencies

A context document may depend on other documents.

Example:

    editing/operations.md
        ↓
    editing/clips.md
        ↓
    editing/timeline.md

Dependencies must be resolved before building the final context.

---

# 17. Dependency Resolution

The resolver should:

    Identify Dependencies
        ↓
    Load Dependencies
        ↓
    Validate Dependencies
        ↓
    Resolve Ordering
        ↓
    Build Context

Circular dependencies must be detected.

---

# 18. Circular Dependency

Example:

    A → B
    B → C
    C → A

This must produce a context error.

The Context Engine must not enter an infinite resolution loop.

---

# 19. Context Conflict

A conflict occurs when two context sources provide incompatible rules.

Example:

    Context A:
    "Timeline is immutable."

    Context B:
    "Timeline can be modified directly."

The Context Engine must detect the conflict.

---

# 20. Conflict Resolution

Conflict resolution follows priority.

Example:

    Security Rule
        >
    Architecture Rule
        >
    Domain Rule
        >
    Project Context
        >
    User Content

Lower-priority instructions cannot override higher-priority rules.

---

# 21. Context Snapshot

Every AI operation should be associated with a context snapshot.

Example:

    contextSnapshotId

The snapshot represents the context used for the operation.

---

# 22. Snapshot Contents

A snapshot may contain:

    Context Versions
    Selected Documents
    Project Version
    Tool Versions
    Agent Version

This improves reproducibility.

---

# 23. Context Reproducibility

Given:

    Same Context Snapshot
    Same Project State
    Same Tool Definitions

the application should be able to reproduce the AI execution environment as closely as practical.

AI model output itself may remain probabilistic.

---

# 24. Context Versioning

Context versions should change when meaning changes.

Example:

    editing/operations.md
    v1.0.0

changes to:

    v2.0.0

when its contract or meaning changes significantly.

---

# 25. Semantic Versioning

Where practical:

    MAJOR
    MINOR
    PATCH

can be used.

Example:

    1.0.0
    1.1.0
    1.1.1

---

# 26. Context Cache

Static context may be cached.

Example:

    Context File
        ↓
    Hash
        ↓
    Cache

If the hash has not changed, cached content may be reused.

---

# 27. Cache Invalidation

Cache must be invalidated when:

    Context Content Changes
    Context Version Changes
    Dependencies Change

---

# 28. Dynamic Context Cache

Dynamic context should use shorter cache lifetimes or explicit invalidation.

Example:

    Timeline Context

should be refreshed after:

    trim_clip
    split_clip
    move_clip
    delete_clip

---

# 29. Context Size

The Context Engine must monitor:

    Character Count
    Token Estimate
    Document Count
    Context Build Time

---

# 30. Context Budget

Each AI task may define a context budget.

Example:

    Editing Agent:
        20k tokens

    Simple Media Analysis:
        8k tokens

The selector should prioritize the most relevant context when the budget is exceeded.

---

# 31. Relevance Ranking

Future implementations may rank context using:

    Rule Priority
    Task Relevance
    Domain Relevance
    Dependency Importance
    Semantic Similarity

---

# 32. Retrieval Strategy

Initial implementation:

    Explicit Selection

Future implementation:

    Explicit Selection
        +
    Semantic Retrieval

Do not introduce vector search before the simpler approach becomes insufficient.

---

# 33. Context Compression

When context exceeds the budget:

    Low Priority Context
        ↓
    Summarize / Compress
        ↓
    Preserve High Priority Context

Security and architectural rules should not be blindly removed.

---

# 34. Context Truncation

Truncation must be controlled.

Never randomly cut context.

Preferred strategy:

    Remove Irrelevant Context
        ↓
    Compress Low Priority Context
        ↓
    Reduce Historical Context
        ↓
    Truncate Only as Last Resort

---

# 35. Historical Context

Previous operations may be included when relevant.

Example:

    User:
    "Undo what you just did."

Required context:

    Previous Operation
    Previous Timeline State
    Current Timeline State

The entire historical project state is unnecessary.

---

# 36. Project State Adapter

The Project State Adapter converts application state into AI-readable context.

Example:

    Database
        ↓
    Project Adapter
        ↓
    Structured Project Context

The AI should not receive raw database records by default.

---

# 37. Timeline State Adapter

The Timeline Adapter converts:

    Tracks
    Clips
    Effects
    Transitions

into a normalized representation.

---

# 38. Media State Adapter

Media context may include:

    Media ID
    Duration
    Resolution
    Frame Rate
    Codec
    Container
    Audio Streams
    Video Streams

Sensitive storage information must not be exposed.

---

# 39. Workflow State Adapter

Workflow context may include:

    Workflow ID
    Current Step
    Status
    Completed Steps
    Failed Steps
    Available Actions

---

# 40. Tool Context

The AI should receive only tools relevant to the current task.

Example:

    Editing Agent

may receive:

    get_timeline
    trim_clip
    split_clip
    move_clip

It should not automatically receive:

    delete_user
    change_permissions
    infrastructure_admin

---

# 41. Context and Authorization

Tool availability must respect authorization.

Flow:

    User
      ↓
    Permissions
      ↓
    Available Tools
      ↓
    Context Builder
      ↓
    AI

---

# 42. Context Security Boundary

The Context Engine must sanitize dynamic content before adding it to AI context.

Potentially dangerous content includes:

    User Text
    Subtitles
    Metadata
    External Documents
    AI Tool Results

---

# 43. Instruction/Data Separation

The final context must clearly distinguish:

    Instructions

from:

    Data

Example:

    <rules>
    ...
    </rules>

    <project_data>
    ...
    </project_data>

    <user_request>
    ...
    </user_request>

---

# 44. Context Observability

Every context build should be traceable.

Track:

    requestId
    workflowId
    agentId
    contextSnapshotId
    selectedDocuments
    versions
    tokenEstimate
    duration

---

# 45. Context Debug Mode

Development environments may expose detailed context diagnostics.

Example:

    Selected:
        editing/timeline.md
        editing/clips.md

    Ignored:
        product/users.md

    Reason:
        Not Relevant

Detailed context debugging must not expose secrets.

---

# 46. Error Handling

Context errors should be structured.

Example:

    CONTEXT_NOT_FOUND
    CONTEXT_VERSION_CONFLICT
    CONTEXT_DEPENDENCY_ERROR
    CONTEXT_VALIDATION_ERROR
    CONTEXT_SIZE_EXCEEDED

---

# 47. Fail-Safe Behavior

If required security or architecture context cannot be loaded:

    Stop AI Execution

Do not continue with incomplete critical rules.

---

# 48. Optional Context

If non-critical context is unavailable, the system may continue if the task remains safe and valid.

Example:

    Optional Historical Context Missing

may not block a simple read operation.

---

# 49. Context Engine API

The internal API may conceptually expose:

    resolve()
    load()
    select()
    validate()
    build()
    snapshot()

Implementation details may evolve.

---

# 50. Context Engine Independence

The Context Engine must not depend on:

    Express
    React
    Sequelize
    FFmpeg
    Specific AI Provider

It should operate as an independent application component.

---

# 51. Integration Boundaries

The Context Engine may integrate with:

    Application Services
    AI Service
    Project Service
    Media Service
    Timeline Service
    Workflow Service

through defined interfaces.

---

# 52. Testing

The Context Engine must be tested independently.

Tests should cover:

    Context Selection
    Priority
    Dependencies
    Conflicts
    Validation
    Versioning
    Cache
    Context Budget
    Security
    Snapshot Creation

---

# 53. Deterministic Tests

Given the same:

    Request
    Project State
    Context Versions
    Available Tools

the Context Engine should produce the same context structure.

---

# 54. Performance

Context building should be fast enough for interactive AI workflows.

Optimize:

    File Loading
    Parsing
    Dependency Resolution
    Retrieval
    Caching
    Serialization

Do not optimize prematurely.

---

# 55. Scalability

The Context Engine should eventually support:

    Multiple Agents
    Multiple Projects
    Large Context Libraries
    Dynamic Retrieval
    Vector Search
    Context Caching
    Distributed Workers

---

# 56. Future Vector Retrieval

If context grows significantly:

    User Request
        ↓
    Embedding
        ↓
    Vector Database
        ↓
    Candidate Context
        ↓
    Priority Filter
        ↓
    Context Builder

Vector search is an optimization, not the initial source of truth.

---

# 57. Provider Independence

The final context representation should be convertible to different model APIs.

Example:

    Context Engine
        ↓
    Provider Adapter
        ├── OpenRouter
        ├── Anthropic
        ├── OpenAI
        └── Local Model

---

# 58. Architecture Rules

The Context Engine must:

1. Remain provider independent.
2. Keep context selection deterministic.
3. Separate loading from selection.
4. Separate selection from validation.
5. Separate validation from building.
6. Keep static and dynamic context separate.
7. Track context versions.
8. Detect dependency conflicts.
9. Detect circular dependencies.
10. Prevent secret leakage.
11. Respect authorization.
12. Control context size.
13. Support context snapshots.
14. Detect stale project state.
15. Fail closed when critical context is unavailable.
16. Keep AI-specific formatting outside the core engine.
17. Remain independently testable.
18. Support future semantic retrieval.
19. Keep project contexts isolated.
20. Preserve context hierarchy and priority.