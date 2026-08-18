# Context Resolution

## Purpose

Context Resolution is responsible for determining the effective set of Contexts after Composition.

Resolution evaluates:

    Conflicts
    Overrides
    Precedence
    Authority
    Priority
    Scope
    Version Compatibility
    Dependencies
    Constraints

Resolution produces a deterministic result that can be passed to the Loader and Builder.

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

Resolution must:

> Determine which Contexts are effective when multiple Contexts interact, conflict, override, or impose competing constraints.

Resolution must be policy-driven.

---

# 2. Resolution vs Composition

Composition determines:

    How Contexts are structured together.

Resolution determines:

    Which Contexts and rules become effective.

---

# 3. Resolution vs Selection

Selection determines:

    Which Contexts are relevant.

Resolution determines:

    Which selected Contexts remain effective after applying policies and relationships.

---

# 4. Resolution vs Priority

Priority provides an ordering or importance signal.

Resolution uses Priority together with:

    Authority
    Scope
    Explicit Overrides
    Constraints
    Version Compatibility

Priority alone must not determine the final result.

---

# 5. Resolution Input

Resolution may receive:

    Composition Result
    Context Metadata
    Dependencies
    Relationships
    Priority
    Authority
    Scope
    Version Constraints
    Override Rules
    Conflict Rules
    Security Policies
    Governance Policies

---

# 6. Resolution Output

Resolution should produce:

    Effective Contexts
    Removed Contexts
    Overrides
    Conflicts
    Unresolved Conflicts
    Applied Rules
    Resolution Metadata
    Resolution Trace

---

# 7. Effective Context

An Effective Context is a Context that remains valid after Resolution.

Example:

    Context A
    Context B
    Context C

After Resolution:

    Context A
    Context C

Context B may have been overridden or rejected.

---

# 8. Resolution Status

Each Context may receive a status:

    EFFECTIVE
    OVERRIDDEN
    REJECTED
    INCOMPATIBLE
    UNRESOLVED
    OPTIONAL
    CONDITIONAL

---

# 9. Conflict Resolution

Example:

    Context A:
        database = PostgreSQL

    Context B:
        database = MongoDB

Resolution must determine which rule is authoritative.

---

# 10. Conflict Types

Possible conflict types:

    VALUE_CONFLICT
    POLICY_CONFLICT
    VERSION_CONFLICT
    SCOPE_CONFLICT
    AUTHORITY_CONFLICT
    DEPENDENCY_CONFLICT
    RESOURCE_CONFLICT
    SECURITY_CONFLICT
    BEHAVIOR_CONFLICT

---

# 11. Explicit Override

Example:

    A:
        timeout = 30s

    B:
        overrides A
        timeout = 60s

Result:

    timeout = 60s

Explicit Override should generally have stronger precedence than implicit ordering.

---

# 12. Override Relationship

If:

    B overrides A

Resolution should preserve:

    A → overridden_by → B

for traceability.

---

# 13. Authority

Authority determines the ability of a Context to influence the final result.

Example:

    SYSTEM
    POLICY
    PROJECT
    DOMAIN
    USER
    EXTERNAL

Higher Authority may override lower Authority when explicitly allowed.

---

# 14. Authority Must Be Explicit

Resolution must not infer Authority from:

    Context Name
    File Position
    Search Score
    Creation Date

Authority must come from trusted metadata or policy.

---

# 15. Scope

Resolution must consider Scope.

Example:

    SYSTEM:
        timeout = 60s

    PROJECT:
        timeout = 30s

The system must determine whether Project scope is allowed to override System scope.

---

# 16. Scope Precedence

Possible precedence:

    SYSTEM
      ↓
    PROJECT
      ↓
    DOMAIN
      ↓
    TASK
      ↓
    USER

This is only an example.

Actual precedence must be policy-defined.

---

# 17. Priority

Priority can be used as a tie-breaker.

Example:

    Context A:
        priority = 100

    Context B:
        priority = 50

If Authority and Scope are equal, A may win.

---

# 18. Priority Is Not Authority

A Context with:

    priority = 1000

must not automatically override a System Policy with:

    priority = 10

if Authority rules prevent that override.

---

# 19. Precedence

Resolution should use an explicit precedence model.

Example:

    Explicit Override
        >
    Authority
        >
    Scope
        >
    Policy Priority
        >
    Context Priority
        >
    Deterministic Tie Breaker

The exact precedence must be configurable.

---

# 20. Deterministic Resolution

Identical input must produce identical output.

Given:

    Same Contexts
    Same Versions
    Same Policies
    Same Configuration

Resolution must produce the same result.

---

# 21. Tie Breaking

If two Contexts have equal precedence, a deterministic tie-breaker is required.

Example:

    contextId ASC

---

# 22. Resolution Graph

Resolution can operate on a graph:

    Context A
       ↓
    Context B
       ↓
    Context C

Relationships may include:

    overrides
    depends_on
    conflicts_with
    requires
    extends

---

# 23. Resolution Ordering

A possible resolution sequence:

    Validate Inputs
       ↓
    Apply Security Rules
       ↓
    Validate Dependencies
       ↓
    Resolve Explicit Overrides
       ↓
    Resolve Authority
       ↓
    Resolve Scope
       ↓
    Resolve Priority
       ↓
    Resolve Version Constraints
       ↓
    Detect Remaining Conflicts
       ↓
    Produce Effective Context Set

---

# 24. Security First

Security constraints should be evaluated before normal precedence rules.

A lower-priority Security Policy may still reject a Context.

---

# 25. Security Boundary

Resolution must never produce an Effective Context that violates Security Policy.

---

# 26. Unauthorized Override

A Context must not override another Context unless its Authority and Scope allow the operation.

---

# 27. Version Resolution

Example:

    Context A:
        requires:
            database >= 2.0

    Available:

        database@1.5
        database@2.1
        database@3.0

Resolution should select a compatible version according to Version Policy.

---

# 28. Latest Version Is Not Always Correct

The latest version is not automatically the correct version.

Compatibility must be evaluated first.

---

# 29. Version Conflict

Example:

    Context A:
        requires database = 2.x

    Context B:
        requires database = 3.x

If both are Required:

    VERSION_CONFLICT

---

# 30. Dependency Resolution

If:

    A requires B

then B must be available and compatible.

If B cannot be satisfied:

    A cannot become Effective

unless an explicit fallback exists.

---

# 31. Required Dependencies

Required dependencies must be resolved before a Context becomes Effective.

---

# 32. Optional Dependencies

Optional dependencies may be missing.

The Context may remain Effective if its behavior remains valid.

---

# 33. Recommended Dependencies

Recommended dependencies should not automatically block Resolution.

---

# 34. Dependency Cycle

If:

    A → B
    B → C
    C → A

Resolution must detect:

    RESOLUTION_CYCLE

---

# 35. Cycle Handling

Possible strategies:

    Reject
    Break According to Policy
    Mark Unresolved
    Manual Resolution

---

# 36. Policy Conflict

Example:

    Policy A:
        minimum password length = 12

    Policy B:
        minimum password length = 8

If Policy A has higher Authority:

    minimum password length = 12

---

# 37. Security Policy Conflict

Security conflicts should generally fail closed.

Example:

    Policy A:
        encryption required

    Policy B:
        encryption optional

If A has authoritative Security scope:

    encryption required

---

# 38. Configuration Conflict

Example:

    Context A:
        port = 3000

    Context B:
        port = 4000

Resolution must use the configured precedence rules.

---

# 39. Behavioral Conflict

Example:

    Context A:
        retry = enabled

    Context B:
        retry = disabled

Resolution determines the effective behavior.

---

# 40. Structural Conflict

Example:

    Context A:
        authentication required

    Context B:
        authentication disabled

This should be classified as a structural or policy conflict.

---

# 41. Constraint Resolution

Constraints can be:

    Hard Constraints
    Soft Constraints

Hard Constraints must be satisfied.

Soft Constraints may be relaxed according to policy.

---

# 42. Hard Constraints

Examples:

    Security Requirements
    Legal Policies
    Required Dependencies
    Supported Versions

Violation should normally fail Resolution.

---

# 43. Soft Constraints

Examples:

    Preferred Provider
    Recommended Cache
    Optional Optimization

Soft constraints may be overridden when necessary.

---

# 44. Conditional Rules

A Context may apply only under certain conditions.

Example:

    if environment == production:
        enable-security-policy

Resolution evaluates the condition.

---

# 45. Environment Resolution

Example:

    development:
        debug = true

    production:
        debug = false

Resolution must select the rule applicable to the active environment.

---

# 46. Project Resolution

Project-level Contexts may override Domain defaults when explicitly allowed.

---

# 47. User Resolution

User-level preferences should only override higher-level Contexts when policy permits it.

---

# 48. Session Resolution

Temporary Session Contexts may override lower-level temporary configuration.

Session overrides should not automatically override Security or System Policies.

---

# 49. Temporary Overrides

Temporary Contexts should include:

    expiration
    scope
    owner
    reason

---

# 50. Expired Overrides

Expired Overrides must not become Effective.

Status:

    EXPIRED

---

# 51. Conflict Resolution Strategies

Possible strategies:

    Highest Authority
    Highest Priority
    Explicit Override
    Most Specific Scope
    Latest Compatible Version
    Merge
    Reject
    Manual Resolution

---

# 52. Merge Strategy

Some Contexts can be merged.

Example:

    Tags:
        backend

    Tags:
        database

Result:

    backend
    database

Merge must only be allowed for compatible fields.

---

# 53. Non-Mergeable Fields

Some fields should not be automatically merged.

Examples:

    Security Level
    Authentication Method
    Database Provider
    Encryption Requirement

---

# 54. List Merge

Lists may support:

    Union
    Intersection
    Replacement
    Append
    Remove

The strategy must be explicit.

---

# 55. Map Merge

Maps may support:

    Deep Merge
    Shallow Merge
    Replacement

The strategy must be defined per field.

---

# 56. Scalar Conflict

Scalar values generally require precedence.

Example:

    timeout = 30

    timeout = 60

Only one effective value should remain unless the schema allows multiple values.

---

# 57. Null Handling

Resolution must define how:

    null
    missing
    empty

are interpreted.

They must not automatically be treated as identical.

---

# 58. Default Values

Defaults should have lower precedence than explicit values unless policy states otherwise.

Example:

    Default:
        timeout = 30

    Explicit:
        timeout = 60

Result:

    timeout = 60

---

# 59. Inheritance

Context may inherit values from Parent Contexts.

Example:

    backend
      ↓
    database
      ↓
    postgresql

Child values may override inherited values when permitted.

---

# 60. Inheritance Precedence

A possible model:

    Explicit Child
        >
    Parent
        >
    Global Default

---

# 61. Inheritance Cycle

Resolution must detect:

    A inherits B
    B inherits A

Result:

    INHERITANCE_CYCLE

---

# 62. Resolution Evidence

Every major Resolution decision should include evidence.

Example:

    security.policy selected because:

    - Authority = SYSTEM
    - Scope = GLOBAL
    - Explicit override not allowed
    - Security policy requires it

---

# 63. Explainable Resolution

Resolution should answer:

    Why did this Context win?
    Why was another Context rejected?
    Which rule caused the decision?
    Which Policy was applied?

---

# 64. Resolution Trace

Example:

    Context A
        ↓
    Context B overrides A
        ↓
    B rejected by Security Policy
        ↓
    A restored
        ↓
    A becomes EFFECTIVE

---

# 65. Resolution Audit

Audit information may include:

    requestId
    compositionId
    contextId
    decision
    reason
    policy
    authority
    priority
    timestamp

---

# 66. Resolution Snapshot

Example:

    {
      "resolutionId": "res-123",
      "compositionId": "comp-123",
      "policyVersion": "5",
      "effectiveContexts": [
        "security.authentication",
        "security.jwt"
      ]
    }

---

# 67. Resolution Statuses

Possible Context statuses:

    EFFECTIVE
    OVERRIDDEN
    REJECTED
    INCOMPATIBLE
    UNRESOLVED
    EXPIRED
    BLOCKED

---

# 68. Unresolved Conflict

If the system cannot safely determine a winner:

    UNRESOLVED

must be returned.

The system should not guess.

---

# 69. Fail Closed

Security-sensitive unresolved conflicts should normally fail closed.

Example:

    Encryption Required
    vs
    Encryption Disabled

Result:

    Resolution Failure

unless an authoritative policy explicitly resolves it.

---

# 70. Fail Open

Fail Open should only be used when explicitly allowed.

It must never be the default for Security Policies.

---

# 71. Resolution Confidence

Resolution may optionally expose confidence.

However, deterministic Policy decisions should not rely solely on probabilistic confidence.

---

# 72. AI-Assisted Resolution

AI may assist with:

    Conflict Classification
    Semantic Analysis
    Suggested Resolution

But final Security or Policy decisions should remain controlled by deterministic policies.

---

# 73. AI Resolution Boundary

AI should not silently:

    Override Security Policy
    Change Authority
    Bypass Access Control
    Ignore Required Constraints

---

# 74. Human Resolution

High-risk unresolved conflicts may require human intervention.

Example:

    MANUAL_RESOLUTION_REQUIRED

---

# 75. Resolution Policy

Resolution Policy should define:

    Precedence
    Override Rules
    Merge Rules
    Conflict Rules
    Version Rules
    Dependency Rules
    Security Rules
    Failure Strategy

---

# 76. Resolution Configuration

Example:

    resolution:
      explicitOverrideFirst: true
      enforceSecurityFirst: true
      allowImplicitMerge: false
      failOnRequiredConflict: true
      failClosedOnSecurityConflict: true
      deterministicTieBreaker: "contextId"

---

# 77. Resolution Cache

Resolution Results may be cached.

Possible Cache Key:

    compositionHash
    resolutionPolicyVersion
    environment
    securityContext

---

# 78. Cache Invalidation

Resolution Cache must be invalidated when:

    Context Version Changes
    Policy Changes
    Composition Changes
    Security Rules Change
    Environment Changes
    Dependencies Change

---

# 79. Resolution Performance

Resolution should:

    Use Graph Algorithms
    Avoid Reprocessing
    Cache Stable Decisions
    Process Independent Branches in Parallel
    Stop Early When Safe

---

# 80. Parallel Resolution

Independent Context groups may be resolved in parallel.

Example:

    Security Group
          │
          ├── Authentication
          └── Authorization

    Database Group
          │
          ├── PostgreSQL
          └── Redis

Only independent groups should be parallelized.

---

# 81. Resolution Budget

Resolution should have limits for:

    Maximum Contexts
    Maximum Dependency Depth
    Maximum Conflict Count
    Maximum Resolution Time
    Maximum Merge Operations

---

# 82. Resolution Timeout

If Resolution exceeds the configured budget:

    RESOLUTION_TIMEOUT

must be returned.

---

# 83. Partial Resolution

Partial Resolution may be allowed for non-critical Contexts.

Example:

    Security:
        resolved

    Observability:
        unresolved

The final result must indicate:

    partial = true

---

# 84. Required Resolution

If a Required Context cannot be resolved:

    RESOLUTION_FAILED

should normally be returned.

---

# 85. Resolution Metrics

Recommended metrics:

    resolution_requests_total
    resolution_duration
    resolution_conflicts_total
    resolution_unresolved_total
    resolution_overrides_total
    resolution_rejections_total
    resolution_cycles_total
    resolution_failures_total
    resolution_partial_total

---

# 86. Resolution Quality Metrics

Useful measurements:

    Conflict Resolution Rate
    Unresolved Conflict Rate
    Override Accuracy
    Dependency Satisfaction Rate
    Version Compatibility Rate
    Policy Violation Rate

---

# 87. Resolution Testing

Tests should include:

    Explicit Override Tests
    Authority Tests
    Priority Tests
    Scope Tests
    Version Tests
    Dependency Tests
    Conflict Tests
    Merge Tests
    Inheritance Tests
    Security Tests
    Determinism Tests

---

# 88. Security Testing

Security tests must verify:

    Unauthorized Contexts cannot become Effective
    Security Policies cannot be overridden by lower Authority
    Tenant boundaries remain intact
    Expired overrides are rejected
    Blocked contexts remain blocked
    Trust cannot be escalated
    Access policies cannot be bypassed

---

# 89. Regression Testing

Every change to:

    Resolution Policy
    Precedence Rules
    Authority Rules
    Merge Rules
    Version Rules

should run the Resolution Regression Suite.

---

# 90. Resolution Failure Modes

Possible failures:

    RESOLUTION_CONFLICT
    RESOLUTION_CYCLE
    RESOLUTION_TIMEOUT
    VERSION_CONFLICT
    DEPENDENCY_CONFLICT
    AUTHORITY_VIOLATION
    POLICY_VIOLATION
    SECURITY_VIOLATION
    REQUIRED_CONTEXT_MISSING
    UNRESOLVED_CONFLICT

---

# 91. Resolution Architecture

Recommended architecture:

    ┌──────────────────────┐
    │ Composition Result   │
    └──────────┬───────────┘
               ↓
    ┌──────────────────────┐
    │ Resolution Engine    │
    └──────────┬───────────┘
               ↓
       Security Validation
               ↓
       Dependency Analysis
               ↓
       Explicit Overrides
               ↓
       Authority Resolution
               ↓
       Scope Resolution
               ↓
       Priority Resolution
               ↓
       Version Resolution
               ↓
       Conflict Detection
               ↓
       Conflict Resolution
               ↓
       Effective Contexts
               ↓
             Loader

---

# 92. Resolution Result

Example:

    {
      "resolutionId": "res-123",
      "compositionId": "comp-123",
      "contexts": [
        {
          "id": "security.authentication",
          "status": "EFFECTIVE"
        },
        {
          "id": "security.jwt",
          "status": "EFFECTIVE"
        },
        {
          "id": "legacy.auth",
          "status": "OVERRIDDEN",
          "overriddenBy": "security.authentication"
        }
      ],
      "conflicts": [],
      "unresolved": []
    }

---

# 93. Resolution Contract

The Resolution Contract should define:

    Input
    Output
    Precedence
    Authority
    Scope
    Priority
    Overrides
    Merge Rules
    Conflict Rules
    Version Rules
    Dependency Rules
    Security Rules
    Failure Modes
    Partial Results
    Trace Information

---

# 94. Resolution Quality Gates

Before producing the final Resolution Result:

    [ ] Security policies are satisfied
    [ ] Access rules are satisfied
    [ ] Required dependencies are satisfied
    [ ] Version constraints are satisfied
    [ ] Authority is valid
    [ ] Scope is valid
    [ ] Overrides are authorized
    [ ] Cycles are detected
    [ ] Conflicts are resolved or explicitly marked
    [ ] Required Contexts are effective
    [ ] Expired Contexts are rejected
    [ ] Blocked Contexts are rejected
    [ ] Provenance is preserved
    [ ] Resolution is deterministic
    [ ] Resolution is explainable
    [ ] Resolution metadata is complete

---

# 95. Resolution Golden Rules

1. Resolution must be policy-driven.
2. Resolution must be deterministic.
3. Resolution must preserve Context Identity.
4. Resolution must preserve Provenance.
5. Resolution must preserve Version information.
6. Resolution must respect Authority.
7. Resolution must respect Scope.
8. Resolution must respect Priority.
9. Resolution must respect Security Policy.
10. Resolution must respect Governance Policy.
11. Explicit Overrides must be validated.
12. Lower Authority Contexts must not bypass higher Authority Policies.
13. Priority must not automatically imply Authority.
14. Latest Version must not automatically win.
15. Required Dependencies must be satisfied.
16. Optional Dependencies may be missing when permitted.
17. Dependency Cycles must be detected.
18. Inheritance Cycles must be detected.
19. Conflicts must not be silently ignored.
20. Unresolved conflicts must be explicitly represented.
21. Security-sensitive conflicts should fail closed.
22. AI suggestions must not bypass deterministic Security Policies.
23. Expired overrides must not become Effective.
24. Blocked Contexts must not become Effective.
25. Unauthorized Contexts must not become Effective.
26. Tenant boundaries must remain isolated.
27. Project boundaries must remain isolated.
28. Environment boundaries must remain respected.
29. Merge behavior must be explicitly defined.
30. Scalar conflicts must have deterministic precedence.
31. List merge behavior must be explicit.
32. Map merge behavior must be explicit.
33. Null and missing values must be distinguished when required.
34. Defaults must have explicit precedence.
35. Context inheritance must be policy-controlled.
36. Authority escalation must be prevented.
37. Trust escalation must be prevented.
38. Resolution must produce an explainable decision.
39. Resolution should produce an audit trail where required.
40. Resolution should produce a trace for debugging.
41. Resolution should expose failure states.
42. Resolution should support partial results when safe.
43. Resolution should support caching when safe.
44. Resolution Cache must be invalidated when inputs change.
45. Resolution should enforce resource budgets.
46. Resolution should support timeouts.
47. Resolution should support parallel processing for independent branches.
48. Resolution must remain independent from Context Loading.
49. Resolution must remain independent from Context Building.
50. Resolution must produce a stable Effective Context Set for the next pipeline stage.