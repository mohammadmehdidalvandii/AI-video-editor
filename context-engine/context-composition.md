# Context Composition

## Purpose

Context Composition is responsible for combining selected contexts into a coherent and structured context set.

Composition occurs after Discovery, Selection, and Priority evaluation, and before Resolution, Loading, and Building.

Flow:

    Request
       ↓
    Discovery
       ↓
    Selector
       ↓
    Priority
       ↓
    Composition
       ↓
    Resolution
       ↓
    Loader
       ↓
    Builder

---

# 1. Core Principle

Composition must:

> Combine selected contexts without losing their identity, scope, authority, priority, dependencies, relationships, metadata, or version information.

Composition must not resolve conflicts without an explicit policy.

---

# 2. Composition vs Discovery

Discovery answers:

    Which contexts might be relevant?

Composition answers:

    How should the selected contexts be structured together?

---

# 3. Composition vs Selection

Selection determines:

    Which contexts should be used?

Composition determines:

    How should the selected contexts be combined?

---

# 4. Composition vs Resolution

Composition:

    Organizes contexts
    Preserves relationships
    Preserves dependencies
    Detects potential conflicts

Resolution:

    Resolves conflicts
    Applies precedence
    Handles overrides
    Determines the final effective context

---

# 5. Composition Input

Composition may receive:

    Selected Contexts
    Context Metadata
    Priority Information
    Dependencies
    Relationships
    Scope
    Authority
    Version Information
    Composition Rules
    System Policies
    Security Constraints

---

# 6. Composition Output

Composition may produce:

    Ordered Contexts
    Context Groups
    Dependency Graph
    Relationship Map
    Composition Metadata
    Conflict Candidates
    Resolution Candidates

---

# 7. Context Identity

Composition must not change Context Identity.

Example:

    security.authentication

must remain:

    security.authentication

after composition.

Context IDs must remain stable.

---

# 8. Context Grouping

Related contexts may be grouped together.

Example:

    Authentication
        ├── JWT
        ├── Refresh Token
        └── Authorization

Grouping must not remove the identity of individual contexts.

---

# 9. Domain Grouping

Contexts may be grouped by domain.

Example:

    backend
    database
    security
    deployment

---

# 10. Context Type Grouping

Contexts may be grouped by type.

Examples:

    RULE
    POLICY
    ARCHITECTURE
    REFERENCE
    EXAMPLE
    CONSTRAINT

---

# 11. Scope Grouping

Contexts may be grouped by scope.

Examples:

    SYSTEM
    PROJECT
    DOMAIN
    TASK
    USER
    SESSION

---

# 12. Authority Grouping

Contexts may be grouped by authority.

Examples:

    SYSTEM
    POLICY
    PROJECT
    DOMAIN
    USER
    EXTERNAL

Authority must remain explicit.

---

# 13. Dependency Ordering

If:

    A → B

means that A depends on B, Composition must preserve this dependency.

Example:

    API
      ↓
    Authentication
      ↓
    JWT

---

# 14. Dependency Graph

Composition may construct a dependency graph.

Example:

    Context A
       ↓
    Context B
       ↓
    Context C

The graph must preserve dependency direction.

---

# 15. Topological Ordering

If dependencies do not contain cycles, contexts may be ordered using topological ordering.

Example:

    C
    ↓
    B
    ↓
    A

The exact ordering policy must be deterministic.

---

# 16. Dependency Cycles

If:

    A → B
    B → C
    C → A

exists, the system must detect:

    COMPOSITION_CYCLE

Cycles must never be silently ignored.

---

# 17. Cycle Handling

Possible strategies include:

    Reject
    Break According to Policy
    Mark for Resolution
    Request Manual Intervention

The strategy must be explicitly defined by policy.

---

# 18. Composition Order

Context ordering can consider:

    Scope
    Authority
    Priority
    Dependency
    Context Type
    Version
    Explicit Ordering Rules

Example:

    System
    Policy
    Architecture
    Project
    Domain
    Task
    User
    External

This ordering is only an example and must be configurable.

---

# 19. Scope Ordering

A possible scope hierarchy is:

    SYSTEM
      ↓
    PROJECT
      ↓
    DOMAIN
      ↓
    TASK
      ↓
    USER

The actual hierarchy must be defined by the system policy.

---

# 20. Authority Ordering

Authority determines how much influence a Context may have during later resolution.

Example:

    SYSTEM > PROJECT > USER

This is only an example.

Authority must not be inferred implicitly.

---

# 21. Priority Preservation

Composition must preserve Context Priority.

Example:

    Context A:
        priority = 100

    Context B:
        priority = 50

The Priority values must remain available to Resolution.

---

# 22. Metadata Preservation

Composition must preserve important metadata.

Examples:

    id
    version
    source
    owner
    scope
    authority
    priority
    trust
    freshness
    status

---

# 23. Provenance Preservation

Every Context should retain information about its origin.

Example:

    source:
        project-rules

or:

    source:
        system-policy

---

# 24. Version Preservation

Composition must preserve Context Version information.

Example:

    security.jwt@2.1.0

must remain identifiable as version `2.1.0`.

---

# 25. Context Boundaries

Composition must not merge independent contexts into a single identity without an explicit policy.

Example:

    database
    security

must remain independently identifiable.

---

# 26. Composition Layers

Composition may create logical layers.

Example:

    ┌────────────────────────┐
    │ Composition Layer      │
    ├────────────────────────┤
    │ Context A              │
    │ Context B              │
    │ Context C              │
    └────────────────────────┘

Layers must preserve Context boundaries.

---

# 27. Logical Composition

Logical Composition combines contexts that belong to the same conceptual problem.

Example:

    Authentication
        +
    Authorization
        +
    Session Management

---

# 28. Structural Composition

Structural Composition organizes contexts into a hierarchy.

Example:

    security/
        authentication/
        authorization/
        sessions/

---

# 29. Semantic Composition

Semantic Composition groups contexts that are related by meaning.

Example:

    PostgreSQL
    Transactions
    ACID
    Connection Pooling

---

# 30. Context Bundles

Composition may create a Context Bundle.

Example:

    backend-authentication-bundle

containing:

    authentication
    authorization
    jwt
    refresh-token

A Bundle is a container and must not replace the identities of its member contexts.

---

# 31. Bundle Identity

A Bundle may have its own identifier:

    backend-authentication-bundle

while preserving:

    security.authentication
    security.jwt
    security.authorization

as independent Context IDs.

---

# 32. Nested Composition

Bundles may contain other bundles.

Example:

    backend
      └── security
            └── authentication

Nested Composition must have a configurable depth limit.

---

# 33. Composition Depth

Composition depth should be bounded.

Example:

    maxDepth = 5

This prevents excessive nesting and recursive structures.

---

# 34. Flattening

Some systems may flatten hierarchical structures.

Example:

    security
      └── authentication
            └── jwt

may become:

    security
    authentication
    jwt

Flattening must preserve parent-child relationships.

---

# 35. Non-Flattening

The default behavior should generally preserve the original hierarchy unless flattening is explicitly required.

---

# 36. Relationship Preservation

Composition must preserve Context relationships.

Examples:

    depends_on
    extends
    overrides
    references
    conflicts_with
    requires
    supplements

---

# 37. Extension Relationship

If:

    A extends B

Composition must preserve the relationship between A and B.

---

# 38. Supplement Relationship

If:

    A supplements B

Composition must not treat A as a replacement for B.

---

# 39. Override Relationship

If:

    A overrides B

both contexts should initially remain available.

Resolution determines which Context becomes effective.

---

# 40. Conflict Relationship

If:

    A conflicts_with B

Composition must preserve the conflict relationship.

Composition must not silently remove either Context.

---

# 41. Reference Relationship

References must remain traceable after Composition.

Example:

    Context A
        references:
            Context B

The reference must remain resolvable.

---

# 42. Conflict Detection

Composition may detect obvious conflicts.

Example:

    Rule A:
        Use PostgreSQL

    Rule B:
        Use MongoDB only

Result:

    CONFLICT_CANDIDATE

---

# 43. Conflict Handling Boundary

Composition:

    Detects conflicts

Resolution:

    Decides conflicts

This boundary must remain explicit.

---

# 44. Duplicate Contexts

If the same Context appears multiple times:

    security.jwt
    security.jwt

Composition should deduplicate it.

---

# 45. Duplicate Versions

The following are not necessarily duplicates:

    security.jwt@1.0.0
    security.jwt@2.0.0

They may represent different versions and require Version Resolution.

---

# 46. Compatibility

Composition should preserve compatibility requirements.

Example:

    requires:
        node >= 20

---

# 47. Incompatible Contexts

Example:

    Context A:
        Node 18

    Context B:
        Node 22

Composition should identify this as a compatibility conflict when the constraints are mutually exclusive.

---

# 48. Composition Policy

Composition Policy defines:

    Grouping
    Ordering
    Flattening
    Dependency Handling
    Scope Handling
    Authority Handling
    Conflict Detection
    Duplicate Handling

---

# 49. Composition Configuration

Example:

    composition:
      maxDepth: 5
      preserveMetadata: true
      preserveIdentity: true
      detectCycles: true
      detectConflicts: true
      flatten: false

---

# 50. Deterministic Composition

For identical input and identical policy:

    Same Contexts
    Same Versions
    Same Relationships
    Same Policy

must produce the same Composition result.

---

# 51. Composition Reproducibility

Composition should record its configuration version.

Example:

    compositionPolicyVersion:
        3

This allows historical results to be reproduced.

---

# 52. Composition Snapshot

A Composition Snapshot may contain:

    contextIds
    versions
    policyVersion
    ordering
    dependencies
    relationships
    conflicts

---

# 53. Composition Provenance

For each Context, the system should be able to answer:

    Where did it come from?
    Why is it present?
    Which Selector selected it?
    Which Policy allowed it?

---

# 54. Composition Explainability

Example:

    security.jwt included because:

    - Selected by Selector
    - Required by authentication
    - Compatible with project requirements
    - Allowed by Security Policy

---

# 55. Composition and Loader

Composition must not be responsible for loading full Context content.

Composition should primarily prepare:

    Structure
    Relationships
    Dependencies
    Metadata

Loader is responsible for retrieving the actual Context content.

---

# 56. Lazy Composition

Large systems may use Lazy Composition.

Only metadata and references are composed initially.

Content is loaded later when required.

---

# 57. Eager Composition

Small Context sets may use Eager Composition.

The required content is composed immediately.

---

# 58. Lazy vs Eager Composition

Lazy Composition:

    Lower Initial Memory
    Lower Initial Cost
    Deferred Loading
    More Complexity

Eager Composition:

    Simpler
    Faster Immediate Access
    Higher Initial Cost

The strategy should be configurable.

---

# 59. Composition Cache

Composition results may be cached.

Possible Cache Key:

    selectedContextIds
    versions
    compositionPolicyVersion

---

# 60. Cache Invalidation

Composition Cache must be invalidated when relevant inputs change.

Examples:

    Context Version
    Context Relationship
    Composition Policy
    Selection Result
    Dependency Graph

---

# 61. Composition Memory

Composition should avoid storing full Context content in memory unless required.

Metadata-first Composition is preferred for large systems.

---

# 62. Large Context Sets

Large Context sets may require:

    Streaming
    Lazy References
    Pagination
    Chunked Composition

---

# 63. Streaming Composition

Contexts may be composed incrementally.

Example:

    Context 1
       ↓
    Context 2
       ↓
    Context 3

The system must preserve ordering and relationships during streaming.

---

# 64. Partial Composition

If some optional Contexts cannot be composed:

    partial = true

must be recorded.

---

# 65. Required Context

If a Required Context cannot be composed:

    COMPOSITION_FAILED

should be returned unless a specific fallback policy exists.

---

# 66. Optional Context

Optional Contexts may be omitted when unavailable.

Example:

    observability.metrics

If unavailable, the Composition may continue.

---

# 67. Dependency Strength

Dependencies may be classified as:

    REQUIRED
    OPTIONAL
    RECOMMENDED

This classification affects Composition and Resolution behavior.

---

# 68. Composition Weight

Contexts may have Composition Weight.

Example:

    Security:
        weight = 100

    Example:
        weight = 20

Weight must not automatically override Authority.

---

# 69. Stable Ordering

When two Contexts have equal Priority and equal Authority, a deterministic tie-breaker must be used.

Example:

    contextId ASC

---

# 70. Scope Inheritance

Child Contexts may inherit Scope from their Parent.

Example:

    project.backend

Child:

    project.backend.database

---

# 71. Scope Override

A Child Context may explicitly define a different Scope.

Example:

    Parent:
        PROJECT

    Child:
        TASK

Explicit Scope Override must be allowed only by policy.

---

# 72. Authority Inheritance

Authority may be:

    Inherited
    Explicit
    Restricted

---

# 73. Authority Escalation

A Child Context must not gain higher Authority than its Parent without an explicit policy.

---

# 74. Trust Propagation

Trust may be inherited from a trusted Source.

However:

    Untrusted Source

must never become Trusted merely because it was composed with a Trusted Context.

---

# 75. Freshness Propagation

Composition must preserve Freshness information.

---

# 76. Mixed Freshness

A Composition may contain:

    Context A:
        Fresh

    Context B:
        Stale

The system must preserve this distinction.

---

# 77. Composition Metadata

Example:

    {
      "compositionId": "comp-123",
      "policyVersion": "2",
      "contexts": [
        {
          "id": "security.authentication",
          "version": "2.0.0"
        },
        {
          "id": "security.jwt",
          "version": "3.1.0"
        }
      ]
    }

---

# 78. Composition ID

Each Composition may have a unique identifier.

Example:

    comp-2026-001

This is useful for:

    Logging
    Tracing
    Auditing
    Debugging

---

# 79. Request ID

Composition should preserve the originating Request ID.

Example:

    requestId:
        req-123

---

# 80. Composition Trace

Example:

    Request
       ↓
    Selector
       ↓
    Selected 8 Contexts
       ↓
    Composition
       ↓
    3 Groups
       ↓
    7 Relationships
       ↓
    1 Conflict Candidate
       ↓
    Resolution

---

# 81. Composition Metrics

Recommended metrics:

    composition_requests_total
    composition_duration
    composition_context_count
    composition_group_count
    composition_dependency_count
    composition_conflict_count
    composition_cycle_count
    composition_failure_total

---

# 82. Composition Quality Metrics

Useful metrics include:

    Duplicate Rate
    Conflict Rate
    Missing Dependency Rate
    Invalid Dependency Rate
    Composition Latency
    Composition Failure Rate
    Partial Composition Rate

---

# 83. Composition Testing

Composition should be tested using:

    Ordering Tests
    Dependency Tests
    Cycle Tests
    Duplicate Tests
    Scope Tests
    Authority Tests
    Version Tests
    Conflict Tests
    Metadata Tests
    Determinism Tests

---

# 84. Regression Testing

Changes to:

    Composition Policy
    Context Structure
    Dependency Model
    Relationship Model

must be covered by Regression Tests.

---

# 85. Security Testing

Security tests must verify:

    Unauthorized Contexts cannot enter Composition
    Restricted Contexts remain restricted
    Tenant boundaries remain intact
    Authority cannot be escalated
    Trust cannot be silently upgraded

---

# 86. Composition Isolation

Composition must prevent unauthorized Context mixing.

---

# 87. Tenant Boundary

Example:

    tenant-a/security
    tenant-b/security

must not be composed together without explicit permission.

---

# 88. Project Boundary

Example:

    project-a/database
    project-b/database

must not be composed together without explicit permission.

---

# 89. Environment Boundary

Example:

    production/security
    development/security

must remain Environment-aware.

---

# 90. Composition and Governance

Governance defines:

    Who Can Compose?
    Which Contexts Can Be Combined?
    Which Policies Apply?
    Which Sources Are Allowed?

---

# 91. Composition and Validation

Validation should verify:

    Structure
    Dependencies
    Relationships
    Metadata
    Version Constraints
    Scope Constraints

---

# 92. Composition and Resolution

Composition prepares Contexts for Resolution.

Example:

    Composition:

    A
    B
    C

    Conflict:

    A ↔ B

Resolution determines:

    A
    B
    Both
    Neither

according to policy.

---

# 93. Composition and Builder

Builder consumes the resolved Composition and produces the final Context representation.

Composition itself should not generate the final prompt or final Context payload.

---

# 94. Composition Architecture

Recommended Architecture:

    ┌─────────────────────┐
    │ Selected Contexts   │
    └──────────┬──────────┘
               ↓
    ┌─────────────────────┐
    │ Composition Engine  │
    └──────────┬──────────┘
               ↓
       ┌───────┴────────┐
       ↓                ↓
    Grouping        Dependency
       │                │
       └───────┬────────┘
               ↓
        Ordering Engine
               ↓
        Relationship Map
               ↓
        Conflict Detection
               ↓
        Composition Result
               ↓
           Resolution

---

# 95. Composition Result

Example:

    {
      "compositionId": "comp-123",
      "contexts": [
        {
          "id": "security.authentication",
          "version": "2.0.0",
          "scope": "project",
          "authority": "policy"
        },
        {
          "id": "security.jwt",
          "version": "3.1.0",
          "scope": "project",
          "authority": "domain"
        }
      ],
      "groups": [
        "security.authentication"
      ],
      "dependencies": [
        {
          "from": "security.authentication",
          "to": "security.jwt",
          "type": "required"
        }
      ],
      "conflicts": []
    }

---

# 96. Composition Contract

The Composition Contract should define:

    Input Contexts
    Output Structure
    Ordering
    Dependencies
    Relationships
    Metadata
    Scope
    Authority
    Version
    Errors
    Partial Results

---

# 97. Composition Quality Gates

Before returning a Composition Result:

    [ ] Every Context has a valid ID
    [ ] Every Context has a valid version
    [ ] Context identity is preserved
    [ ] Dependencies are valid
    [ ] Relationships are valid
    [ ] Cycles are detected
    [ ] Duplicates are handled
    [ ] Scope boundaries are respected
    [ ] Authority is preserved
    [ ] Trust information is preserved
    [ ] Freshness information is preserved
    [ ] Version compatibility is checked
    [ ] Security constraints are enforced
    [ ] Ordering is deterministic
    [ ] Required Contexts are available
    [ ] Optional failures are marked
    [ ] Conflicts are recorded
    [ ] Provenance is preserved

---

# 98. Composition Failure Modes

Possible failures:

    COMPOSITION_CYCLE
    INVALID_DEPENDENCY
    DUPLICATE_CONTEXT
    INCOMPATIBLE_VERSION
    INVALID_SCOPE
    INVALID_RELATIONSHIP
    POLICY_VIOLATION
    REQUIRED_CONTEXT_MISSING
    AUTHORITY_VIOLATION
    SECURITY_VIOLATION

---

# 99. Failure Handling

Every failure should be:

    Detected
    Classified
    Logged
    Measured
    Traced
    Recovered or Safely Rejected

---

# 100. Composition Golden Rules

1. Composition combines selected Contexts.
2. Composition must not perform Discovery.
3. Composition must not perform final Selection.
4. Composition must not silently resolve conflicts.
5. Composition must preserve Context Identity.
6. Composition must preserve Metadata.
7. Composition must preserve Version.
8. Composition must preserve Source.
9. Composition must preserve Scope.
10. Composition must preserve Authority.
11. Composition must preserve Priority.
12. Composition must preserve Trust.
13. Composition must preserve Freshness.
14. Composition must preserve Dependencies.
15. Composition must preserve Relationships.
16. Composition must detect dependency cycles.
17. Composition must detect duplicate Contexts.
18. Composition must detect invalid dependencies.
19. Composition must detect compatibility conflicts.
20. Composition must maintain deterministic ordering.
21. Composition must use explicit tie-breakers.
22. Composition must respect Tenant boundaries.
23. Composition must respect Project boundaries.
24. Composition must respect Environment boundaries.
25. Composition must prevent unauthorized Authority escalation.
26. Composition must not silently upgrade Trust.
27. Composition must distinguish Required and Optional Contexts.
28. Composition should support Partial Composition.
29. Composition must expose failure states.
30. Composition should support Lazy Composition.
31. Composition should support Large Context Sets.
32. Composition should support caching when safe.
33. Composition Cache must be invalidated when inputs change.
34. Composition should support reproducible results.
35. Composition should support Composition Snapshots.
36. Composition should preserve Provenance.
37. Composition should be explainable.
38. Composition should be observable.
39. Composition should be auditable where required.
40. Composition must be testable.
41. Composition must support Regression Testing.
42. Composition must respect Security Policies.
43. Composition must respect Governance Policies.
44. Composition must remain independent from Loading.
45. Composition must remain independent from Building.
46. Composition must have a clear boundary with Resolution.
47. Composition should minimize unnecessary content loading.
48. Composition should preserve Context boundaries.
49. Composition should preserve dependency integrity.
50. Composition should preserve relationship integrity.
51. Composition should be deterministic.
52. Composition should be reproducible.
53. Composition should be version-aware.
54. Composition should be lifecycle-aware.
55. Composition should be policy-aware.
56. Composition should be source-aware.
57. Composition should be dependency-aware.
58. Composition should be conflict-aware.
59. Composition should be security-aware.
60. Composition must produce a well-defined structure for the Resolution stage.