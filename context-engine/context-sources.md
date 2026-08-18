# Context Sources

## Purpose

The Context Sources system defines where context originates, how it is discovered, how it is identified, and how it enters the Context Engine.

Its goals are:

- Define Context Origins
- Standardize Context Sources
- Track Source Ownership
- Validate Source Reliability
- Control Source Trust
- Support Multiple Source Types
- Track Source Version
- Detect Source Changes
- Support Source Prioritization
- Enable Reproducible Context Loading

---

# 1. Core Principle

Context must have a known origin.

Every context item should be traceable to:

    Source
    Location
    Owner
    Version
    Timestamp
    Trust Level

The Context Engine must be able to answer:

    Where did this context come from?
    Who owns it?
    When was it created?
    Which version was used?
    Can the source be trusted?
    Has the source changed?

---

# 2. Source Lifecycle

The source lifecycle is:

    Discover
        ↓
    Register
        ↓
    Validate
        ↓
    Classify
        ↓
    Index
        ↓
    Load
        ↓
    Monitor
        ↓
    Update
        ↓
    Deprecate
        ↓
    Remove

---

# 3. Source Types

Supported source types may include:

    FILE
    DIRECTORY
    DATABASE
    API
    GIT
    DOCUMENTATION
    CONFIGURATION
    CODE
    TOOL
    MODEL
    MEMORY
    USER_INPUT
    SYSTEM
    WORKFLOW
    GENERATED
    EXTERNAL

---

# 4. File Source

A file may contain context.

Examples:

    README.md
    architecture.md
    rules.md
    workflow.md

Example:

    sourceType:
        FILE

    path:
        context-engine/rules.md

---

# 5. Directory Source

A directory may contain multiple related context files.

Example:

    context/
    ├── architecture/
    ├── rules/
    ├── workflows/
    └── security/

The directory itself may be registered as a source.

---

# 6. Git Source

Git repositories may provide context.

Examples:

    Git Repository
    Git Branch
    Git Commit
    Git Tag

A context snapshot should identify the exact Git revision when reproducibility is required.

---

# 7. Git Revision

Example:

    repository:
        project/context

    revision:
        8f91a23

    branch:
        main

---

# 8. Database Source

Context may originate from a database.

Examples:

    PostgreSQL
    MySQL
    MongoDB
    Redis

Database-backed context should define:

    Database
    Collection/Table
    Query
    Version
    Owner

---

# 9. API Source

External or internal APIs may provide context.

Example:

    sourceType:
        API

    endpoint:
        /api/context/project

The source must define:

    Authentication
    Schema
    Version
    Availability

---

# 10. Documentation Source

Documentation may provide domain or system context.

Examples:

    API Documentation
    Architecture Documentation
    Product Documentation
    Technical Documentation

Documentation should have ownership and version information.

---

# 11. Code Source

Code can be a context source.

Examples:

    TypeScript
    JavaScript
    Python
    SQL
    Configuration Files

Code-derived context must distinguish:

    Source Code
    Generated Context
    Documentation

---

# 12. Configuration Source

Configuration files may provide operational context.

Examples:

    .env.example
    config.json
    vite.config.ts
    docker-compose.yml

Sensitive values must never be imported into context without explicit authorization.

---

# 13. Tool Source

Tools can provide context dynamically.

Examples:

    Git
    FFmpeg
    Database Tools
    Search Tools
    File System Tools

Tool-generated context must include tool identity and version when relevant.

---

# 14. Model Source

A model may generate context.

Example:

    sourceType:
        MODEL

Generated context should be marked as generated rather than authoritative by default.

---

# 15. Memory Source

Long-term memory may provide contextual information.

Memory context must include:

    Memory ID
    Creation Time
    Source
    Confidence
    Scope
    Expiration

---

# 16. User Input Source

User-provided information is an important context source.

Example:

    sourceType:
        USER_INPUT

User input should normally receive high relevance because it directly describes the current task.

---

# 17. System Source

System-level rules may provide context.

Examples:

    Security Policies
    Runtime Constraints
    Platform Rules
    Global Configuration

System context should have the highest authority where applicable.

---

# 18. Workflow Source

A workflow may generate context requirements.

Example:

    Video Editing Workflow

requires:

    timeline
    clips
    rendering
    codecs

---

# 19. Generated Source

Context may be generated from another context.

Example:

    source:
        architecture/backend.md

    generated:
        backend-summary.md

Generated context must preserve its upstream source reference.

---

# 20. External Source

External sources may provide:

    Documentation
    APIs
    Knowledge Bases
    Standards
    Public Data

External context must have an explicit trust policy.

---

# 21. Source Authority

Not all sources have equal authority.

Possible authority levels:

    SYSTEM
    POLICY
    PROJECT
    DOMAIN
    USER
    TOOL
    GENERATED
    EXTERNAL

---

# 22. Authority Rule

When sources conflict:

    Higher Authority
        >
    Lower Authority

Authority must not be inferred only from filename or location.

---

# 23. Source Trust

Every source may have a trust level.

Example:

    TRUSTED
    VERIFIED
    UNVERIFIED
    UNKNOWN
    BLOCKED

---

# 24. Trusted Source

Trusted sources may be used in production context workflows.

Examples:

    Approved Repository
    Validated Policy
    Controlled Database

---

# 25. Verified Source

Verified sources have passed defined validation but may not have the highest authority.

---

# 26. Unverified Source

Unverified sources may be used only when the workflow explicitly allows them.

They should not silently override trusted context.

---

# 27. Unknown Source

Unknown sources should be treated cautiously.

Possible behavior:

    Reject
    Isolate
    Require Review

---

# 28. Blocked Source

Blocked sources must not provide production context.

Examples:

    Compromised Source
    Unauthorized Repository
    Invalid Endpoint

---

# 29. Source Registration

Every managed source should be registered.

Example:

    {
      "sourceId": "src_001",
      "sourceType": "GIT",
      "name": "project-context",
      "owner": "engineering",
      "trust": "TRUSTED"
    }

---

# 30. Source ID

Every source must have a stable identifier.

Example:

    sourceId:
        src_git_project_context

---

# 31. Source Name

The name should describe the source.

Good:

    project-context-repository

Bad:

    source-1

---

# 32. Source Location

A source must define its location when applicable.

Examples:

    File Path
    Repository URL
    Database
    API Endpoint
    Bucket
    Collection

Sensitive URLs or credentials must not be exposed in logs.

---

# 33. Source Owner

Every managed source should have an owner.

Example:

    owner:
        platform-team

---

# 34. Source Maintainer

A maintainer may be responsible for operational maintenance.

Example:

    maintainer:
        context-team

---

# 35. Source Version

Sources should expose a version when possible.

Examples:

    Git Commit
    API Version
    Schema Version
    Document Version
    Tool Version

---

# 36. Source Timestamp

Track:

    createdAt
    updatedAt
    lastFetchedAt
    lastValidatedAt

---

# 37. Source Freshness

Freshness determines how current a source is.

Example:

    fresh
    stale
    expired

---

# 38. Freshness Policy

Different sources may require different freshness policies.

Example:

    Security Policy:
        Immediate

    Project Documentation:
        Weekly

    Historical Documentation:
        No Automatic Refresh

---

# 39. Source Availability

Track:

    AVAILABLE
    DEGRADED
    UNAVAILABLE

---

# 40. Source Health

Source health may combine:

    Availability
    Freshness
    Integrity
    Security
    Validation

---

# 41. Source Health Example

Example:

    source:
        project-context

    availability:
        AVAILABLE

    freshness:
        FRESH

    integrity:
        VALID

    security:
        TRUSTED

---

# 42. Source Integrity

Source integrity should be verifiable where required.

Possible mechanisms:

    Hash
    Signature
    Checksum
    Git Commit
    Version

---

# 43. Source Hash

A hash may identify the exact source content.

Example:

    hash:
        sha256:abc123...

---

# 44. Source Change Detection

The system should detect:

    Content Changes
    Version Changes
    Schema Changes
    Location Changes
    Ownership Changes

---

# 45. Change Detection

When a source changes:

    Detect
        ↓
    Identify Affected Context
        ↓
    Validate
        ↓
    Evaluate
        ↓
    Update

---

# 46. Source Dependencies

Sources may depend on other sources.

Example:

    Generated Context
        ↓
    Documentation
        ↓
    Source Code

Dependencies should be tracked.

---

# 47. Dependency Failure

If a source dependency becomes unavailable:

    Source:
        DEGRADED

or:

    Source:
        UNAVAILABLE

depending on impact.

---

# 48. Source Priority

Sources may have priority.

Example:

    System Policy:
        100

    Project Rule:
        80

    Domain Documentation:
        60

    External Reference:
        20

Priority values are configurable.

---

# 49. Source Priority vs Context Priority

Source priority determines source authority.

Context priority determines the importance of a selected context item.

These concepts must remain separate.

---

# 50. Source Selection

The Context Engine should not automatically load every source.

Selection may depend on:

    Task
    Scope
    Authority
    Trust
    Freshness
    Availability
    Security
    Cost

---

# 51. Source Filtering

Sources may be filtered before loading.

Possible filters:

    Type
    Owner
    Trust
    Security
    Version
    Freshness
    Environment

---

# 52. Environment

Sources may be scoped to environments.

Examples:

    DEVELOPMENT
    TEST
    STAGING
    PRODUCTION

---

# 53. Environment Restrictions

A development-only source must not automatically enter production context.

Example:

    source:
        local-debug-context

    environment:
        DEVELOPMENT

---

# 54. Source Access Control

Access to sources must be controlled.

Possible permissions:

    READ
    WRITE
    ADMIN
    APPROVE
    DELETE

---

# 55. Source Authentication

External sources may require:

    API Key
    OAuth
    Service Account
    SSH
    Database Credentials

Credentials must be stored outside normal context content.

---

# 56. Secret Isolation

Never store:

    API Keys
    Passwords
    Private Keys
    Tokens

inside ordinary context files.

---

# 57. Source Authorization

Authorization should verify:

    Who Requested
    Which Source
    Which Operation
    Which Environment

---

# 58. Source Security Policy

Every source may define:

    Allowed Consumers
    Allowed Environments
    Allowed Operations
    Security Class

---

# 59. Source Content Boundary

A source may contain information that should not enter context.

Examples:

    Secrets
    Private Data
    Internal Credentials
    Unrelated Files

The source loader must enforce boundaries.

---

# 60. Source Filtering Pipeline

Recommended flow:

    Source
        ↓
    Authentication
        ↓
    Authorization
        ↓
    Security Filtering
        ↓
    Validation
        ↓
    Context Extraction

---

# 61. Source Normalization

Different source formats should be normalized before entering the Context Engine.

Examples:

    Markdown
    JSON
    YAML
    SQL
    TypeScript
    API Response

can be converted into a normalized representation.

---

# 62. Source Schema

A managed source may use:

    sourceId
    sourceType
    name
    location
    owner
    version
    trust
    securityClass
    environment
    status
    createdAt
    updatedAt

---

# 63. Source Registry

The Context Engine should maintain a source registry.

The registry provides:

    Source Discovery
    Ownership
    Metadata
    Version
    Health
    Trust
    Permissions

---

# 64. Source Registry Example

Example:

    sources/
        src_project_docs
        src_backend_code
        src_git_repository
        src_ffmpeg_docs
        src_security_policy

---

# 65. Source Discovery

The discovery system should identify available sources.

Possible mechanisms:

    Directory Scan
    Git Scan
    Database Query
    API Discovery
    Registry Lookup

---

# 66. Source Indexing

Sources may be indexed for efficient discovery.

Index fields may include:

    Source ID
    Type
    Tags
    Owner
    Version
    Keywords
    Scope
    Dependencies

---

# 67. Source Tags

Sources may have tags.

Example:

    tags:
        backend
        database
        architecture

Tags should support discovery but should not replace explicit metadata.

---

# 68. Source Scope

A source should define its scope.

Example:

    scope:
        project

Possible scopes:

    GLOBAL
    ORGANIZATION
    PROJECT
    MODULE
    TASK
    USER

---

# 69. Source Visibility

Visibility may be:

    PUBLIC
    INTERNAL
    RESTRICTED
    PRIVATE

Visibility is different from authority.

---

# 70. Source Compatibility

A source may define compatible:

    Context Engine Version
    Schema Version
    Model Version
    Tool Version

---

# 71. Compatibility Failure

If a source is incompatible:

    Reject
    Transform
    Migrate
    Require Review

---

# 72. Source Transformation

A source may require transformation before use.

Example:

    Legacy Schema
        ↓
    Transformer
        ↓
    Current Context Schema

---

# 73. Transformation Tracking

Track:

    Source Version
    Transformer Version
    Output Version

---

# 74. Source Caching

Frequently accessed sources may be cached.

Cache metadata should include:

    Source ID
    Version
    Hash
    Timestamp

---

# 75. Cache Invalidation

Cache must be invalidated when:

    Source Version Changes
    Source Hash Changes
    Security Policy Changes
    Source Becomes Invalid

---

# 76. Source Snapshot

A source snapshot represents a known source state.

Example:

    sourceSnapshot:
        src_001@8f92a1

Snapshots support reproducibility.

---

# 77. Source Reproducibility

The system should be able to identify the exact source state used during context construction.

---

# 78. Source Audit

Source operations should be auditable.

Track:

    Source Created
    Source Updated
    Source Accessed
    Source Approved
    Source Blocked
    Source Removed

---

# 79. Source Usage Metrics

Track:

    Access Count
    Load Count
    Failure Count
    Average Latency
    Token Contribution
    Selection Frequency

---

# 80. Source Cost

Some sources may have access costs.

Examples:

    API Calls
    Database Queries
    External Search
    Model Generation

The Context Engine may consider cost during source selection.

---

# 81. Source Latency

Track:

    Discovery Latency
    Fetch Latency
    Transformation Latency
    Validation Latency

---

# 82. Source Reliability

Reliability can be measured through:

    Success Rate
    Availability
    Failure Rate
    Response Time

---

# 83. Source Failure Handling

Possible strategies:

    Retry
    Fallback
    Cache
    Skip
    Fail Workflow

The strategy depends on source criticality.

---

# 84. Critical Source

If a required source fails:

    Context Construction may fail.

Example:

    Security Policy Source

should not silently be skipped.

---

# 85. Optional Source

Optional source failure may allow the workflow to continue.

Example:

    External Documentation

may be skipped when unavailable.

---

# 86. Fallback Source

A source may define fallback sources.

Example:

    Primary:
        internal-documentation

    Fallback:
        approved-cache

Fallback must respect security and authority rules.

---

# 87. Source Conflict

Two sources may provide contradictory information.

Example:

    Source A:
        Node.js 22

    Source B:
        Node.js 20

The conflict must be detected.

---

# 88. Conflict Resolution

Conflict resolution may consider:

    Authority
    Version
    Freshness
    Scope
    Trust
    Explicit Policy

---

# 89. Source Conflict Logging

Every significant conflict should be observable.

Example:

    conflict:
        runtime-version

    sourceA:
        node-docs@22

    sourceB:
        legacy-docs@20

---

# 90. Source Precedence

Precedence rules must be explicit.

Example:

    Security Policy
        >
    System Configuration
        >
    Project Configuration
        >
    Documentation
        >
    External Reference

---

# 91. Source Freshness vs Authority

Freshness does not automatically override authority.

Example:

    Old Security Policy

may still outrank:

    New External Documentation

until the policy is officially updated.

---

# 92. Source Ownership Change

Ownership changes should be tracked.

Example:

    oldOwner:
        team-a

    newOwner:
        team-b

---

# 93. Source Deprecation

A source may become deprecated.

Reasons:

    Replaced
    Obsolete
    Moved
    Security Risk
    Duplicate

---

# 94. Source Replacement

Deprecated sources should define a replacement where possible.

Example:

    deprecated:
        legacy-api-docs

    replacement:
        api-docs-v2

---

# 95. Source Removal

Before removal:

    Identify Consumers
        ↓
    Identify Dependencies
        ↓
    Migrate
        ↓
    Validate
        ↓
    Remove

---

# 96. Source Archive

Removed sources may be archived when needed for:

    Audit
    Reproducibility
    Historical Analysis

---

# 97. Source Lifecycle States

Possible states:

    DISCOVERED
    REGISTERED
    VALIDATING
    ACTIVE
    DEGRADED
    BLOCKED
    DEPRECATED
    REMOVED
    ARCHIVED

---

# 98. Source State Rules

Normal production context construction should use:

    ACTIVE

Potentially:

    DEGRADED

only when explicitly allowed by policy.

Never automatically use:

    BLOCKED
    REMOVED
    ARCHIVED

---

# 99. Source Observability

The system should expose:

    Source Health
    Source Usage
    Source Errors
    Source Latency
    Source Changes
    Source Trust
    Source Selection

---

# 100. Architecture Rules

The Context Sources system must:

1. Give every managed source a stable identity.
2. Define the source type.
3. Define source ownership.
4. Track source versions.
5. Track source freshness.
6. Track source health.
7. Track source trust.
8. Track source security.
9. Support source discovery.
10. Support source indexing.
11. Support source filtering.
12. Support source access control.
13. Prevent secret leakage.
14. Detect source changes.
15. Track source dependencies.
16. Support source snapshots.
17. Support source reproducibility.
18. Support source caching.
19. Support cache invalidation.
20. Support source conflicts.
21. Define source precedence.
22. Support source fallback.
23. Support source deprecation.
24. Support source migration.
25. Support source archival.
26. Integrate with Loader.
27. Integrate with Selector.
28. Integrate with Priority.
29. Integrate with Security.
30. Integrate with Validation.
31. Integrate with Evaluation.
32. Integrate with Observability.
33. Preserve source audit history.
34. Prevent unauthorized sources from entering production context.
35. Ensure every production context can be traced back to its source.

---

# 101. Golden Rules

1. Every context must have a traceable source.
2. Every managed source must have an owner.
3. Source authority must be explicit.
4. Trust must not be assumed.
5. External sources must never silently override trusted internal sources.
6. Source freshness must be monitored.
7. Source versions must be tracked.
8. Source integrity must be verifiable where required.
9. Secrets must remain outside normal context content.
10. Source access must be authorized.
11. Source conflicts must be detectable.
12. Source precedence must be deterministic.
13. Critical source failures must not be silently ignored.
14. Optional source failures may use controlled fallback behavior.
15. Deprecated sources must have a migration path when possible.
16. Removed sources should remain archived when required for reproducibility.
17. Source snapshots must identify the exact source state.
18. Cached sources must respect version and security changes.
19. Generated context must preserve its upstream source.
20. Source metadata must remain synchronized with reality.
21. Context construction must remain traceable to its sources.
22. Source security must be enforced before context extraction.
23. Source lifecycle must be observable.
24. Source lifecycle must be auditable.
25. The Context Engine must never treat an unknown source as trusted by default.