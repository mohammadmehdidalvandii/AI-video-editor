# Context Maintenance

## Purpose

The Context Maintenance system defines how context is kept accurate, consistent, current, and maintainable throughout the lifetime of the project.

Its goals are:

- Prevent Context Drift
- Remove Obsolete Information
- Keep Dependencies Valid
- Maintain Context Quality
- Reduce Duplication
- Control Context Growth
- Preserve Compatibility
- Keep Context Documentation Synchronized With the System

---

# 1. Core Principle

Context is a living system artifact.

It must evolve together with:

    Product
    Architecture
    Code
    Database
    Tools
    AI Workflows
    Security Policies

Context that no longer represents the actual system must be treated as invalid.

---

# 2. Maintenance Lifecycle

The maintenance lifecycle is:

    Monitor
        ↓
    Detect Change
        ↓
    Identify Affected Context
        ↓
    Review
        ↓
    Update
        ↓
    Validate
        ↓
    Test
        ↓
    Release

---

# 3. Maintenance Responsibilities

Maintenance includes:

    Context Updates
    Dependency Updates
    Version Updates
    Metadata Updates
    Conflict Resolution
    Duplication Removal
    Stale Context Detection
    Deprecation
    Retirement

---

# 4. Context Owner

Every important context should have an owner.

The owner is responsible for ensuring that the context remains:

    Accurate
    Current
    Valid
    Consistent
    Maintained

---

# 5. Maintenance Ownership

Example:

    context:
        architecture/backend.md

    owner:
        backend-team

    reviewer:
        backend-lead

---

# 6. Maintenance Triggers

Context maintenance may be triggered by:

    Code Changes
    Architecture Changes
    Product Changes
    Database Changes
    API Changes
    Tool Changes
    Security Changes
    Model Changes
    Workflow Changes
    Dependency Changes

---

# 7. Code Change Trigger

When implementation changes a behavior described by context:

    Code Change
        ↓
    Detect Affected Context
        ↓
    Review Context
        ↓
    Update Context

---

# 8. Architecture Change Trigger

Example:

    PostgreSQL
        ↓
    MySQL

Affected context may include:

    architecture/backend.md
    architecture/data-flow.md
    product/requirements.md
    ai/workflows.md

All affected contexts should be reviewed.

---

# 9. Tool Change Trigger

Example:

    FFmpeg Version
        6.x
        ↓
        7.x

Review:

    video/ffmpeg.md
    video/codecs.md
    video/filters.md
    editing/rendering.md

---

# 10. API Change Trigger

When an API changes:

    Endpoint
    Request Schema
    Response Schema
    Authentication
    Error Handling

related context must be reviewed.

---

# 11. Database Change Trigger

Database changes may affect:

    Schema
    Relationships
    Queries
    Constraints
    Performance
    Data Flow

Affected context must be identified.

---

# 12. Security Change Trigger

Security changes require immediate context review.

Examples:

    Authentication
    Authorization
    Secrets
    Encryption
    Tool Permissions
    Network Access

---

# 13. Context Drift

Context drift occurs when documented behavior differs from actual system behavior.

Example:

    Context:
        Rendering uses Worker A.

    System:
        Rendering uses Worker B.

This is context drift.

---

# 14. Drift Severity

Possible levels:

    LOW
    MEDIUM
    HIGH
    CRITICAL

---

# 15. Low Drift

Examples:

    Typo
    Formatting
    Minor Naming Difference

---

# 16. Medium Drift

Examples:

    Outdated API Description
    Missing Configuration
    Old Workflow

---

# 17. High Drift

Examples:

    Incorrect Architecture
    Incorrect Data Flow
    Wrong Tool Behavior

---

# 18. Critical Drift

Examples:

    Incorrect Security Rule
    Incorrect Authorization Rule
    Incorrect Tool Permission
    Incorrect Production Architecture

Critical drift should block affected production workflows when necessary.

---

# 19. Drift Detection

Drift may be detected through:

    Automated Tests
    Static Analysis
    Code Review
    Context Evaluation
    Runtime Observability
    Architecture Review
    Developer Reports

---

# 20. Automated Drift Detection

Possible checks:

    File Exists
    API Exists
    Model Exists
    Tool Exists
    Dependency Exists
    Schema Matches
    Version Matches

---

# 21. Manual Drift Detection

Developers should report context inconsistencies during:

    Code Review
    Architecture Review
    Bug Investigation
    Feature Development

---

# 22. Context Health

Every context may have a health state.

Possible states:

    HEALTHY
    WARNING
    STALE
    INVALID
    DEPRECATED
    RETIRED

---

# 23. Healthy Context

A healthy context:

    Matches System
    Passes Validation
    Has Valid Dependencies
    Has Owner
    Has Current Version

---

# 24. Stale Context

A stale context contains information that may no longer represent the current system.

Stale context should be reviewed before production use.

---

# 25. Invalid Context

Invalid context violates one or more requirements.

Examples:

    Broken Dependency
    Invalid Metadata
    Invalid Version
    Security Violation
    Contradiction

Invalid context must not enter production.

---

# 26. Context Freshness

Track:

    Created At
    Updated At
    Last Reviewed At
    Last Validated At

---

# 27. Review Age

A context should expose its review age.

Example:

    lastReviewed:
        2026-08-01

    currentDate:
        2026-08-18

    reviewAge:
        17 days

---

# 28. Freshness Policy

Different context categories may use different freshness requirements.

Example:

    Security:
        High Frequency

    Architecture:
        Medium Frequency

    Historical:
        Low Frequency

---

# 29. Automatic Review Reminder

The system may generate reminders when:

    Review Due
    Dependency Changed
    Context Frequently Fails
    Context Becomes Stale

---

# 30. Context Dependencies

Dependencies must be monitored.

Example:

    editing.rendering

depends on:

    editing.timeline
    video.ffmpeg
    video.codecs

If one dependency changes, dependent contexts should be reviewed.

---

# 31. Dependency Impact Analysis

Before changing a context:

    Identify Dependents
        ↓
    Evaluate Compatibility
        ↓
    Update Affected Contexts
        ↓
    Run Tests

---

# 32. Reverse Dependency Graph

The system should be able to determine:

    Which contexts depend on this context?

Example:

    video.ffmpeg

used by:

    editing.rendering
    editing.operations
    ai.workflows

---

# 33. Dependency Change

When:

    video.ffmpeg

changes version:

    Identify Dependents
        ↓
    Validate
        ↓
    Evaluate
        ↓
    Update

---

# 34. Context Duplication

Duplicate information creates maintenance problems.

Example:

    Rule A

exists in:

    file-1.md
    file-2.md
    file-3.md

If the rule changes, all copies must be updated.

---

# 35. Duplication Reduction

Prefer:

    Single Source of Truth

and references instead of repeated copies.

---

# 36. Duplicate Detection

Potential duplicates can be detected using:

    Text Similarity
    Semantic Similarity
    Identical Rules
    Matching Metadata

---

# 37. Duplicate Resolution

When duplicates are found:

    Identify Canonical Context
        ↓
    Remove Duplicate
        ↓
    Add Reference
        ↓
    Validate

---

# 38. Context Splitting

A context should be split when it becomes too broad.

Example:

    video.md

contains:

    FFmpeg
    Codecs
    Containers
    Filters
    Rendering

Possible result:

    video/
    ├── ffmpeg.md
    ├── codecs.md
    ├── containers.md
    ├── filters.md
    └── rendering.md

---

# 39. Context Merging

Contexts may be merged when they:

    Have the Same Responsibility
    Are Always Used Together
    Have Strongly Coupled Rules

---

# 40. Merge Safety

Before merging:

    Check Dependencies
    Check References
    Check Selection
    Check Evaluation
    Check Version Compatibility

---

# 41. Context Size

Monitor context size.

Metrics:

    Characters
    Tokens
    Sections
    Dependencies
    Rules

---

# 42. Context Growth

Large contexts should be reviewed.

Possible actions:

    Split
    Compress
    Remove Duplication
    Deprecate
    Archive

---

# 43. Context Complexity

Complexity increases with:

    Number of Rules
    Number of Dependencies
    Number of Cross-References
    Number of Exceptions
    Number of Overrides

---

# 44. Complexity Threshold

If context complexity becomes too high:

    Review Required

Do not allow complexity to grow indefinitely.

---

# 45. Rule Maintenance

Rules should be:

    Explicit
    Testable
    Scoped
    Versioned

Avoid ambiguous rules.

---

# 46. Ambiguous Rule

Bad:

    "Rendering should usually be fast."

Good:

    "Interactive preview rendering should complete within the configured preview latency target."

---

# 47. Rule Validation

When modifying a rule:

    Identify Consumers
        ↓
    Identify Conflicts
        ↓
    Update Tests
        ↓
    Evaluate
        ↓
    Approve

---

# 48. Exception Maintenance

Exceptions must be reviewed periodically.

Example:

    Exception:
        Skip validation for legacy clips.

This should have:

    Reason
    Scope
    Owner
    Expiration

---

# 49. Expired Exceptions

Expired exceptions must not remain active silently.

Possible behavior:

    Warning
    Block
    Review Required

depending on severity.

---

# 50. Temporary Context

Temporary context should have an expiration date.

Example:

    expiresAt:
        2026-09-01

After expiration:

    Review
        ↓
    Remove
    or
    Promote to Permanent

---

# 51. Experimental Context

Experimental context should be clearly labeled.

Example:

    status:
        experimental

It must not silently become production context.

---

# 52. Context Promotion

Promotion flow:

    Experimental
        ↓
    Evaluation
        ↓
    Review
        ↓
    Approval
        ↓
    Active

---

# 53. Context Deprecation

When context becomes obsolete:

    Active
        ↓
    Deprecated
        ↓
    Migration
        ↓
    Retired

---

# 54. Replacement Context

Deprecated context should provide a replacement when possible.

Example:

    old:
        editing/render.md

    replacement:
        editing/rendering.md

---

# 55. Reference Migration

Before retiring context:

    Find References
        ↓
    Update References
        ↓
    Validate
        ↓
    Retire

---

# 56. Broken References

The maintenance system should detect:

    Missing Context
    Missing File
    Invalid ID
    Invalid Version
    Invalid Link

---

# 57. Reference Validation

Every context reference should resolve to:

    Valid Context ID
    Valid Version
    Valid Path

---

# 58. Orphan Context

An orphan context has:

    No Owner
    No References
    No Usage
    No Clear Purpose

Orphan contexts should be reviewed.

---

# 59. Unused Context

Unused context may indicate:

    Obsolete Information
    Poor Discovery
    Wrong Metadata
    Missing Workflow Integration

Do not automatically delete unused context.

---

# 60. Usage Monitoring

Track:

    Selection Count
    Workflow Usage
    Agent Usage
    Tool Usage

---

# 61. Frequently Selected Context

A context used very frequently should be reviewed for:

    Correct Scope
    Size
    Dependencies
    Relevance

---

# 62. Context Cache Maintenance

If caching is used:

    Cache Invalidation
    Cache Expiration
    Version Matching
    Snapshot Matching

must be maintained.

---

# 63. Cache Invalidation

When context changes:

    Context Version Changes
        ↓
    Old Cache Invalidated

---

# 64. Stale Cache Protection

A cached context must not be used when:

    Version Mismatch
    Snapshot Mismatch
    Security Policy Mismatch

exists.

---

# 65. Snapshot Maintenance

Snapshots should remain:

    Immutable
    Identifiable
    Reproducible

---

# 66. Snapshot Retention

Retention policy may define:

    Active Snapshots
    Recent Snapshots
    Historical Snapshots
    Archived Snapshots

---

# 67. Snapshot Cleanup

Snapshots may be archived when no longer required.

Do not delete snapshots required for:

    Audit
    Reproducibility
    Incident Investigation

---

# 68. Context Migration

Schema changes may require migration.

Example:

    Context Schema v1
        ↓
    Migration
        ↓
    Context Schema v2

---

# 69. Migration Requirements

Every migration should define:

    Source Version
    Target Version
    Transformation
    Validation
    Rollback

---

# 70. Migration Validation

After migration:

    Validate Metadata
    Validate Dependencies
    Validate Semantics
    Run Tests
    Run Evaluation

---

# 71. Backward Compatibility

When possible, context changes should preserve compatibility.

Breaking changes require:

    Major Version

---

# 72. Maintenance Branches

Large maintenance changes may use dedicated branches.

Example:

    context/maintenance/rendering-v2

---

# 73. Small Changes

Small corrections may use normal feature branches.

Example:

    context/fix-ffmpeg-doc

---

# 74. Pull Requests

Maintenance PRs should include:

    Context Changed
    Reason
    Impact
    Version
    Tests
    Evaluation

---

# 75. Maintenance Commit

Commit messages should describe the change.

Examples:

    docs: update ffmpeg context

    docs: remove obsolete rendering rule

    docs: update timeline dependency

---

# 76. Change Log

Important context changes should maintain a changelog.

Example:

    ## 2.1.0

    Added:
        Render pipeline constraints

    Changed:
        Worker dependency

    Fixed:
        Incorrect codec requirement

---

# 77. Maintenance Testing

Every semantic maintenance change should run:

    Validation
    Relevant Unit Tests
    Integration Tests
    Context Evaluation

---

# 78. Security Maintenance

Security context changes additionally require:

    Security Tests
    Review
    Regression Tests

---

# 79. Architecture Maintenance

Architecture context changes should run:

    Dependency Validation
    Integration Tests
    Architecture Evaluation

---

# 80. Product Maintenance

Product context changes should verify:

    Requirements
    Users
    Use Cases
    Workflows

remain consistent.

---

# 81. AI Context Maintenance

AI-related context should be reviewed when:

    Model Changes
    Prompt Changes
    Tool Changes
    Agent Changes
    Workflow Changes

---

# 82. Prompt Maintenance

Prompts should be evaluated when:

    Context Structure Changes
    Model Changes
    Tool Schema Changes

---

# 83. Tool Context Maintenance

When a tool changes:

    Tool Schema
    Tool Permissions
    Tool Output
    Tool Errors

related context must be updated.

---

# 84. Workflow Maintenance

When a workflow changes:

    Required Context
    Optional Context
    Tool Dependencies
    Security Requirements

must be reviewed.

---

# 85. Maintenance Automation

Automation may perform:

    Broken Reference Detection
    Version Validation
    Duplicate Detection
    Stale Context Detection
    Dependency Checks
    Context Size Checks
    Security Scanning

---

# 86. Automated Maintenance Report

A maintenance report may contain:

    Stale Contexts
    Broken References
    Duplicate Content
    Invalid Dependencies
    Missing Owners
    Expired Overrides
    Oversized Contexts

---

# 87. Maintenance Dashboard

A future dashboard may display:

    Healthy Context
    Stale Context
    Invalid Context
    Deprecated Context
    Orphan Context
    Context Growth
    Dependency Health

---

# 88. Maintenance Metrics

Track:

    Context Count
    Active Context Count
    Stale Context Count
    Deprecated Context Count
    Broken References
    Duplicate Rules
    Average Context Size
    Average Review Age
    Validation Failures

---

# 89. Maintenance SLOs

Possible targets:

    Critical Context Drift:
        0 unresolved

    Broken References:
        0 production

    Security Context:
        100% reviewed

    Invalid Production Context:
        0

Exact targets are configurable.

---

# 90. Maintenance Alerts

Alerts may trigger for:

    Critical Context Becomes Stale
    Dependency Becomes Invalid
    Security Context Changes
    Broken Reference Detected
    Expired Override
    Context Size Exceeds Limit

---

# 91. Incident Maintenance

After a production incident:

    Identify Context Involved
        ↓
    Determine Root Cause
        ↓
    Update Context
        ↓
    Add Regression Test
        ↓
    Run Evaluation
        ↓
    Release

---

# 92. Root Cause Categories

Context-related incidents may result from:

    Missing Context
    Incorrect Context
    Stale Context
    Wrong Priority
    Wrong Version
    Security Filter
    Dependency Failure
    Context Drift

---

# 93. Post-Incident Review

Every significant context incident should answer:

    What happened?
    Which context was involved?
    Why was it not detected?
    How should detection improve?
    What test should be added?

---

# 94. Context Quality Improvement

Maintenance should continuously improve:

    Accuracy
    Relevance
    Structure
    Security
    Efficiency
    Discoverability

---

# 95. Maintenance and Evaluation

Maintenance and evaluation form a feedback loop:

    Evaluation
        ↓
    Failure
        ↓
    Maintenance
        ↓
    Validation
        ↓
    Evaluation

---

# 96. Maintenance and Governance

Governance defines:

    Who
    When
    How
    Approval

Maintenance performs:

    Update
    Validation
    Cleanup
    Migration

---

# 97. Maintenance and Security

Security requirements always take priority over optimization.

Never remove security context simply to:

    Reduce Tokens
    Improve Latency
    Reduce Context Size

---

# 98. Maintenance and Context Budget

When context becomes too large:

    Remove Irrelevant Content
        ↓
    Remove Duplication
        ↓
    Split Context
        ↓
    Compress Optional Context

Never silently remove required security context.

---

# 99. Maintenance Checklist

Before completing maintenance:

    [ ] Owner Identified
    [ ] Scope Confirmed
    [ ] Dependencies Checked
    [ ] References Checked
    [ ] Security Checked
    [ ] Version Updated
    [ ] Tests Updated
    [ ] Evaluation Completed
    [ ] Documentation Updated
    [ ] Review Completed

---

# 100. Architecture Rules

The Context Maintenance system must:

1. Keep context synchronized with the actual system.
2. Detect context drift.
3. Track context freshness.
4. Maintain ownership.
5. Validate dependencies.
6. Detect broken references.
7. Detect unnecessary duplication.
8. Control context growth.
9. Support context splitting.
10. Support context merging.
11. Support migration.
12. Support deprecation.
13. Support retirement.
14. Preserve historical snapshots.
15. Maintain cache consistency.
16. Require tests for semantic changes.
17. Require security validation for security changes.
18. Monitor context usage.
19. Support automated maintenance checks.
20. Feed maintenance results back into evaluation.

---

# 101. Golden Rules

1. Context must evolve with the system.
2. Stale context is a correctness problem.
3. Critical context must never remain ownerless.
4. Every semantic change must be validated.
5. Dependencies must be checked before release.
6. Broken references must not reach production.
7. Duplicate rules should have one canonical source.
8. Temporary context must expire.
9. Deprecated context must have a migration path when possible.
10. Retired context should remain available for historical reconstruction when necessary.
11. Context growth must be monitored.
12. Security context must never be removed for optimization.
13. Cache entries must match context versions.
14. Context migrations must be reversible when practical.
15. Production context must remain traceable.
16. Incident fixes must produce regression tests.
17. Maintenance must be connected to evaluation.
18. Maintenance must be controlled by governance.
19. Automation should detect maintenance problems early.
20. The goal of maintenance is to keep context synchronized with reality.