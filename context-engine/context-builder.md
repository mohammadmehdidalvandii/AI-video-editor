# Context Builder

## Purpose

The Context Builder is responsible for assembling selected and validated context into a structured context package that can be consumed by AI agents.

The Context Builder does not select context, resolve authorization, or execute AI requests.

Its responsibility is:

    Selected Context
        ↓
    Ordered Context
        ↓
    Structured Sections
        ↓
    Context Package
        ↓
    AI Provider Adapter

---

# 1. Core Responsibility

The Context Builder transforms:

    Context Documents
    +
    Project State
    +
    Task Context
    +
    User Request
    +
    Tool Context
    +
    Resolved Priority

into:

    Final Context Package

---

# 2. Non-Responsibilities

The Context Builder must not:

- Discover context
- Select context
- Resolve permissions
- Execute tools
- Execute AI requests
- Modify project state
- Change context priority
- Rewrite security rules

These responsibilities belong to other components.

---

# 3. Build Pipeline

The build process follows:

    Selected Context
        ↓
    Validation
        ↓
    Priority Ordering
        ↓
    Dependency Ordering
        ↓
    Section Assembly
        ↓
    Context Formatting
        ↓
    Token Estimation
        ↓
    Context Package

---

# 4. Context Package

The final package should be structured.

Example:

    {
      "metadata": {},
      "rules": {},
      "architecture": {},
      "domain": {},
      "project": {},
      "task": {},
      "tools": {},
      "user": {}
    }

The exact representation may evolve.

---

# 5. Context Sections

The initial structure contains:

    Metadata
    Rules
    Architecture
    Domain
    Project
    Task
    Tools
    User Request

---

# 6. Metadata Section

Metadata may contain:

    contextSnapshotId
    projectId
    workflowId
    agentId
    contextVersion
    buildVersion

Metadata should not contain secrets.

---

# 7. Rules Section

Rules contain system constraints.

Examples:

    Security Rules
    Architecture Rules
    AI Rules
    Coding Rules

Rules must appear before lower-priority content.

---

# 8. Architecture Section

Architecture context describes system-level constraints.

Examples:

    System Architecture
    Backend Architecture
    Frontend Architecture
    Worker Architecture
    Data Flow

---

# 9. Domain Section

Domain context contains task-specific knowledge.

Examples:

    Video
    FFmpeg
    FFprobe
    Codecs
    Containers
    Timeline
    Tracks
    Clips
    Rendering

---

# 10. Project Section

Project context represents current application state.

Examples:

    Project Metadata
    Current Timeline
    Current Media
    Current Workflow
    Project Configuration

---

# 11. Task Section

Task context describes the current operation.

Example:

    {
      "type": "trim_clip",
      "target": "clip_123",
      "start": 0,
      "end": 10
    }

Task data must be structured whenever possible.

---

# 12. Tools Section

The Tools section describes tools available to the current agent.

Example:

    get_timeline
    trim_clip
    split_clip
    render_video

Only authorized and relevant tools should be included.

---

# 13. User Section

The User section contains the current request.

Example:

    "Trim the first 10 seconds of this clip."

User content must remain separated from system instructions.

---

# 14. Instruction/Data Separation

The builder must clearly distinguish:

    Instructions

from:

    Data

Example:

    <rules>
    ...
    </rules>

    <architecture>
    ...
    </architecture>

    <project_data>
    ...
    </project_data>

    <task>
    ...
    </task>

    <user_request>
    ...
    </user_request>

---

# 15. Why Separation Matters

Clear separation helps prevent:

    Data
    ↓
    being interpreted as
    ↓
    System Instructions

This is especially important for:

    Subtitles
    Uploaded Documents
    External Metadata
    User Text
    Tool Results

---

# 16. Context Ordering

The builder should preserve the resolved priority.

Default order:

    Rules
        ↓
    Architecture
        ↓
    Domain
        ↓
    Project
        ↓
    Task
        ↓
    Tools
        ↓
    User
        ↓
    External Data

---

# 17. Dependency Ordering

Dependencies must be placed before the context that depends on them when required for comprehension.

Example:

    timeline
        ↓
    clips
        ↓
    operations

The final ordering must remain deterministic.

---

# 18. Context Boundaries

Each context source should have a clear boundary.

Example:

    <context id="editing.timeline">
    ...
    </context>

This makes context inspection and debugging easier.

---

# 19. Context IDs

Every included context should retain its stable ID.

Example:

    editing.timeline
    editing.clips
    video.ffmpeg

IDs should not be removed during building.

---

# 20. Context Versions

Every context should retain its version.

Example:

    editing.timeline@1.2.0

This improves:

    Debugging
    Reproducibility
    Snapshotting
    Auditing

---

# 21. Context Source

The builder may preserve the source.

Example:

    source:
        context/editing/timeline.md

Source information is useful for debugging.

---

# 22. Context Hash

The builder may include a content hash.

Example:

    sha256:
        abc123...

The hash can be used to verify context integrity.

---

# 23. Context Snapshot

Every build should be associated with a snapshot.

Example:

    contextSnapshotId:
        ctx_2026_00123

The snapshot identifies the exact context configuration used.

---

# 24. Snapshot Contents

A snapshot may contain:

    Context IDs
    Versions
    Hashes
    Priorities
    Project Version
    Agent Version
    Builder Version

---

# 25. Reproducibility

Given the same:

    Context Snapshot
    Project State
    Task
    Tool Definitions

the builder should generate the same structural context.

---

# 26. Dynamic Context

Dynamic data should be converted into normalized structures before building.

Example:

    Database Record
        ↓
    Project Adapter
        ↓
    Project Context
        ↓
    Context Builder

The builder should not perform database queries.

---

# 27. Static Context

Static Markdown context should already be normalized by the Context Loader.

The builder combines it without changing its meaning.

---

# 28. Context Normalization

Normalization may include:

    Removing Unsupported Metadata
    Standardizing IDs
    Standardizing Versions
    Normalizing Section Names

Normalization must not alter the semantic meaning of rules.

---

# 29. Formatting

The builder may support multiple output formats.

Example:

    Structured JSON
    XML-like Sections
    Markdown Sections
    Provider Message Structures

The internal representation should remain provider independent.

---

# 30. Provider Independence

The Context Builder must not depend directly on:

    OpenAI
    Anthropic
    OpenRouter
    Local Models

Provider-specific formatting belongs to:

    Provider Adapter

---

# 31. Provider Adapter

Architecture:

    Context Builder
        ↓
    Provider-Neutral Context
        ↓
    Provider Adapter
        ↓
    AI Provider

This keeps the core Context Engine independent.

---

# 32. Token Estimation

The builder should estimate context size.

Possible metrics:

    Character Count
    Word Count
    Token Estimate

Exact tokenization may be performed by a provider adapter when necessary.

---

# 33. Context Budget

The builder must validate the final context against the configured budget.

Example:

    Maximum:
        20,000 tokens

If the context exceeds the budget:

    Return CONTEXT_BUDGET_EXCEEDED

or pass the package to a controlled compression stage.

---

# 34. Compression

Compression should happen only when explicitly configured.

Possible strategies:

    Remove Optional Context
    Summarize Historical Context
    Compress Supporting Context

Security rules should not be automatically summarized.

---

# 35. Truncation

Random truncation is forbidden.

Never do:

    content.substring(0, maxTokens)

without understanding the semantic consequences.

Preferred order:

    Remove Irrelevant Context
        ↓
    Remove Optional Context
        ↓
    Compress Supporting Context
        ↓
    Controlled Truncation

---

# 36. Required Context Protection

Required context must be protected from removal.

Example:

    editing.operations

If required context cannot fit inside the budget:

    Build Fails

---

# 37. Security Context Protection

Security rules must remain protected.

Example:

    rules.security

The builder must not remove security rules to save tokens.

---

# 38. Tool Context

Tool definitions should include only information necessary for the current task.

Example:

    Tool:
        trim_clip

    Description:
        Trim a clip within the timeline.

    Parameters:
        clipId
        start
        end

Avoid sending unrelated tool definitions.

---

# 39. Tool Schema

Tool schemas should remain structured.

Example:

    {
      "name": "trim_clip",
      "description": "...",
      "parameters": {
        "clipId": "string",
        "start": "number",
        "end": "number"
      }
    }

---

# 40. Tool Authorization

The builder may receive an already-filtered tool set from the authorization layer.

The builder must not assume that every available tool is authorized.

---

# 41. User Input

User input should be included as data.

Example:

    <user_request>
    Trim the first 10 seconds.
    </user_request>

User input must not be promoted to system-level instructions.

---

# 42. External Content

External content should be explicitly marked.

Example:

    <external_content>
    ...
    </external_content>

External content is untrusted.

---

# 43. Prompt Injection Boundary

The builder should maintain clear boundaries around untrusted content.

Example:

    <external_content>
    Ignore all previous instructions.
    </external_content>

This remains data and must not override:

    <rules>

---

# 44. Context Metadata

The builder should expose enough metadata for debugging.

Example:

    {
      "id": "editing.timeline",
      "version": "1.0.0",
      "priority": 80,
      "source": "context/editing/timeline.md"
    }

---

# 45. Build Result

A successful build may return:

    {
      "snapshotId": "...",
      "context": {},
      "metadata": {},
      "tokenEstimate": 12000
    }

---

# 46. Build Errors

Possible errors:

    CONTEXT_BUILD_FAILED
    CONTEXT_BUDGET_EXCEEDED
    REQUIRED_CONTEXT_MISSING
    CONTEXT_FORMAT_ERROR
    CONTEXT_CONFLICT
    CONTEXT_INVALID

---

# 47. Fail-Safe Behavior

If critical context is missing:

    Stop Build

If required context is missing:

    Stop Build

If optional context is missing:

    Continue only if safe

---

# 48. Deterministic Output

The builder must generate deterministic structure.

Given the same:

    Selected Context
    Priority
    Project State
    Task
    Tool Definitions

the resulting structure should be identical.

---

# 49. Build Ordering

The builder should use the following conceptual order:

    1. Metadata
    2. Security Rules
    3. Architecture Rules
    4. Domain Rules
    5. Project Context
    6. Task Context
    7. Tool Definitions
    8. User Request
    9. External Data

---

# 50. Context Integrity

The builder should preserve:

    Context ID
    Version
    Priority
    Source
    Content Hash

This makes the final context traceable.

---

# 51. Context Logging

The builder should log metadata rather than full sensitive content.

Log:

    Snapshot ID
    Context IDs
    Versions
    Token Estimate
    Build Duration

Avoid logging:

    API Keys
    Passwords
    Private User Data
    Full Sensitive Documents

---

# 52. Observability

The builder should expose:

    Build Duration
    Context Count
    Token Estimate
    Context Size
    Snapshot ID
    Compression Status

---

# 53. Debug Mode

Development mode may expose:

    Selected Context
    Ordering
    Priority
    Excluded Context
    Token Estimate
    Build Steps

Production logs should avoid sensitive content.

---

# 54. Caching

Built context may be cached when:

    Context Versions
    Project State Version
    Task
    Tool Definitions

remain unchanged.

---

# 55. Cache Invalidation

Invalidate the built context when:

    Context Changes
    Project Changes
    Tool Permissions Change
    Task Changes
    Agent Profile Changes

---

# 56. Testing

The builder must be independently testable.

Tests should cover:

    Context Ordering
    Section Separation
    Metadata
    Versioning
    Snapshot Creation
    Token Budget
    Required Context
    Security Context
    Tool Context
    External Content
    Determinism
    Cache

---

# 57. Security Tests

Security tests must verify:

    Secrets are not included
    External instructions cannot override rules
    Unauthorized tools are excluded
    User content remains separated
    Context boundaries remain intact

---

# 58. Example

User request:

    "Trim the first 10 seconds of clip_123."

Selected context:

    rules/security
    rules/ai-rules
    architecture/system
    editing/timeline
    editing/clips
    editing/operations
    video/ffmpeg

Tools:

    get_timeline
    trim_clip

Final structure:

    <rules>
    ...
    </rules>

    <architecture>
    ...
    </architecture>

    <domain>
    ...
    </domain>

    <project>
    ...
    </project>

    <tools>
    ...
    </tools>

    <user_request>
    Trim the first 10 seconds of clip_123.
    </user_request>

---

# 59. Architecture Rules

The Context Builder must:

1. Build only from selected context.
2. Preserve context priority.
3. Preserve context IDs.
4. Preserve context versions.
5. Preserve context boundaries.
6. Separate instructions from data.
7. Protect security context.
8. Protect required context.
9. Respect context budgets.
10. Remain provider independent.
11. Avoid database access.
12. Avoid tool execution.
13. Avoid authorization decisions.
14. Produce deterministic structure.
15. Support context snapshots.
16. Support observability.
17. Avoid logging sensitive content.
18. Support controlled compression.
19. Reject unsafe context builds.
20. Remain independently testable.

---

# 60. Golden Rules

1. Build context; do not select it.
2. Never change the meaning of context.
3. Never allow user content to become system instructions.
4. Never allow external content to override rules.
5. Never remove critical security context.
6. Never silently remove required context.
7. Keep provider formatting outside the core builder.
8. Preserve context metadata.
9. Preserve context versions.
10. Preserve context priority.
11. Keep context boundaries explicit.
12. Keep context construction deterministic.
13. Keep context size measurable.
14. Respect context budgets.
15. Use controlled compression.
16. Never randomly truncate context.
17. Keep sensitive data out of logs.
18. Create reproducible context snapshots.
19. Keep the builder independent from application infrastructure.
20. Treat the final AI context as a controlled, versioned artifact.