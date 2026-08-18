# Context Structure

## Purpose

The Context Structure defines how context is physically and logically organized inside the Context Engine.

Its goals are:

- Define Context Organization
- Define Context Components
- Standardize Context Layout
- Separate Content From Metadata
- Define Context Boundaries
- Define Relationships Between Context Components
- Support Discovery
- Support Loading
- Support Selection
- Support Composition
- Support Validation
- Support Versioning
- Support Reproducibility
- Keep Context Modular and Maintainable

---

# 1. Core Principle

Context should be structured, not dumped into a single large document.

A well-structured context should allow the system to answer:

    What is this context?
    What does it contain?
    Which part is relevant?
    Which rules apply?
    Which dependencies exist?
    Which information is authoritative?
    Which sections can be loaded independently?

---

# 2. Context Unit

The smallest managed unit of context is a Context Unit.

Example:

    context:
        backend.database

A Context Unit should have:

    Identity
    Metadata
    Content
    Relationships
    Validation Rules
    Lifecycle Information

---

# 3. Context Package

Multiple related Context Units may form a Context Package.

Example:

    backend/
    ├── database
    ├── api
    ├── authentication
    └── caching

The package represents a larger domain.

---

# 4. Context Hierarchy

Contexts may be hierarchical.

Example:

    project
      └── backend
          └── database
              └── postgresql

The hierarchy should be explicit.

---

# 5. Context Root

Every Context Package should have a root.

Example:

    context-engine/

The root defines the boundary of the Context Package.

---

# 6. Recommended Directory Structure

Example:

    context/
    ├── metadata.yaml
    ├── README.md
    ├── architecture/
    │   ├── metadata.yaml
    │   ├── backend.md
    │   └── database.md
    ├── rules/
    │   ├── metadata.yaml
    │   ├── coding.md
    │   └── security.md
    ├── workflows/
    │   ├── metadata.yaml
    │   └── deployment.md
    └── references/
        ├── metadata.yaml
        └── api.md

---

# 7. Separation of Metadata and Content

Metadata should not be mixed unnecessarily with large context content.

Example:

    metadata.yaml

contains:

    id
    version
    owner
    category
    dependencies

while:

    database.md

contains the actual context.

---

# 8. Context File

A context file contains the actual knowledge, rule, instruction, or reference.

Examples:

    architecture.md
    database.md
    security.md
    workflow.md

---

# 9. Metadata File

Metadata describes the context.

Example:

    metadata.yaml

It should not contain large amounts of contextual content.

---

# 10. README

A README may explain the purpose of a Context Package.

It should provide:

    Package Purpose
    Structure
    Usage
    Ownership
    Contribution Rules

It should not replace machine-readable metadata.

---

# 11. Context Components

A Context Unit may contain:

    Identity
    Metadata
    Content
    Rules
    Examples
    References
    Dependencies
    Constraints

Not every Context Unit requires every component.

---

# 12. Identity Component

Defines:

    id
    name
    namespace

Example:

    id:
        backend.database.postgresql

---

# 13. Content Component

Contains the actual information.

Example:

    PostgreSQL is the primary transactional database.

---

# 14. Rule Component

Defines behavior or constraints.

Example:

    All transactional writes must use PostgreSQL.

---

# 15. Constraint Component

Defines limitations.

Example:

    Maximum database connection count:
        100

---

# 16. Example Component

Examples clarify how the context should be applied.

Example:

    Correct:
        Use PostgreSQL transactions.

    Incorrect:
        Store transactional state only in memory.

Examples should not be interpreted as universal rules unless explicitly marked.

---

# 17. Reference Component

References provide supporting information.

Examples:

    Official Documentation
    Architecture Documents
    API Specifications

References should not automatically have the same authority as the primary context.

---

# 18. Dependency Component

Defines required external context.

Example:

    dependencies:
        backend.database
        security.authentication

---

# 19. Relationship Component

Contexts may have relationships.

Possible relationships:

    PARENT
    CHILD
    DEPENDS_ON
    EXTENDS
    REFERENCES
    CONFLICTS
    REPLACES
    GENERATED_FROM

---

# 20. Parent Relationship

Example:

    backend.database

is parent of:

    backend.database.postgresql

---

# 21. Child Relationship

A child context specializes a parent context.

Example:

    database

    ↓

    postgresql

---

# 22. Dependency Relationship

Example:

    api.orders

depends on:

    database.orders

---

# 23. Extension Relationship

A context may extend another context.

Example:

    backend.database

    ↓

    backend.postgresql

The child adds additional rules without duplicating the parent.

---

# 24. Reference Relationship

A context may reference another context without depending on it.

Example:

    api.orders

references:

    domain.orders

---

# 25. Conflict Relationship

Two contexts may be incompatible.

Example:

    database.postgresql

conflicts with:

    database.mongodb-primary

Conflict metadata must be explicit.

---

# 26. Replacement Relationship

A newer context may replace an older one.

Example:

    database.v1

replaced by:

    database.v2

---

# 27. Generated Relationship

A context may be generated from another source.

Example:

    database-summary

generated from:

    database-architecture

The upstream source must remain traceable.

---

# 28. Section Structure

A context document should use predictable sections.

Recommended:

    # Purpose

    # Scope

    # Rules

    # Constraints

    # Examples

    # Dependencies

    # References

---

# 29. Purpose Section

Defines why the context exists.

Example:

    ## Purpose

    Define database architecture rules for backend services.

---

# 30. Scope Section

Defines where the context applies.

Example:

    ## Scope

    Applies to backend services using PostgreSQL.

---

# 31. Rules Section

Defines explicit rules.

Example:

    ## Rules

    - Use transactions for multi-step writes.
    - Use parameterized queries.
    - Do not store secrets in source code.

---

# 32. Constraints Section

Defines limitations.

Example:

    ## Constraints

    - Maximum connection pool size: 100.
    - Production database must use TLS.

---

# 33. Examples Section

Provides concrete examples.

Example:

    ## Examples

    Correct:
        Transactional order creation.

    Incorrect:
        Multiple writes without transaction handling.

---

# 34. Dependencies Section

Defines required contexts.

Example:

    ## Dependencies

    - backend.database
    - security.secrets

---

# 35. References Section

Defines external or supporting references.

Example:

    ## References

    - PostgreSQL documentation
    - Internal database architecture

---

# 36. Atomic Context

Context should be as atomic as practical.

A context should represent one clear responsibility.

Good:

    database.transactions

Bad:

    everything-about-backend

---

# 37. Single Responsibility

Each Context Unit should have one primary responsibility.

Example:

    security.authentication

should not contain:

    database-migration
    frontend-styling
    deployment

---

# 38. Context Granularity

Context can exist at different granularities.

Example:

    Project
        ↓
    Service
        ↓
    Module
        ↓
    Feature
        ↓
    Rule

The appropriate granularity depends on retrieval and usage requirements.

---

# 39. Avoid Over-Fragmentation

Do not create unnecessary micro-contexts.

Bad:

    database
    database-name
    database-port
    database-driver
    database-host

when they always need to be loaded together.

---

# 40. Avoid Over-Consolidation

Do not place unrelated information in one context.

Bad:

    backend-everything.md

containing:

    API
    Database
    Security
    Deployment
    Frontend

---

# 41. Context Boundary

A Context Unit should have clear boundaries.

Example:

    backend.database

contains database architecture.

It does not contain:

    frontend UI
    marketing
    deployment procedures

---

# 42. Context Independence

A context should be usable independently whenever possible.

If it cannot operate independently, dependencies must be explicitly defined.

---

# 43. Dependency Closure

A context should define the dependencies required for correct interpretation.

Example:

    orders.workflow

requires:

    orders.domain
    database.transactions

---

# 44. Context Completeness

A context should contain enough information to fulfill its declared responsibility.

It should not require hidden assumptions.

---

# 45. Hidden Context

Hidden dependencies are dangerous.

Bad:

    Context A references an undocumented rule from Context B.

Good:

    Context A explicitly declares dependency on Context B.

---

# 46. Context Duplication

Avoid duplicate copies of the same rule.

Bad:

    security.md
    backend.md
    api.md

all containing different copies of the same authentication rule.

Prefer:

    security.authentication

as the authoritative context.

---

# 47. Single Source of Truth

Authoritative information should have one primary location.

Other contexts should reference it.

---

# 48. Context References

Instead of copying large content:

    See:
        security.authentication

Use references where appropriate.

---

# 49. Reference Stability

References should use stable IDs.

Prefer:

    security.authentication

over:

    ../security/authentication-v2-final.md

---

# 50. Context Namespace

Namespaces prevent naming collisions.

Example:

    video.timeline
    backend.database
    security.authentication

---

# 51. Namespace Structure

Recommended pattern:

    domain.subdomain.context

Example:

    video.editing.timeline

---

# 52. Namespace Ownership

Namespaces may correspond to teams or domains.

Example:

    backend.*

owned by:

    backend-team

---

# 53. Context Ordering

Context files should have deterministic ordering where ordering affects interpretation.

Example:

    system
        ↓
    policy
        ↓
    architecture
        ↓
    domain
        ↓
    task

---

# 54. Structural Ordering

A context document should normally follow:

    Identity
        ↓
    Purpose
        ↓
    Scope
        ↓
    Rules
        ↓
    Constraints
        ↓
    Examples
        ↓
    Dependencies
        ↓
    References

---

# 55. Rule Ordering

Rules should be ordered by authority or importance when necessary.

Example:

    Critical Security Rules
        ↓
    Required Architecture Rules
        ↓
    Recommended Practices

---

# 56. Context Composition

Multiple Context Units may be composed into one execution context.

Example:

    System
        +
    Security
        +
    Project
        +
    Task

---

# 57. Composition Boundaries

Composition should not mutate the original Context Units.

The Builder creates a runtime representation.

---

# 58. Runtime Context

Runtime context is the assembled context sent to the consumer.

Example:

    Context Units
        ↓
    Selection
        ↓
    Ordering
        ↓
    Composition
        ↓
    Runtime Context

---

# 59. Static vs Runtime Structure

Static structure:

    Files
    Metadata
    Dependencies

Runtime structure:

    Selected Context
    Ordered Context
    Composed Context
    Final Prompt

These must remain conceptually separate.

---

# 60. Context Envelope

A runtime context may have an envelope.

Example:

    {
      "contextId": "backend.database",
      "version": "2.1.0",
      "source": "architecture/database.md",
      "content": "...",
      "metadata": {}
    }

---

# 61. Content Sections

Large contexts should be divided into logical sections.

Example:

    database.md

    ├── Purpose
    ├── Architecture
    ├── Rules
    ├── Transactions
    ├── Performance
    └── Examples

---

# 62. Section IDs

Important sections may have stable IDs.

Example:

    sectionId:
        database.transactions

This allows targeted retrieval.

---

# 63. Section Metadata

Sections may define:

    id
    importance
    tags
    dependencies
    tokenCost

---

# 64. Section-Level Retrieval

The system may load only relevant sections.

Example:

    Query:
        transaction handling

Load:

    database.transactions

instead of the entire:

    database.md

---

# 65. Section-Level Priority

Sections may have different priorities.

Example:

    security.rules:
        CRITICAL

    security.examples:
        LOW

---

# 66. Section-Level Dependencies

A section may depend on another section.

Example:

    transactions

depends on:

    database.connection

---

# 67. Section-Level Validation

Important sections may have specific validation rules.

Example:

    security.rules

requires:

    security-policy-schema

---

# 68. Context Chunking

Large context may be divided into chunks.

Chunking should preserve:

    Meaning
    Section Boundaries
    References
    Metadata

---

# 69. Semantic Chunking

Prefer semantic boundaries over arbitrary character limits.

Good boundaries:

    Rule
    Concept
    Procedure
    Section
    Example

---

# 70. Chunk Identity

Each chunk may have:

    chunkId
    contextId
    sectionId
    version

Example:

    chunkId:
        database.transactions.001

---

# 71. Chunk Ordering

Chunks should preserve logical order where required.

---

# 72. Chunk Relationships

Chunks may reference:

    Previous Chunk
    Next Chunk
    Parent Section
    Dependency Section

---

# 73. Context Compression

Context may be compressed for runtime usage.

Compression should preserve critical information.

---

# 74. Compression Priority

Do not compress critical rules in a way that changes their meaning.

Example:

    Security Constraint:
        Preserve Exact Meaning

---

# 75. Context Summary

A context may have a summary.

Example:

    summary:
        PostgreSQL is the transactional database used by backend services.

Summary should not replace authoritative content when exact information is required.

---

# 76. Summary vs Source

The source remains authoritative.

The summary is derived information.

---

# 77. Derived Context

Derived context must identify its source.

Example:

    derivedFrom:
        backend.database

    transformation:
        summarization

---

# 78. Derived Context Freshness

When the source changes:

    Derived Context
        ↓
    Potentially Stale

The system should detect this.

---

# 79. Context Templates

Templates may standardize structure.

Example:

    context-template.md

Template:

    Purpose
    Scope
    Rules
    Constraints
    Examples
    Dependencies
    References

---

# 80. Structure Validation

Every context should be checked against its structural requirements.

Validation may check:

    Required Sections
    Section Order
    Metadata Presence
    References
    Dependency Declarations

---

# 81. Structural Validation Failure

Examples:

    Missing Purpose
    Missing Scope
    Missing Metadata
    Broken Dependency
    Invalid Section

Result:

    STRUCTURE_INVALID

---

# 82. Schema

A Context Structure Schema may define:

    Required Fields
    Allowed Sections
    Allowed Relationships
    Allowed Metadata
    Naming Rules

---

# 83. Schema Version

Structure schemas should have versions.

Example:

    contextStructureSchema:
        2.0.0

---

# 84. Structure Evolution

When structure changes:

    Old Structure
        ↓
    Migration
        ↓
    New Structure
        ↓
    Validation

---

# 85. Backward Compatibility

Structural changes should preserve compatibility where possible.

Breaking structural changes require:

    New Schema Version
    Migration
    Validation

---

# 86. Context Packaging

Contexts may be packaged as:

    Directory
    Repository
    Archive
    Database Record
    API Resource

The logical structure should remain consistent regardless of packaging.

---

# 87. Context Manifest

A package may contain a manifest.

Example:

    context-manifest.yaml

The manifest may define:

    Package ID
    Version
    Contexts
    Dependencies
    Schema Version

---

# 88. Manifest Example

Example:

    package:
        id: video-editor
        version: 1.0.0

    contexts:
        - video.timeline
        - video.clips
        - video.rendering

---

# 89. Context Graph

The complete Context Engine can be represented as a graph.

Example:

    video
      │
      ├── timeline
      │     └── clips
      │
      ├── rendering
      │     └── ffmpeg
      │
      └── export

Nodes represent contexts.

Edges represent relationships.

---

# 90. Graph Relationships

Possible edges:

    DEPENDS_ON
    EXTENDS
    REFERENCES
    CONFLICTS
    REPLACES
    GENERATED_FROM

---

# 91. Graph Validation

The system should detect:

    Circular Dependencies
    Missing Nodes
    Broken References
    Conflicting Nodes
    Orphan Contexts

---

# 92. Orphan Context

An orphan context has no meaningful relationship with the Context Engine.

It may indicate:

    Missing Metadata
    Missing Dependency
    Incorrect Registration

---

# 93. Context Index

The structure may support an index.

Example:

    index.yaml

    contexts:
        - video.timeline
        - video.clips
        - video.rendering

The index improves discovery.

---

# 94. Context Manifest vs Index

Manifest defines package structure.

Index supports discovery.

They should not be treated as identical.

---

# 95. File Naming

Use predictable names.

Good:

    architecture.md
    metadata.yaml
    rules.md
    workflow.md

Avoid:

    final.md
    new-final.md
    final-v2-real.md

---

# 96. Directory Naming

Directory names should represent domains or responsibilities.

Good:

    architecture/
    security/
    workflows/

Bad:

    misc/
    stuff/
    other/

---

# 97. File Naming and Identity

File names should not be the primary identity.

The stable Context ID is the identity.

---

# 98. Moving Context

A context may move to another directory without changing its ID.

Example:

    old:
        architecture/database.md

    new:
        backend/database.md

Context ID remains:

    backend.database

if the logical identity has not changed.

---

# 99. Structural Refactoring

Refactoring may include:

    Split Context
    Merge Context
    Move Context
    Rename Context
    Reorganize Sections

All changes must preserve references where possible.

---

# 100. Splitting Context

Large context may be split.

Example:

    backend.md

becomes:

    backend/
    ├── api.md
    ├── database.md
    └── caching.md

References must be migrated.

---

# 101. Merging Context

Small contexts may be merged when they always operate together.

Example:

    database.connection
    database.pool

may become:

    database.connection-pool

when independent retrieval provides no value.

---

# 102. Merge Rules

Before merging:

    Analyze Consumers
    Analyze Dependencies
    Analyze Retrieval
    Analyze Ownership
    Validate New Structure

---

# 103. Context Duplication Detection

The system should detect highly duplicated context.

Possible signals:

    Similar Content
    Same Rules
    Same Purpose
    Same Source

---

# 104. Duplicate Resolution

Possible actions:

    Merge
    Reference
    Deprecate Duplicate
    Keep Explicitly Separate

---

# 105. Structural Consistency

Contexts within the same domain should follow consistent structure.

Example:

    backend.api
    backend.database
    backend.cache

should use similar organizational conventions where practical.

---

# 106. Structural Exceptions

Exceptions may exist for special context types.

Example:

    SECURITY_POLICY

may require stricter structure than:

    REFERENCE

Exceptions must be explicit.

---

# 107. Context Type Structure

Different context types may have different required sections.

Example:

    RULE:

        Purpose
        Rule
        Scope
        Exceptions

    PROCEDURE:

        Purpose
        Preconditions
        Steps
        Validation

---

# 108. Rule Context Structure

Example:

    # Purpose

    # Scope

    # Rules

    # Exceptions

    # Examples

---

# 109. Procedure Context Structure

Example:

    # Purpose

    # Preconditions

    # Steps

    # Validation

    # Failure Handling

---

# 110. Reference Context Structure

Example:

    # Purpose

    # Source

    # Description

    # Relevant Sections

    # Limitations

---

# 111. Security Context Structure

Example:

    # Purpose

    # Scope

    # Threats

    # Rules

    # Constraints

    # Exceptions

    # Validation

Security context should use stricter validation.

---

# 112. Workflow Context Structure

Example:

    # Purpose

    # Trigger

    # Preconditions

    # Steps

    # Dependencies

    # Failure Handling

    # Outputs

---

# 113. Architecture Context Structure

Example:

    # Purpose

    # Scope

    # Components

    # Responsibilities

    # Dependencies

    # Constraints

    # Decisions

---

# 114. Decision Records

Architecture contexts may include decisions.

Example:

    Decision:
        PostgreSQL is used for transactional storage.

    Reason:
        Strong transactional guarantees.

---

# 115. Context Invariants

A context may define invariants.

Example:

    Invariant:
        Every order must have exactly one customer.

Invariants should be clearly distinguishable from recommendations.

---

# 116. Normative vs Informational Content

Structure should distinguish:

    MUST
    MUST NOT
    SHOULD
    SHOULD NOT
    MAY

Normative statements should not be hidden inside examples or references.

---

# 117. Context Priority Markers

Critical sections may be marked.

Example:

    CRITICAL
    HIGH
    NORMAL
    LOW

Priority markers must integrate with the Priority system.

---

# 118. Context Security Markers

Sensitive sections may have additional classification.

Example:

    PUBLIC
    INTERNAL
    RESTRICTED

Section-level security must not weaken context-level security.

---

# 119. Context Visibility

Structure should support visibility boundaries.

Example:

    public/
    internal/
    restricted/

Visibility must be enforced by Security, not only directory naming.

---

# 120. Context Readability

Context should remain understandable to humans.

Structure should favor:

    Clear Headings
    Short Sections
    Explicit Rules
    Predictable Organization
    Stable References

---

# 121. Machine Readability

Context should also be easy for machines to process.

Use:

    Stable IDs
    Structured Metadata
    Explicit Relationships
    Consistent Sections
    Predictable Naming

---

# 122. Human vs Machine Optimization

Do not optimize structure exclusively for machines.

The same structure should remain readable by humans.

---

# 123. Context Density

Avoid excessive information density.

Bad:

    One paragraph containing multiple unrelated rules.

Better:

    Separate rules
    Separate constraints
    Separate examples

---

# 124. Context Noise

Remove:

    Repetition
    Unrelated Details
    Temporary Notes
    Ambiguous Statements
    Duplicate Rules

---

# 125. Context Signal

High-value information should be structurally visible.

Examples:

    Rules
    Constraints
    Requirements
    Dependencies
    Exceptions

---

# 126. Exceptions

Exceptions should be explicitly defined.

Example:

    Rule:
        All production writes require transactions.

    Exception:
        Read-only operations do not require transactions.

---

# 127. Contradiction Prevention

A context should avoid contradictory statements.

Example:

    Rule A:
        Use PostgreSQL.

    Rule B:
        Use MongoDB as the primary transactional database.

This should be detected before activation.

---

# 128. Structural Contradiction

Contradictions may exist between:

    Sections
    Contexts
    Metadata
    Dependencies

Validation should detect them where possible.

---

# 129. Context Ordering and Precedence

Structure defines organization.

Priority defines selection importance.

Authority defines influence.

These concepts must remain separate.

---

# 130. Context Structure and Sources

Sources provide origin.

Structure defines organization.

Example:

    Source:
        Git Repository

    Structure:
        architecture/
        rules/
        workflows/

---

# 131. Context Structure and Metadata

Metadata describes the structure.

Example:

    contextType:
        RULE

    sections:
        Purpose
        Rules
        Exceptions

---

# 132. Context Structure and Discovery

Discovery should use:

    IDs
    Names
    Tags
    Sections
    Relationships
    Keywords

---

# 133. Context Structure and Loader

Loader should understand:

    Package
    Manifest
    Metadata
    File
    Section
    Chunk

---

# 134. Context Structure and Selector

Selector may select:

    Entire Context
    Section
    Chunk

depending on granularity.

---

# 135. Context Structure and Builder

Builder combines selected structural units while preserving:

    Ordering
    References
    Metadata
    Authority
    Priority

---

# 136. Context Structure and Validation

Validation checks:

    Schema
    Required Sections
    Relationships
    References
    Dependencies
    Naming

---

# 137. Context Structure and Versioning

Structural changes may require:

    Patch
    Minor
    Major

depending on compatibility impact.

---

# 138. Context Structure and Lifecycle

Structural refactoring should follow lifecycle rules.

Example:

    Draft
        ↓
    Refactor
        ↓
    Validate
        ↓
    Evaluate
        ↓
    Review
        ↓
    Activate

---

# 139. Context Structure and Maintenance

Maintenance handles:

    Reorganization
    Splitting
    Merging
    Cleanup
    Duplicate Removal

---

# 140. Context Structure and Evaluation

Evaluation should determine whether structural changes improve:

    Retrieval
    Relevance
    Accuracy
    Token Efficiency
    Task Success

---

# 141. Context Structure and Observability

Observability should track:

    Context Size
    Section Usage
    Chunk Usage
    Retrieval Frequency
    Structural Errors

---

# 142. Context Structure and Security

Security controls:

    Access
    Visibility
    Classification
    Section Protection
    Source Authorization

Structure itself must never be treated as a security boundary.

---

# 143. Context Structure Contract

Every production Context Package should define:

    Root
    Context Units
    Metadata
    Relationships
    Dependencies
    Schema Version

---

# 144. Context Package Example

Example:

    video-editor/
    │
    ├── metadata.yaml
    ├── manifest.yaml
    │
    ├── architecture/
    │   ├── metadata.yaml
    │   ├── system.md
    │   └── pipeline.md
    │
    ├── domain/
    │   ├── metadata.yaml
    │   ├── timeline.md
    │   └── clips.md
    │
    ├── rules/
    │   ├── metadata.yaml
    │   ├── editing.md
    │   └── security.md
    │
    ├── workflows/
    │   ├── metadata.yaml
    │   └── rendering.md
    │
    └── references/
        ├── metadata.yaml
        └── ffmpeg.md

---

# 145. Context Unit Example

Example:

    domain/timeline.md

Metadata:

    id:
        video.timeline

    type:
        DOMAIN

    version:
        1.2.0

    owner:
        editing-team

    scope:
        video-editor

Content:

    # Purpose

    Defines timeline behavior.

    # Rules

    Every clip must have a start time.

    # Constraints

    Clips must not overlap when overlap is disabled.

---

# 146. Context Package Manifest Example

Example:

    package:
        id:
            video-editor

        version:
            1.0.0

        schema:
            2.0.0

    contexts:
        - video.timeline
        - video.clips
        - video.rendering

    dependencies:
        - video.ffmpeg

---

# 147. Structural Validation Checklist

Before activation verify:

    [ ] Context ID exists
    [ ] Metadata exists
    [ ] Purpose exists
    [ ] Scope exists
    [ ] Content exists
    [ ] Required sections exist
    [ ] Dependencies are declared
    [ ] References are valid
    [ ] Relationships are valid
    [ ] No circular dependencies
    [ ] No unresolved references
    [ ] Naming follows conventions
    [ ] Schema version is supported
    [ ] Security classification exists
    [ ] Owner exists

---

# 148. Structural Quality Checklist

A high-quality context should have:

    Clear Responsibility
    Clear Boundaries
    Explicit Dependencies
    Stable Identity
    Predictable Structure
    Minimal Duplication
    High Signal
    Low Noise
    Human Readability
    Machine Readability

---

# 149. Architecture Rules

The Context Structure system must:

1. Define a clear Context Unit.
2. Define Context Packages.
3. Define Context Hierarchy.
4. Define Context Boundaries.
5. Separate Metadata from Content.
6. Support stable Context IDs.
7. Support namespaces.
8. Support structured sections.
9. Support section-level retrieval.
10. Support semantic chunking.
11. Support explicit relationships.
12. Support dependencies.
13. Support references.
14. Support conflicts.
15. Support replacements.
16. Support generated contexts.
17. Prevent hidden dependencies.
18. Minimize duplication.
19. Maintain a single source of truth.
20. Support context composition.
21. Support runtime context generation.
22. Support structural schemas.
23. Support schema versioning.
24. Support structural migration.
25. Support manifests.
26. Support indexes.
27. Support context graphs.
28. Detect circular dependencies.
29. Detect broken references.
30. Detect orphan contexts.
31. Support context splitting.
32. Support context merging.
33. Support structural refactoring.
34. Support context snapshots.
35. Preserve reproducibility.
36. Support human readability.
37. Support machine readability.
38. Support security boundaries through the Security system.
39. Integrate with Sources.
40. Integrate with Metadata.
41. Integrate with Discovery.
42. Integrate with Loader.
43. Integrate with Selector.
44. Integrate with Priority.
45. Integrate with Builder.
46. Integrate with Validation.
47. Integrate with Versioning.
48. Integrate with Evaluation.
49. Integrate with Maintenance.
50. Integrate with Lifecycle.
51. Integrate with Observability.

---

# 150. Golden Rules

1. One Context Unit should have one primary responsibility.
2. Context must have clear boundaries.
3. Metadata and content should remain conceptually separate.
4. Stable IDs are more important than file names.
5. Context dependencies must be explicit.
6. Hidden dependencies are not allowed.
7. The authoritative rule should have one primary source.
8. References should use stable IDs.
9. Context structure should be deterministic.
10. Structure should be understandable by both humans and machines.
11. Large contexts should be divided into meaningful sections.
12. Semantic boundaries are preferred over arbitrary chunks.
13. Critical rules must remain distinguishable from examples.
14. Examples must not silently become rules.
15. Exceptions must be explicit.
16. Normative statements must be clearly identifiable.
17. Context structure must support selective loading.
18. Context structure must support selective retrieval.
19. Structural changes must be validated.
20. Breaking structural changes require migration.
21. Context packages should expose a manifest when appropriate.
22. Relationships must be explicit.
23. Circular dependencies must be rejected.
24. Broken references must be detected.
25. Orphan contexts should be identified.
26. Context duplication should be minimized.
27. Derived contexts must preserve their upstream source.
28. Runtime composition must not mutate source contexts.
29. Context snapshots must preserve structural reproducibility.
30. Structure must remain independent from selection priority.
31. Structure must remain independent from authority.
32. Structure must remain independent from security policy.
33. Security must not rely only on directory structure.
34. Context structure should minimize noise.
35. Context structure should maximize useful signal.
36. Every production Context Package must have a predictable and validated structure.