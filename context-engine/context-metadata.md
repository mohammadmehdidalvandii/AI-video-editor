# Context Metadata

## Purpose

The Context Metadata system defines the information that describes, identifies, classifies, controls, and explains every context artifact.

Metadata allows the Context Engine to understand context without reading the entire context content.

Its goals are:

- Identify Context
- Describe Context
- Classify Context
- Define Scope
- Define Ownership
- Track Version
- Track Dependencies
- Define Authority
- Define Trust
- Define Security
- Define Freshness
- Support Discovery
- Support Selection
- Support Priority
- Support Validation
- Support Evaluation
- Support Observability
- Support Lifecycle Management

---

# 1. Core Principle

Context content answers:

    What does this context say?

Metadata answers:

    What is this context?
    Where does it belong?
    Who owns it?
    What version is it?
    How important is it?
    Can it be trusted?
    When should it be used?
    When should it expire?
    What does it depend on?

Metadata must therefore be treated as a first-class part of the Context Engine.

---

# 2. Metadata vs Content

Context:

    The application uses PostgreSQL for transactional data.

Metadata:

    id:
        database.postgresql

    category:
        architecture

    scope:
        backend

    version:
        2.0.0

    owner:
        backend-team

The metadata describes the context.

---

# 3. Metadata Identity

Every context should have a stable identity.

Example:

    id:
        architecture.database.postgresql

The identity should remain stable across compatible versions.

---

# 4. Context Name

The name should be human-readable.

Example:

    name:
        PostgreSQL Architecture

The name may change without changing the stable context ID when appropriate.

---

# 5. Description

Every context should have a concise description.

Example:

    description:
        Defines PostgreSQL usage and database architecture rules.

The description helps discovery and selection.

---

# 6. Purpose

Metadata should define why the context exists.

Example:

    purpose:
        Provide database architecture guidance for backend services.

---

# 7. Category

Contexts should be classified.

Possible categories:

    SYSTEM
    ARCHITECTURE
    DOMAIN
    PROJECT
    TASK
    RULE
    SECURITY
    TOOL
    WORKFLOW
    REFERENCE
    GENERATED
    EXPERIMENTAL

---

# 8. Subcategory

A context may define a more specific classification.

Example:

    category:
        ARCHITECTURE

    subcategory:
        DATABASE

---

# 9. Tags

Tags support discovery and filtering.

Example:

    tags:
        backend
        database
        postgresql
        architecture

Tags should supplement structured metadata rather than replace it.

---

# 10. Scope

Metadata should define where the context applies.

Possible scopes:

    GLOBAL
    ORGANIZATION
    PROJECT
    MODULE
    SERVICE
    TASK
    USER
    WORKFLOW

---

# 11. Scope Boundary

A context should define its boundaries.

Example:

    scope:
        backend

    excludedScope:
        frontend
        mobile

This prevents accidental use outside the intended domain.

---

# 12. Environment

Metadata may specify where a context is valid.

Examples:

    DEVELOPMENT
    TEST
    STAGING
    PRODUCTION

---

# 13. Owner

Every production context should have an owner.

Example:

    owner:
        backend-team

The owner is responsible for correctness and lifecycle management.

---

# 14. Maintainer

A maintainer may be responsible for operational updates.

Example:

    maintainer:
        platform-team

---

# 15. Reviewers

Critical contexts may define reviewers.

Example:

    reviewers:
        architecture-team
        security-team

---

# 16. Author

Track the creator of the context.

Example:

    createdBy:
        user_or_service

---

# 17. Creation Time

Track when the context was created.

Example:

    createdAt:
        timestamp

---

# 18. Update Time

Track the latest modification.

Example:

    updatedAt:
        timestamp

---

# 19. Review Time

Track the latest review.

Example:

    reviewedAt:
        timestamp

---

# 20. Version

Every managed context should have a version.

Example:

    version:
        2.1.0

---

# 21. Version Scheme

Semantic versioning may be used:

    MAJOR.MINOR.PATCH

Example:

    2.1.0

Meaning:

    MAJOR:
        Breaking change

    MINOR:
        Backward-compatible feature

    PATCH:
        Correction

---

# 22. Status

Metadata should expose lifecycle status.

Possible values:

    DRAFT
    REGISTERED
    APPROVED
    ACTIVE
    DEGRADED
    DEPRECATED
    RETIRED
    ARCHIVED

---

# 23. Authority

Authority determines how strongly a context can influence decisions.

Possible levels:

    SYSTEM
    POLICY
    PROJECT
    DOMAIN
    USER
    TOOL
    GENERATED
    EXTERNAL

---

# 24. Authority Rule

Authority should be explicit.

Example:

    authority:
        POLICY

Authority must not be inferred only from the context filename.

---

# 25. Trust Level

Metadata should define source trust.

Possible values:

    TRUSTED
    VERIFIED
    UNVERIFIED
    UNKNOWN
    BLOCKED

---

# 26. Security Classification

Possible values:

    PUBLIC
    INTERNAL
    CONTROLLED
    RESTRICTED
    CONFIDENTIAL

---

# 27. Access Policy

Metadata may define who can access the context.

Example:

    access:
        roles:
            backend
            architecture

---

# 28. Allowed Consumers

A context may define allowed consumers.

Examples:

    agents
    backend-services
    development-tools
    evaluation-pipeline

---

# 29. Source

Metadata should identify the context source.

Example:

    source:
        architecture/database.md

The source should link the context to its origin.

---

# 30. Source Type

Example:

    sourceType:
        FILE

Possible types:

    FILE
    GIT
    DATABASE
    API
    USER_INPUT
    SYSTEM
    GENERATED
    TOOL
    MEMORY
    EXTERNAL

---

# 31. Source Version

Track the version of the source.

Example:

    sourceVersion:
        8f91a23

---

# 32. Source Hash

A hash may identify the exact content.

Example:

    sourceHash:
        sha256:abc123...

---

# 33. Freshness

Metadata should describe freshness.

Possible values:

    FRESH
    STALE
    EXPIRED
    UNKNOWN

---

# 34. Freshness Policy

Metadata may define how long the context remains valid.

Example:

    freshness:
        maxAge:
            7d

---

# 35. Expiration

Temporary contexts may define an expiration.

Example:

    expiresAt:
        timestamp

---

# 36. Permanent Context

Contexts without an expiration may still require periodic review.

Example:

    expiresAt:
        null

    reviewInterval:
        90d

---

# 37. Dependencies

Metadata should define dependencies.

Example:

    dependencies:
        - architecture.database
        - security.authentication

Dependencies allow the Context Engine to understand relationships between contexts.

---

# 38. Dependency Type

Possible dependency types:

    REQUIRED
    OPTIONAL
    RECOMMENDED
    CONDITIONAL

---

# 39. Dependency Version

Dependencies may specify versions.

Example:

    dependencies:
        - id:
            architecture.database
          version:
            ">=2.0.0"

---

# 40. Compatibility

Metadata may define compatible systems.

Examples:

    contextEngine:
        ">=1.5.0"

    model:
        ">=5.0"

    environment:
        production

---

# 41. Incompatibility

Metadata may define incompatible contexts or versions.

Example:

    conflicts:
        - architecture.database.mysql

---

# 42. Context Conflicts

Two contexts may conflict.

Example:

    Context A:
        PostgreSQL

    Context B:
        MongoDB

The Context Engine should detect conflicts before composition.

---

# 43. Conflict Resolution Metadata

A context may define conflict behavior.

Possible strategies:

    HIGHER_AUTHORITY
    HIGHER_PRIORITY
    NEWER_VERSION
    EXPLICIT_POLICY
    MANUAL_REVIEW
    REJECT

---

# 44. Priority

Metadata may contain context priority.

Example:

    priority:
        80

Priority should remain separate from authority.

---

# 45. Selection Weight

Some systems may use a selection weight.

Example:

    selectionWeight:
        0.85

This can influence candidate ranking.

---

# 46. Relevance Hints

Metadata may provide discovery hints.

Example:

    relevance:
        backend
        database
        transactions

These hints help the Selector locate appropriate contexts.

---

# 47. Keywords

Keywords may improve retrieval.

Example:

    keywords:
        PostgreSQL
        transaction
        ACID
        database

---

# 48. Context Type

A context may have a functional type.

Examples:

    FACT
    RULE
    PROCEDURE
    CONSTRAINT
    DEFINITION
    EXAMPLE
    REFERENCE
    INSTRUCTION

---

# 49. Context Role

Metadata may define how the context participates in execution.

Examples:

    REQUIRED
    OPTIONAL
    SUPPORTING
    REFERENCE
    OVERRIDE
    FALLBACK

---

# 50. Required Context

Required context must be available before execution.

Example:

    role:
        REQUIRED

If unavailable:

    Context Construction Fails

---

# 51. Optional Context

Optional context may improve execution but is not mandatory.

Example:

    role:
        OPTIONAL

---

# 52. Fallback Context

Fallback context can be used when the primary context is unavailable.

Example:

    role:
        FALLBACK

---

# 53. Override Context

Override context can explicitly replace lower-authority information.

Override capability must be controlled by policy.

---

# 54. Token Budget

Metadata may define an expected token budget.

Example:

    tokenBudget:
        2000

This helps the Builder control context size.

---

# 55. Context Size

Track expected or measured size.

Possible metrics:

    characters
    tokens
    lines
    sections

---

# 56. Compression Policy

Metadata may define whether compression is allowed.

Example:

    compression:
        allowed: true

---

# 57. Summarization Policy

A context may allow summarization.

Example:

    summarization:
        allowed: true

Critical rules should generally preserve exact wording where required.

---

# 58. Retrieval Policy

Metadata may define retrieval behavior.

Example:

    retrieval:
        strategy:
            semantic

---

# 59. Retrieval Constraints

Possible constraints:

    maxResults
    minScore
    requiredTags
    allowedSources
    allowedVersions

---

# 60. Loading Policy

Metadata may define how the Loader should handle the context.

Example:

    loading:
        mode:
            lazy

Possible modes:

    EAGER
    LAZY
    ON_DEMAND

---

# 61. Cache Policy

Metadata may define caching.

Example:

    cache:
        enabled:
            true

        ttl:
            1h

---

# 62. Cache Key

A cache key should include enough identity information.

Example:

    contextId
    version
    sourceHash

---

# 63. Validation Policy

Metadata may specify validation requirements.

Example:

    validation:
        required:
            true

        schema:
            context-v2

---

# 64. Evaluation Policy

Metadata may specify evaluation requirements.

Example:

    evaluation:
        required:
            true

        frequency:
            release

---

# 65. Observability Policy

Metadata may define required observability.

Example:

    observability:
        tracing:
            required

        metrics:
            enabled

---

# 66. Lifecycle Policy

Metadata may define lifecycle behavior.

Example:

    lifecycle:
        reviewInterval:
            90d

        expiration:
            365d

---

# 67. Deprecation Metadata

When deprecated:

    deprecatedAt
    deprecatedBy
    reason
    replacement

---

# 68. Replacement

Example:

    replacement:
        architecture.database@3.0.0

This allows automated migration planning.

---

# 69. Migration Metadata

Metadata may define migration information.

Example:

    migration:
        from:
            1.x

        to:
            2.x

        strategy:
            manual

---

# 70. Metadata Schema Version

Metadata itself should have a schema version.

Example:

    metadataSchema:
        1.0.0

This is separate from the context version.

---

# 71. Context Version vs Metadata Schema

These are different.

Context version:

    2.1.0

Metadata schema:

    1.0.0

Context version describes the context.

Metadata schema describes how metadata is structured.

---

# 72. Metadata Validation

Metadata must be validated before the context becomes active.

Validation should check:

    Required Fields
    Field Types
    Allowed Values
    References
    Dependencies
    Security
    Version Format

---

# 73. Required Metadata

Minimum metadata should include:

    id
    name
    description
    version
    status
    source
    owner
    category

---

# 74. Optional Metadata

Optional fields may include:

    tags
    keywords
    dependencies
    conflicts
    tokenBudget
    cache
    retrieval
    expiration
    reviewers

---

# 75. Metadata Completeness

The system may calculate metadata completeness.

Example:

    completeness:
        92%

Low completeness should trigger review for critical contexts.

---

# 76. Metadata Quality

Metadata quality can be evaluated through:

    Completeness
    Accuracy
    Consistency
    Freshness
    Validity
    Ownership

---

# 77. Metadata Consistency

Metadata must remain consistent with context content.

Example:

    metadata:
        category:
            DATABASE

    content:
        frontend animation rules

This mismatch should be detected.

---

# 78. Metadata Drift

Metadata can become stale independently of context content.

Example:

    owner:
        team-a

while the actual owner is:

    team-b

This is metadata drift.

---

# 79. Metadata Change Detection

Track changes to:

    Owner
    Version
    Status
    Scope
    Authority
    Security
    Dependencies
    Priority

---

# 80. Metadata History

Important metadata changes should be preserved.

Example:

    owner:
        team-a

    changedTo:
        team-b

    changedAt:
        timestamp

---

# 81. Metadata Audit

Audit:

    Created
    Updated
    Approved
    Reviewed
    Deprecated
    Retired

---

# 82. Metadata Immutability

Some metadata fields should be immutable after release.

Examples:

    contextId
    initialSource
    creationTimestamp

Other fields may change through controlled updates.

---

# 83. Mutable Metadata

Examples:

    owner
    maintainer
    tags
    reviewDate
    status

Changes must remain auditable.

---

# 84. Metadata Security

Metadata itself may contain sensitive information.

Examples:

    Internal Paths
    Ownership Information
    Access Rules
    Security Class

Metadata must follow the same security policies as context content.

---

# 85. Secret Protection

Metadata must never contain:

    Passwords
    API Keys
    Tokens
    Private Keys

---

# 86. Metadata Visibility

Metadata visibility may differ from context visibility.

Example:

    Context:
        RESTRICTED

    Metadata:
        INTERNAL

The system must enforce appropriate access policies.

---

# 87. Metadata Indexing

Metadata should be indexed for fast queries.

Possible indexes:

    id
    category
    tags
    owner
    status
    version
    scope
    authority
    securityClass

---

# 88. Metadata Search

The system should support queries such as:

    Find all active backend contexts.

    Find all security contexts.

    Find contexts owned by backend-team.

    Find contexts requiring review.

---

# 89. Metadata Filtering

Filters may include:

    category
    scope
    owner
    status
    authority
    trust
    securityClass
    environment
    version

---

# 90. Metadata and Discovery

Discovery should primarily use metadata to identify candidate contexts.

Flow:

    Query
        ↓
    Metadata Search
        ↓
    Candidate Contexts
        ↓
    Relevance Evaluation
        ↓
    Selector

---

# 91. Metadata and Selector

The Selector may use:

    Scope
    Tags
    Keywords
    Priority
    Authority
    Freshness
    Dependencies
    Compatibility

---

# 92. Metadata and Priority

Priority may be calculated using metadata.

Possible inputs:

    Explicit Priority
    Authority
    Relevance
    Scope
    Freshness
    Required Role

---

# 93. Metadata and Loader

The Loader uses metadata to determine:

    Where to Load
    What Version
    How to Load
    Whether Cache Can Be Used
    Whether Source Is Allowed

---

# 94. Metadata and Builder

The Builder uses metadata to determine:

    Context Order
    Token Budget
    Compression
    Summarization
    Required Context

---

# 95. Metadata and Validation

Validation uses metadata to determine:

    Schema
    Dependencies
    Security
    Compatibility
    Required Fields

---

# 96. Metadata and Security

Security policies may depend on:

    Security Class
    Source
    Owner
    Environment
    Consumer
    Authority

---

# 97. Metadata and Evaluation

Evaluation may use metadata to select:

    Evaluation Dataset
    Evaluation Frequency
    Quality Threshold
    Regression Policy

---

# 98. Metadata and Observability

Observability can group metrics by:

    Context ID
    Category
    Owner
    Version
    Source
    Environment

---

# 99. Metadata and Lifecycle

Lifecycle decisions may use:

    Status
    Review Date
    Expiration
    Owner
    Version
    Replacement

---

# 100. Metadata and Versioning

Version metadata must distinguish:

    Context Version
    Source Version
    Metadata Schema Version
    Dependency Version

---

# 101. Metadata Example

Example:

    {
      "id": "architecture.database.postgresql",
      "name": "PostgreSQL Architecture",
      "description": "Defines PostgreSQL architecture rules.",
      "purpose": "Guide backend database decisions.",
      "category": "ARCHITECTURE",
      "subcategory": "DATABASE",
      "version": "2.1.0",
      "metadataSchema": "1.0.0",
      "status": "ACTIVE",
      "scope": "BACKEND",
      "environment": "PRODUCTION",
      "owner": "backend-team",
      "maintainer": "platform-team",
      "authority": "PROJECT",
      "trust": "TRUSTED",
      "securityClass": "INTERNAL",
      "source": "architecture/database/postgresql.md",
      "sourceType": "FILE",
      "tags": [
        "backend",
        "database",
        "postgresql"
      ],
      "keywords": [
        "postgresql",
        "transactions",
        "database"
      ],
      "priority": 80,
      "role": "REQUIRED",
      "tokenBudget": 2000,
      "dependencies": [
        {
          "id": "architecture.database",
          "version": ">=2.0.0",
          "type": "REQUIRED"
        }
      ],
      "validation": {
        "required": true,
        "schema": "context-v2"
      },
      "evaluation": {
        "required": true
      },
      "observability": {
        "tracing": true,
        "metrics": true
      }
    }

---

# 102. Minimal Metadata Example

A minimal context may contain:

    id:
        backend.database

    name:
        Backend Database

    description:
        Backend database architecture rules.

    version:
        1.0.0

    status:
        ACTIVE

    category:
        ARCHITECTURE

    source:
        architecture/database.md

    owner:
        backend-team

---

# 103. Metadata Normalization

Metadata should use consistent:

    Field Names
    Value Types
    Enumerations
    Version Format
    Timestamp Format

---

# 104. Timestamp Standard

Timestamps should use a consistent machine-readable format.

Example:

    2026-08-18T12:30:00Z

---

# 105. Identifier Rules

Context IDs should:

- Be stable
- Be unique
- Be predictable
- Avoid sensitive information
- Avoid unnecessary implementation details

---

# 106. Naming Convention

Recommended pattern:

    domain.subdomain.context

Examples:

    backend.database
    video.timeline
    security.authentication
    deployment.kubernetes

---

# 107. Metadata Namespaces

Large systems may use namespaces.

Example:

    project:
        video-editor

    context:
        timeline

Combined identity:

    video-editor.timeline

---

# 108. Metadata Inheritance

Metadata may support inheritance.

Example:

    project.default
        ↓
    backend.database
        ↓
    backend.postgresql

Inherited metadata should be explicit.

---

# 109. Inheritance Rules

Child metadata may:

    Inherit
    Extend
    Override

Parent metadata must not silently override explicit child values.

---

# 110. Metadata Override

Overrides should be controlled.

Example:

    Parent:
        tokenBudget:
            4000

    Child:
        tokenBudget:
            2000

The child explicitly overrides the parent.

---

# 111. Metadata Merge

When combining metadata:

    Explicit Child Value
        >
    Parent Value
        >
    System Default

unless policy specifies otherwise.

---

# 112. Metadata Defaults

Defaults may be provided for:

    Cache
    Observability
    Validation
    Lifecycle
    Security

Defaults must be documented and deterministic.

---

# 113. Metadata Conflict

If metadata fields conflict:

    Explicit Policy
        >
    Explicit Override
        >
    Child Metadata
        >
    Parent Metadata
        >
    Default

---

# 114. Metadata Dependency Graph

Metadata may form a dependency graph.

Example:

    backend
      ↓
    database
      ↓
    postgresql
      ↓
    transactions

This graph can support discovery and validation.

---

# 115. Metadata Graph Validation

The system should detect:

    Circular Dependencies
    Missing Dependencies
    Invalid Versions
    Conflicting Contexts

---

# 116. Circular Dependency

Example:

    A → B
    B → C
    C → A

This should be rejected unless explicitly supported by the architecture.

---

# 117. Metadata References

Metadata may reference:

    Contexts
    Sources
    Versions
    Policies
    Tools
    Workflows

References should use stable identifiers.

---

# 118. Broken Reference

A reference to a nonexistent context should produce:

    METADATA_REFERENCE_ERROR

---

# 119. Metadata Migration

When metadata schema changes:

    Detect Old Schema
        ↓
    Transform
        ↓
    Validate
        ↓
    Store New Schema

---

# 120. Backward Compatibility

Metadata schema changes should preserve compatibility where possible.

Breaking changes require:

    New Schema Version
    Migration
    Validation

---

# 121. Metadata Registry

A metadata registry may provide:

    Metadata Storage
    Metadata Search
    Metadata Validation
    Metadata Versioning
    Metadata History

---

# 122. Metadata API

Possible APIs:

    GET /contexts/:id/metadata
    PUT /contexts/:id/metadata
    GET /contexts/search
    GET /contexts/:id/history

---

# 123. Metadata API Security

Metadata APIs must enforce:

    Authentication
    Authorization
    Validation
    Audit Logging

---

# 124. Metadata Update

A metadata update should follow:

    Request
        ↓
    Authorization
        ↓
    Validation
        ↓
    Conflict Detection
        ↓
    Update
        ↓
    Audit
        ↓
    Observability

---

# 125. Concurrent Metadata Updates

Concurrent updates should be protected using:

    Revision
    Version
    ETag
    Optimistic Locking

---

# 126. Metadata Rollback

Metadata changes should be reversible when required.

Example:

    Metadata v5
        ↓
    Problem
        ↓
    Rollback
        ↓
    Metadata v4

---

# 127. Metadata Quality Gates

Production activation may require:

    Metadata Completeness
    Metadata Validation
    Owner Assignment
    Security Classification
    Source Verification

---

# 128. Metadata Review

Metadata should be reviewed when:

    Context Changes
    Ownership Changes
    Security Changes
    Architecture Changes
    Dependency Changes

---

# 129. Metadata Review Frequency

Review frequency may depend on criticality.

Example:

    Security Context:
        30d

    Architecture Context:
        90d

    Reference Context:
        180d

Exact intervals are configurable.

---

# 130. Metadata Metrics

Track:

    Metadata Completeness
    Metadata Validation Failures
    Metadata Drift
    Metadata Conflicts
    Metadata Update Frequency
    Metadata Review Compliance
    Missing Ownership
    Missing Source
    Missing Version

---

# 131. Metadata Health

A context metadata record may be:

    HEALTHY
    INCOMPLETE
    STALE
    INVALID
    CONFLICTED

---

# 132. Metadata Health Rules

Example:

    Missing Owner:
        INCOMPLETE

    Invalid Version:
        INVALID

    Stale Review:
        STALE

    Conflicting Dependencies:
        CONFLICTED

---

# 133. Metadata Observability

Important metadata changes should generate events.

Examples:

    metadata.created
    metadata.updated
    metadata.validated
    metadata.conflict
    metadata.invalid
    metadata.reviewed

---

# 134. Metadata Security Events

Examples:

    Unauthorized Metadata Update
    Security Classification Change
    Owner Change
    Access Policy Change

These events should be audited.

---

# 135. Metadata and Auditability

A historical execution should be able to identify:

    Context ID
    Context Version
    Metadata Version
    Source Version
    Dependencies
    Security Policy

---

# 136. Metadata Snapshot

A metadata snapshot captures the metadata state at execution time.

Example:

    context:
        backend.database

    contextVersion:
        2.1.0

    metadataSchema:
        1.0.0

    metadataHash:
        sha256:...

---

# 137. Reproducibility

Context execution should be reproducible using:

    Context Content
    Context Version
    Metadata Snapshot
    Source Snapshot
    Dependency Versions
    Configuration

---

# 138. Metadata and Context Contract

Metadata acts as the contract between:

    Source
    Loader
    Selector
    Priority
    Builder
    Validator
    Security
    Evaluator
    Lifecycle

---

# 139. Metadata Processing Flow

    Source
        ↓
    Metadata
        ↓
    Discovery
        ↓
    Selection
        ↓
    Priority
        ↓
    Loading
        ↓
    Building
        ↓
    Validation
        ↓
    Security
        ↓
    Evaluation
        ↓
    Observability

---

# 140. Architecture Rules

The Context Metadata system must:

1. Give every context a stable identity.
2. Define context purpose.
3. Define context scope.
4. Define context category.
5. Define ownership.
6. Track creation and update timestamps.
7. Track context version.
8. Track metadata schema version.
9. Track lifecycle status.
10. Track source.
11. Track source version.
12. Track authority.
13. Track trust.
14. Track security classification.
15. Track dependencies.
16. Track conflicts.
17. Support tags and keywords.
18. Support priority metadata.
19. Support relevance hints.
20. Support token budgets.
21. Support retrieval policies.
22. Support loading policies.
23. Support caching policies.
24. Support validation policies.
25. Support evaluation policies.
26. Support observability policies.
27. Support lifecycle policies.
28. Support metadata inheritance.
29. Support controlled overrides.
30. Support metadata versioning.
31. Support metadata migration.
32. Support metadata snapshots.
33. Support metadata rollback.
34. Detect metadata drift.
35. Detect broken references.
36. Detect dependency conflicts.
37. Protect metadata from unauthorized modification.
38. Prevent secrets from entering metadata.
39. Preserve metadata history.
40. Make context execution reproducible.

---

# 141. Golden Rules

1. Metadata is part of the Context Engine, not optional decoration.
2. Every context must have a stable ID.
3. Every production context must have an owner.
4. Every production context must have a version.
5. Every context must have a known source.
6. Authority must be explicit.
7. Trust must be explicit.
8. Security classification must be explicit.
9. Scope must be explicit.
10. Lifecycle status must be explicit.
11. Dependencies must be explicit.
12. Conflicts must be detectable.
13. Metadata must be validated before production activation.
14. Metadata changes must be auditable.
15. Metadata schema versions must be independent from context versions.
16. Sensitive information must never be stored as ordinary metadata.
17. Metadata must remain synchronized with context reality.
18. Metadata inheritance must be deterministic.
19. Metadata overrides must be explicit.
20. Metadata defaults must be deterministic.
21. Metadata must support discovery.
22. Metadata must support selection.
23. Metadata must support prioritization.
24. Metadata must support loading.
25. Metadata must support validation.
26. Metadata must support security.
27. Metadata must support evaluation.
28. Metadata must support observability.
29. Metadata must support lifecycle management.
30. Metadata must support reproducibility.
31. Invalid metadata must prevent unsafe context usage.
32. Metadata must remain understandable to both humans and machines.
33. The Context Engine should be able to make most context-management decisions from metadata without loading the full context content.