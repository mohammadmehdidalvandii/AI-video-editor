# Context Governance

## Purpose

The Context Governance system defines how context is created, reviewed, approved, changed, deprecated, and retired.

Its purpose is to ensure that context remains:

- Accurate
- Consistent
- Secure
- Maintainable
- Auditable
- Versioned
- Owned
- Reviewable

---

# 1. Core Responsibility

Context Governance controls the lifecycle of context:

    Create
      ↓
    Review
      ↓
    Validate
      ↓
    Approve
      ↓
    Release
      ↓
    Monitor
      ↓
    Update
      ↓
    Deprecate
      ↓
    Retire

---

# 2. Governance Principles

Context is a system artifact.

It must not be treated as informal documentation when it affects:

    AI Decisions
    Architecture
    Security
    Tool Execution
    Workflows
    Data Processing

---

# 3. Context Ownership

Every important context should have an owner.

Example:

    id:
        architecture.backend

    owner:
        backend-team

The owner is responsible for:

    Accuracy
    Maintenance
    Review
    Compatibility

---

# 4. Context Steward

A context may additionally have a steward.

The steward is responsible for day-to-day maintenance.

Example:

    Owner:
        Architecture Team

    Steward:
        Backend Maintainer

---

# 5. Ownership Metadata

Example:

    owner:
        architecture-team

    steward:
        backend-maintainer

    reviewers:
        backend-lead
        security-lead

---

# 6. No Owner

Critical context must not remain ownerless.

If:

    rules/security.md

has no owner:

    Governance Error

---

# 7. Context Classification

Contexts should be classified.

Example:

    Critical
    Important
    Normal
    Optional
    Experimental
    Deprecated

---

# 8. Critical Context

Critical context affects:

    Security
    Architecture
    Authorization
    Core Business Rules

Critical context requires stronger governance.

Examples:

    rules/security.md
    rules/architecture.md

---

# 9. Experimental Context

Experimental context may be used during development.

It should not automatically enter production.

Example:

    status:
        experimental

---

# 10. Production Context

Production context must satisfy:

    Validation
    Versioning
    Review
    Approval
    Compatibility

---

# 11. Context Lifecycle

Each context has a lifecycle state.

Possible states:

    draft
    review
    approved
    active
    deprecated
    retired

---

# 12. Draft

Draft context is being created or modified.

It may contain:

    Incomplete Information
    Temporary Rules
    Experimental Ideas

Draft context must not automatically enter production.

---

# 13. Review

Review means the context is ready for human or automated inspection.

Review should verify:

    Correctness
    Scope
    Dependencies
    Security
    Version
    Ownership

---

# 14. Approved

Approved context passed governance requirements.

Approval does not necessarily mean production activation.

---

# 15. Active

Active context is currently allowed in production workflows.

---

# 16. Deprecated

Deprecated context should no longer be selected for new workflows.

Existing snapshots may continue using it when required for reproducibility.

---

# 17. Retired

Retired context is no longer available for normal execution.

It may remain archived for:

    Audit
    Historical Snapshots
    Reproducibility

---

# 18. Context Status Metadata

Example:

    status:
        active

    version:
        2.1.0

    owner:
        editing-team

---

# 19. Change Ownership

Changes to context must have an identifiable actor.

Possible actors:

    Developer
    Maintainer
    Administrator
    Automated Migration

AI agents must not silently become context owners.

---

# 20. AI-Generated Context

AI may propose context changes.

Example:

    AI Proposal
        ↓
    Human Review
        ↓
    Validation
        ↓
    Approval
        ↓
    Release

AI-generated changes should not automatically become trusted production rules.

---

# 21. Human Approval

Critical context should require human approval.

Examples:

    Security Rules
    Architecture Rules
    Tool Permissions
    Authorization Rules

---

# 22. Approval Metadata

Example:

    approvedBy:
        security-lead

    approvedAt:
        timestamp

---

# 23. Multiple Approvals

High-risk changes may require multiple reviewers.

Example:

    Security Change

requires:

    Security Reviewer
    Architecture Reviewer

---

# 24. Separation of Duties

The person proposing a sensitive change should not necessarily be the only person approving it.

This is especially important for:

    Security
    Authorization
    Production Tool Permissions

---

# 25. Review Requirements

Every context change should be reviewed according to its risk.

Low risk:

    Typo Fix

High risk:

    Security Rule Change

---

# 26. Risk Classification

Possible levels:

    LOW
    MEDIUM
    HIGH
    CRITICAL

---

# 27. Low-Risk Change

Examples:

    Typo
    Formatting
    Grammar

May require:

    One Reviewer

---

# 28. Medium-Risk Change

Examples:

    New Domain Rule
    New Tool Description
    New Workflow Requirement

May require:

    Owner Review

---

# 29. High-Risk Change

Examples:

    Architecture Change
    Tool Permission Change
    Data Access Change

May require:

    Owner
    Technical Reviewer

---

# 30. Critical Change

Examples:

    Security Rule Change
    Authorization Change
    Secret Handling Change

May require:

    Security Review
    Architecture Review
    Explicit Approval

---

# 31. Change Classification

Before merging a context change determine:

    What changed?
    Who is affected?
    Is it semantic?
    Is it breaking?
    Does it affect security?
    Does it affect tools?
    Does it affect architecture?

---

# 32. Semantic Change

A semantic change modifies what the AI should believe or do.

Example:

    Before:
        Rendering must be synchronous.

    After:
        Rendering must be asynchronous.

This is a semantic change.

---

# 33. Non-Semantic Change

Example:

    Fixing a spelling mistake.

This normally does not require a major version.

---

# 34. Breaking Change

A breaking change invalidates existing consumers.

Examples:

    Removed Rule
    Changed Contract
    Changed Data Model
    Changed Tool Requirement
    Changed Dependency

---

# 35. Version Governance

Semantic versioning should be enforced.

    MAJOR
        Breaking

    MINOR
        Compatible Feature

    PATCH
        Correction

---

# 36. Dependency Governance

Before activating a new version:

    Check Dependencies
        ↓
    Check Compatibility
        ↓
    Validate
        ↓
    Approve

---

# 37. Dependency Ownership

Important dependencies should have owners.

Example:

    editing.operations

depends on:

    editing.timeline

The dependency relationship should be maintained explicitly.

---

# 38. Circular Dependency Prevention

Governance must prevent:

    A → B
    B → C
    C → A

Circular context architecture must be rejected.

---

# 39. Context Scope

Every context should have a defined scope.

Example:

    editing.timeline

scope:

    Video Editing Timeline

It should not contain unrelated:

    Authentication Rules
    Payment Rules
    Infrastructure Secrets

---

# 40. Single Responsibility

A context document should have one primary responsibility.

Bad:

    editing/timeline.md

contains:

    Timeline
    Authentication
    Database Credentials
    Deployment Instructions

Good:

    editing/timeline.md

contains only:

    Timeline Concepts
    Timeline Rules
    Timeline Constraints

---

# 41. Context Boundaries

Context boundaries should remain explicit.

Examples:

    Product
    Architecture
    Video
    Editing
    AI
    Rules

Cross-boundary dependencies must be explicit.

---

# 42. Duplication Governance

Duplicate information should be minimized.

If the same rule exists in:

    architecture/backend.md

and:

    editing/rendering.md

the authoritative source should be identified.

---

# 43. Single Source of Truth

Important rules should have one canonical location.

Other contexts should reference the canonical source.

---

# 44. Reference Policy

Instead of copying:

    Security Rule

into multiple documents, reference:

    rules/security.md

This reduces contradiction risk.

---

# 45. Contradiction Governance

If two contexts contradict each other:

    Detect
      ↓
    Report
      ↓
    Determine Authority
      ↓
    Resolve
      ↓
    Test
      ↓
    Approve

---

# 46. Hidden Rules

Critical behavior must not depend on undocumented rules.

Avoid:

    Implicit Priority
    Hidden Dependencies
    Undocumented Exceptions
    Untracked Overrides

---

# 47. Override Policy

Overrides must be explicit.

Example:

    override:
        architecture.backend

An override should include:

    Reason
    Scope
    Owner
    Expiration

---

# 48. Temporary Overrides

Temporary overrides must have expiration.

Example:

    expiresAt:
        2026-09-01

Expired overrides must automatically become invalid.

---

# 49. Emergency Changes

Emergency security changes may bypass normal timing constraints.

However they must still be:

    Audited
    Versioned
    Reviewed
    Documented

After the emergency, a retrospective review should occur.

---

# 50. Context Review Cycle

Critical context should be reviewed periodically.

Example:

    Security:
        Monthly

    Architecture:
        Quarterly

    Domain:
        As Needed

Exact intervals are configurable.

---

# 51. Review Triggers

A review may be triggered by:

    Security Incident
    Architecture Change
    Tool Change
    Model Change
    Product Change
    Dependency Change
    Context Drift

---

# 52. Stale Context

Context may become stale when the underlying system changes.

Example:

    Architecture:
        PostgreSQL

Current system:

    MySQL

The context requires review.

---

# 53. Context Drift

Governance should monitor divergence between:

    Context

and:

    Actual System

---

# 54. Drift Detection

Potential indicators:

    Code Structure Changed
    Database Changed
    API Changed
    Tool Changed
    Workflow Changed
    Deployment Changed

---

# 55. Drift Status

Possible states:

    CURRENT
    REVIEW_REQUIRED
    STALE
    INVALID

---

# 56. Context Validation

Governance depends on the Context Validator.

Before activation:

    Validate Context

must return:

    PASS

---

# 57. Security Validation

Security-sensitive context must pass:

    Security Validation

before production activation.

---

# 58. Testing Requirement

Context changes must include appropriate tests.

Examples:

    Priority Change
        → Priority Tests

    Security Change
        → Security Tests

    Architecture Change
        → Integration Tests

---

# 59. Regression Requirement

A fixed context bug should produce a regression test.

Example:

    Bug
      ↓
    Fix
      ↓
    Regression Test
      ↓
    Release

---

# 60. Release Governance

A context release should contain:

    Version
    Changes
    Validation
    Tests
    Approval
    Dependencies

---

# 61. Release Candidate

Critical context may use:

    Draft
        ↓
    Release Candidate
        ↓
    Validation
        ↓
    Approval
        ↓
    Production

---

# 62. Rollback

Every production context release should be rollbackable.

Use:

    Previous Version
    Previous Snapshot

---

# 63. Rollback Approval

Security-sensitive rollbacks should be audited.

Example:

    Rollback:
        rules/security@2.0.0
        →
        rules/security@1.9.0

---

# 64. Deprecation Policy

Before retirement:

    Mark Deprecated
        ↓
    Notify Consumers
        ↓
    Provide Replacement
        ↓
    Migrate
        ↓
    Retire

---

# 65. Deprecation Metadata

Example:

    status:
        deprecated

    replacement:
        editing.timeline@2.0.0

    deprecatedAt:
        timestamp

---

# 66. Retirement

Retired context should not be selected for new executions.

Historical snapshots may still reference it.

---

# 67. Historical Preservation

Retired context should remain available when needed for:

    Audit
    Debugging
    Snapshot Reconstruction
    Incident Investigation

---

# 68. Access Control

Context management permissions should be separated.

Possible roles:

    Viewer
    Contributor
    Maintainer
    Approver
    Administrator

---

# 69. Viewer

Can:

    Read Context
    Read Versions
    Read History

Cannot:

    Modify
    Approve
    Release

---

# 70. Contributor

Can:

    Create Draft
    Modify Draft
    Submit Review

Cannot automatically:

    Approve Production Context

---

# 71. Maintainer

Can:

    Maintain Context
    Resolve Issues
    Prepare Releases

---

# 72. Approver

Can:

    Review
    Approve

Approval permissions should be restricted by context category.

---

# 73. Administrator

Can manage:

    Governance Configuration
    Access Policies
    Emergency Controls

Administrative actions must be audited.

---

# 74. Audit Trail

Governance actions must be auditable.

Track:

    Created
    Updated
    Reviewed
    Approved
    Released
    Deprecated
    Retired
    Rolled Back

---

# 75. Audit Metadata

Example:

    {
      "event": "context.approved",
      "contextId": "architecture.backend",
      "version": "2.0.0",
      "actor": "backend-lead",
      "timestamp": "..."
    }

---

# 76. Git Governance

Context changes should use Git.

Git provides:

    History
    Diff
    Review
    Branching
    Rollback

---

# 77. Branch Protection

Critical context should use protected branches.

Examples:

    rules/security.md
    rules/ai-rules.md

Direct production modifications should be restricted.

---

# 78. Pull Requests

Context changes should normally enter production through a reviewed pull request.

The PR should include:

    Change Description
    Risk
    Version
    Tests
    Dependencies
    Migration

---

# 79. Commit Policy

Commits should clearly describe context changes.

Example:

    docs: update editing timeline rules

---

# 80. Change Traceability

A production context change should be traceable to:

    Git Commit
    Pull Request
    Reviewer
    Approval
    Context Version
    Release

---

# 81. Context Registry

A future Context Registry may contain:

    Context ID
    Version
    Owner
    Status
    Dependencies
    Security Class
    Last Review
    Current Release

---

# 82. Registry Example

    editing.timeline

    version:
        2.1.0

    owner:
        editing-team

    status:
        active

    securityClass:
        controlled

---

# 83. Registry Consistency

Registry data must remain consistent with actual context files.

Inconsistency should trigger:

    Governance Error

---

# 84. Context Health

Governance may calculate context health from:

    Validation
    Tests
    Ownership
    Freshness
    Dependencies
    Security
    Usage

---

# 85. Context Health States

Possible states:

    HEALTHY
    WARNING
    REVIEW_REQUIRED
    INVALID

---

# 86. Unused Context

Unused context should be monitored.

Possible actions:

    Review
    Consolidate
    Deprecate

Unused does not automatically mean unnecessary.

---

# 87. Overused Context

If a context appears in almost every workflow:

    Review Scope

It may indicate that the context is too broad.

---

# 88. Context Growth

Monitor:

    Number of Contexts
    Total Context Size
    Average Context Size
    Duplicate Content
    Dependency Complexity

---

# 89. Governance Metrics

Useful metrics:

    Active Contexts
    Deprecated Contexts
    Context Changes
    Review Time
    Approval Time
    Validation Failures
    Security Reviews
    Rollbacks
    Stale Contexts

---

# 90. Governance Alerts

Possible alerts:

    Critical Context Without Owner
    Critical Context Without Review
    Expired Override
    Stale Security Context
    Unapproved Production Change
    Dependency Conflict
    Context Without Version

---

# 91. AI Agent Governance

Agents may consume governed context.

Agents should not bypass:

    Versioning
    Validation
    Security
    Authorization

---

# 92. Agent Context Requirements

Each agent should declare:

    Required Context
    Compatible Versions
    Optional Context
    Tool Requirements

---

# 93. Workflow Governance

Workflows should declare:

    Context Requirements
    Tool Requirements
    Security Requirements
    Version Constraints

---

# 94. Governance and Model Changes

A model change may require context review.

Example:

    Model A
        ↓
    Model B

The model may interpret instructions differently.

Review may be required for:

    Security Rules
    Tool Instructions
    Agent Prompts

---

# 95. Governance and Tool Changes

Tool contract changes may invalidate context.

Example:

    trim_clip@1

becomes:

    trim_clip@2

Related context must be reviewed.

---

# 96. Governance and Architecture Changes

When architecture changes:

    Actual Architecture
        ↓
    Context Review
        ↓
    Update
        ↓
    Validate
        ↓
    Release

---

# 97. Governance and Product Changes

Product requirements may invalidate:

    Product Context
    Domain Context
    Workflow Context
    AI Context

Product changes should trigger relevant context review.

---

# 98. Context Governance API

A future governance API may expose:

    GET /contexts
    GET /contexts/:id
    GET /contexts/:id/versions
    GET /contexts/:id/reviews
    GET /contexts/:id/audit
    POST /contexts/:id/review
    POST /contexts/:id/approve
    POST /contexts/:id/deprecate

---

# 99. Governance API Security

Governance endpoints must enforce:

    Authentication
    Authorization
    Role Permissions
    Audit Logging

---

# 100. Architecture Rules

The Context Governance system must:

1. Assign ownership to critical context.
2. Define context lifecycle states.
3. Require review for important changes.
4. Require stronger approval for security changes.
5. Track context versions.
6. Track context dependencies.
7. Prevent undocumented overrides.
8. Require explicit temporary override expiration.
9. Maintain audit trails.
10. Support rollback.
11. Support deprecation.
12. Preserve historical snapshots.
13. Enforce role-based access.
14. Integrate with Git.
15. Require validation before production activation.
16. Require tests for behavioral changes.
17. Detect stale context.
18. Detect context drift.
19. Prevent unapproved production changes.
20. Keep governance independent from AI decisions.

---

# 101. Golden Rules

1. Every critical context must have an owner.
2. Every production context must have a version.
3. Critical context must be reviewed.
4. Security changes require security review.
5. AI-generated context changes require controlled approval.
6. Context must have a defined lifecycle.
7. Draft context must not silently enter production.
8. Deprecated context must not be selected for new workflows.
9. Retired context must remain available for historical reconstruction when necessary.
10. Every production change must be traceable.
11. Temporary overrides must expire.
12. Hidden overrides are prohibited.
13. Important rules should have a single source of truth.
14. Context duplication should be minimized.
15. Context drift must be monitored.
16. Stale context must be reviewed.
17. Critical governance actions must be audited.
18. Production context must be rollbackable.
19. Security boundaries cannot be bypassed through governance shortcuts.
20. Governance exists to keep context reliable throughout its entire lifecycle.