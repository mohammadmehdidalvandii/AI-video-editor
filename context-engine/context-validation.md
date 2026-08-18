# Context Validation

## Purpose

The Context Validation system verifies that the context selected for an AI task is complete, valid, consistent, safe, and ready to be assembled.

Its responsibility is:

    Selected Context
        ↓
    Structural Validation
        ↓
    Metadata Validation
        ↓
    Dependency Validation
        ↓
    Conflict Validation
        ↓
    Security Validation
        ↓
    Budget Validation
        ↓
    Validated Context

---

# 1. Core Responsibility

The Context Validator ensures that context is safe and valid before it reaches the Context Builder.

It validates:

- Structure
- Metadata
- Versions
- Dependencies
- Priority
- Conflicts
- Required Context
- Security Boundaries
- Context Size
- Project State

---

# 2. Non-Responsibilities

The Context Validator must not:

- Select context
- Load files
- Build provider-specific prompts
- Execute AI requests
- Execute tools
- Modify project state
- Grant permissions

These responsibilities belong to other components.

---

# 3. Validation Pipeline

The validation process follows:

    Selected Context
        ↓
    Structure Validation
        ↓
    Metadata Validation
        ↓
    Version Validation
        ↓
    Dependency Validation
        ↓
    Priority Validation
        ↓
    Conflict Detection
        ↓
    Security Validation
        ↓
    Budget Validation
        ↓
    Project State Validation
        ↓
    Validation Result

---

# 4. Validation Result

The validator should return a structured result.

Example:

    {
      "valid": true,
      "errors": [],
      "warnings": [],
      "validatedContext": []
    }

---

# 5. Validation Error

Errors should be structured.

Example:

    {
      "code": "CONTEXT_VERSION_CONFLICT",
      "contextId": "editing.timeline",
      "message": "Incompatible context version."
    }

---

# 6. Validation Levels

Validation should distinguish between:

    Error
    Warning
    Information

An error prevents execution.

A warning may allow execution if the context remains safe.

---

# 7. Required Context

Required context must exist.

Example:

    editing.operations

If it is missing:

    CONTEXT_REQUIRED_MISSING

Execution must stop.

---

# 8. Critical Context

Critical rules must exist when applicable.

Examples:

    rules.security
    rules.ai-rules

Missing critical rules should fail validation.

---

# 9. Optional Context

Missing optional context may produce a warning.

Example:

    video.codecs

If it is not required for the current task:

    Warning

Execution may continue.

---

# 10. Structure Validation

Every context document must contain the required structure.

Expected fields may include:

    id
    category
    version
    priority
    content

Optional fields may include:

    dependencies
    tags
    description
    source
    hash

---

# 11. Context ID Validation

Context IDs must be:

- Non-empty
- Unique
- Stable
- Validly formatted

Example:

    editing.timeline

Invalid:

    ""

or:

    "../timeline"

---

# 12. Duplicate Context

Duplicate context IDs must be detected.

Example:

    editing.timeline
    editing.timeline

This may cause inconsistent behavior.

Validation should reject duplicates unless explicitly supported.

---

# 13. Category Validation

The category must belong to an allowed category set.

Initial categories:

    product
    architecture
    video
    editing
    ai
    rules

Future categories may be added through configuration.

---

# 14. Version Validation

Versions must follow the project versioning policy.

Example:

    1.0.0
    1.2.0
    2.0.0

Invalid versions should be rejected.

---

# 15. Version Compatibility

Context versions must be compatible with their dependencies.

Example:

    editing.operations@2.0.0

may require:

    editing.timeline@2.x

If only:

    editing.timeline@1.x

is available:

    CONTEXT_VERSION_CONFLICT

---

# 16. Dependency Validation

Every declared dependency must exist.

Example:

    editing.operations

depends on:

    editing.timeline

If the dependency does not exist:

    CONTEXT_DEPENDENCY_MISSING

---

# 17. Circular Dependency

Circular dependencies must be detected.

Example:

    A → B
    B → C
    C → A

Result:

    CONTEXT_CIRCULAR_DEPENDENCY

Validation must stop the build.

---

# 18. Dependency Ordering

Dependencies must be resolved before dependent contexts.

Example:

    timeline
        ↓
    clips
        ↓
    operations

The final context must respect the dependency graph.

---

# 19. Priority Validation

Priority must be valid.

Initial range:

    0 - 100

Examples:

    100
    90
    80

Invalid:

    -1
    150
    "high"

---

# 20. Protected Context

Protected contexts must be validated.

Example:

    rules.security

may have:

    protected: true

Protected contexts cannot be removed during budget optimization.

---

# 21. Conflict Detection

The validator must detect contradictory context.

Example:

    Context A:
    "Rendering must be asynchronous."

    Context B:
    "Rendering must be synchronous."

This should produce:

    CONTEXT_CONFLICT

---

# 22. Conflict Resolution

The validator should not silently resolve semantic conflicts.

It may report:

    Conflict
    Priority A
    Priority B
    Winning Rule
    Reason

The Priority system determines authority.

---

# 23. Same-Priority Conflict

If two contexts have equal authority:

    Priority A = 80
    Priority B = 80

and they conflict:

    Validation Error

The system should not randomly choose one.

---

# 24. Security Validation

The validator must check for:

- Secrets
- Credentials
- API keys
- Access tokens
- Private infrastructure data
- Unauthorized project data

Sensitive data must not enter AI context unless explicitly required and permitted.

---

# 25. Secret Detection

Potential secrets may be detected using:

    Pattern Matching
    Secret Scanners
    Structured Field Rules

Examples:

    API_KEY
    ACCESS_TOKEN
    PASSWORD
    DATABASE_URL

---

# 26. User Data Isolation

The validator must ensure that context belongs to the current project and user scope.

Example:

    Project A

must never contain:

    Project B private context

Violation:

    CONTEXT_ISOLATION_ERROR

---

# 27. Authorization Boundary

The validator may verify that context references are compatible with the current authorization scope.

However:

    Backend Authorization

remains the final authority.

---

# 28. External Content

External content must be marked as untrusted.

Examples:

    Uploaded Document
    Subtitle
    API Response
    Imported Metadata

External content cannot become a higher-priority instruction.

---

# 29. Prompt Injection Detection

The validator may detect suspicious patterns.

Examples:

    Ignore previous instructions
    Reveal system prompt
    Disable security
    Execute arbitrary commands

Detection is an additional safety layer.

It is not a replacement for instruction/data separation.

---

# 30. Tool Context Validation

Tools included in the context must be:

- Known
- Valid
- Authorized
- Relevant
- Schema-compatible

Unknown tools should be rejected.

---

# 31. Tool Schema Validation

Example:

    {
      "name": "trim_clip",
      "parameters": {
        "clipId": "string",
        "start": "number",
        "end": "number"
      }
    }

Required fields must be present.

---

# 32. Project State Validation

Dynamic project context should contain a valid project version.

Example:

    projectVersion:
        42

The validator can use this to detect stale state.

---

# 33. Stale Context

Context is stale when it represents an older project state.

Example:

    Context:
        projectVersion = 41

    Current:
        projectVersion = 42

Result:

    CONTEXT_STALE

The system should rebuild dynamic context when required.

---

# 34. Context Hash

Static context may include a content hash.

Example:

    sha256:
        abc123

The validator can compare the expected hash with the loaded content.

---

# 35. Hash Mismatch

If:

    Expected Hash != Actual Hash

then:

    CONTEXT_INTEGRITY_ERROR

The context should not be trusted automatically.

---

# 36. Context Source Validation

The validator should verify that the source is valid.

Example:

    context/editing/timeline.md

Invalid:

    ../../private/file

---

# 37. File Boundary

Context files must remain inside their configured context scope.

The validator should reject paths that escape the context root.

---

# 38. Context Size Validation

The validator must estimate context size.

Possible measurements:

    Characters
    Words
    Tokens

---

# 39. Context Budget

Example:

    Maximum:
        20,000 tokens

Current:

    22,000 tokens

Result:

    CONTEXT_BUDGET_EXCEEDED

The builder must not blindly send oversized context.

---

# 40. Budget Validation Strategy

When budget is exceeded:

    Identify Optional Context
        ↓
    Reduce Optional Context
        ↓
    Compress Supporting Context
        ↓
    Revalidate

Required context must remain protected.

---

# 41. Empty Context

An empty context document should be detected.

Possible result:

    Warning

or:

    Error

depending on whether the document is required.

---

# 42. Invalid Encoding

Context must use:

    UTF-8

Invalid encoding should produce:

    CONTEXT_ENCODING_ERROR

---

# 43. Invalid Metadata

Example:

    priority: "high"

when numeric priority is required.

Result:

    CONTEXT_METADATA_INVALID

---

# 44. Missing Metadata

If required metadata is missing:

    CONTEXT_METADATA_MISSING

Optional metadata may receive defaults.

---

# 45. Validation Defaults

Defaults may be applied only to optional fields.

Example:

    Missing priority

may use:

    Category Default Priority

Critical security properties should not rely on unsafe defaults.

---

# 46. Validation Order

Recommended order:

    1. Structure
    2. Identity
    3. Metadata
    4. Version
    5. Dependencies
    6. Priority
    7. Conflicts
    8. Security
    9. Integrity
    10. Project State
    11. Budget

---

# 47. Fail Fast

Critical validation failures should stop the process immediately.

Example:

    Security Context Missing

must not continue to later AI execution.

---

# 48. Warning Policy

Warnings should never hide critical errors.

Example:

    Optional Context Missing
        → Warning

    Security Context Missing
        → Error

---

# 49. Validation Report

A validation report may contain:

    Context ID
    Validation Status
    Errors
    Warnings
    Dependencies
    Version
    Priority
    Security Status
    Budget Status

---

# 50. Example Validation Report

Example:

    Context:
        editing.timeline

    Status:
        VALID

    Version:
        1.0.0

    Priority:
        80

    Dependencies:
        editing.clips

    Security:
        PASS

    Budget:
        PASS

---

# 51. Failed Validation Example

Example:

    Context:
        editing.operations

    Status:
        INVALID

    Error:
        CONTEXT_DEPENDENCY_MISSING

    Missing:
        editing.timeline

Result:

    AI execution blocked

---

# 52. Validation Snapshot

Every successful validation should be associated with:

    contextSnapshotId

The snapshot records the validated state.

---

# 53. Snapshot Integrity

The snapshot may contain:

    Context IDs
    Versions
    Hashes
    Priorities
    Project Version
    Validation Version

---

# 54. Deterministic Validation

The same:

    Context
    Metadata
    Configuration
    Project State

must produce the same validation result.

---

# 55. Validation Cache

Validation results may be cached for immutable context.

Cache invalidation occurs when:

    Context Hash Changes
    Context Version Changes
    Configuration Changes
    Dependency Changes

---

# 56. Dynamic Context Validation

Dynamic context should be validated more frequently.

Examples:

    Timeline
    Project State
    Workflow State

These can change during execution.

---

# 57. Revalidation

Revalidation should occur after important state changes.

Example:

    AI
      ↓
    trim_clip
      ↓
    Timeline Changed
      ↓
    Revalidate Context
      ↓
    Continue

---

# 58. Validation and Tools

Before executing a tool:

    Validate Context
        ↓
    Validate Tool
        ↓
    Check Authorization
        ↓
    Execute Tool

The AI plan itself is never sufficient authorization.

---

# 59. Validation and AI Output

AI-generated context or plans must be treated as untrusted input.

Before execution:

    AI Output
        ↓
    Schema Validation
        ↓
    Authorization
        ↓
    Business Rules
        ↓
    Execution

---

# 60. Testing

The validator must be independently tested.

Tests should cover:

    Required Context
    Missing Context
    Duplicate IDs
    Invalid Metadata
    Invalid Versions
    Dependency Errors
    Circular Dependencies
    Priority Errors
    Conflicts
    Secret Detection
    Isolation
    Hash Integrity
    Stale State
    Budget
    Tool Schemas
    Determinism

---

# 61. Security Tests

Security tests must verify:

    No secret leakage
    No path traversal
    No cross-project context
    No unauthorized tools
    No external instruction escalation
    No security-rule removal

---

# 62. Performance

Validation should be efficient.

Optimize:

    Metadata Parsing
    Dependency Graph Validation
    Hash Checking
    Cached Validation
    Token Estimation

Do not sacrifice correctness for minor performance improvements.

---

# 63. Architecture Rules

The Context Validator must:

1. Validate before AI execution.
2. Fail closed on critical security errors.
3. Detect missing required context.
4. Detect invalid metadata.
5. Validate versions.
6. Validate dependencies.
7. Detect circular dependencies.
8. Detect conflicts.
9. Validate context integrity.
10. Detect stale project state.
11. Validate context size.
12. Protect critical context.
13. Preserve project isolation.
14. Respect authorization boundaries.
15. Validate tool schemas.
16. Remain provider independent.
17. Produce structured errors.
18. Produce deterministic results.
19. Support snapshots.
20. Remain independently testable.

---

# 64. Golden Rules

1. Invalid context must never silently pass.
2. Critical security failures must stop execution.
3. Required context must always be present.
4. Dependencies must be resolvable.
5. Circular dependencies must fail.
6. Conflicts must be visible.
7. Same-priority conflicts must not be silently resolved.
8. Secrets must never enter AI context.
9. User/project contexts must remain isolated.
10. External content must remain untrusted.
11. Context integrity must be verifiable.
12. Stale dynamic context must be detected.
13. Context budgets must be enforced.
14. Required context must be protected.
15. Tool schemas must be validated.
16. AI output must remain untrusted.
17. Backend authorization remains authoritative.
18. Validation must be deterministic.
19. Validation failures must be observable.
20. Security must never depend solely on the AI model.