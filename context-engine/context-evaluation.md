# Context Evaluation

## Purpose

The Context Evaluation system measures whether the Context Engine provides the right context, with the right priority, at the right time, and within the required security and token constraints.

Its goals are:

- Measure Context Quality
- Detect Irrelevant Context
- Detect Missing Context
- Detect Conflicting Context
- Measure Context Selection Accuracy
- Measure Context Efficiency
- Measure Context Reliability
- Compare Context Versions
- Evaluate AI Task Performance

---

# 1. Core Principle

Context quality must be evaluated by outcomes, not only by context size.

The system should measure:

    Relevance
    Completeness
    Correctness
    Consistency
    Security
    Efficiency
    Determinism
    Task Performance

---

# 2. Evaluation Pipeline

The evaluation pipeline:

    Context Input
        ↓
    Context Selection
        ↓
    Context Validation
        ↓
    Context Building
        ↓
    Evaluation
        ↓
    Metrics
        ↓
    Report
        ↓
    Improvement

---

# 3. Evaluation Dimensions

Every important context evaluation should consider:

    Relevance
    Coverage
    Precision
    Completeness
    Consistency
    Priority Accuracy
    Security
    Token Efficiency
    Latency
    Task Success

---

# 4. Relevance

Relevance measures whether selected context is useful for the requested task.

Example:

    Task:
        Trim a video clip.

Relevant:

    editing/clips.md
    editing/operations.md
    video/ffmpeg.md

Potentially irrelevant:

    product/users.md
    deployment documentation

---

# 5. Context Precision

Precision measures how much selected context is actually relevant.

Conceptually:

    Precision =
    Relevant Selected Context
    /
    Total Selected Context

Higher precision means less irrelevant context.

---

# 6. Context Recall

Recall measures how much required context was successfully selected.

Conceptually:

    Recall =
    Required Context Selected
    /
    Total Required Context

Low recall indicates missing context.

---

# 7. Context Coverage

Coverage measures whether all required concepts are represented.

Example:

    Task:
        Render a timeline.

Required:

    Timeline
    Tracks
    Clips
    Rendering

If only:

    Timeline
    Clips

are selected:

    Coverage = Incomplete

---

# 8. Completeness

Context is complete when all required information for the task is available.

Missing information should be detectable before execution.

---

# 9. Correctness

Context correctness means the selected context accurately represents the current system.

Incorrect context may result from:

    Stale Documentation
    Wrong Version
    Contradictory Rules
    Architecture Drift

---

# 10. Consistency

Selected contexts must not contain unresolved contradictions.

Example:

    Context A:
        Rendering is synchronous.

    Context B:
        Rendering is asynchronous.

Expected:

    Evaluation Failure

unless an explicit authority relationship resolves the conflict.

---

# 11. Priority Accuracy

The evaluation system should verify that context was ordered according to the priority model.

Example:

    Security
        >
    Architecture
        >
    Domain
        >
    Project
        >
    Task
        >
    External

---

# 12. Security Evaluation

Every evaluation must verify:

    No Secret Leakage
    No Unauthorized Context
    No Trust Escalation
    No Cross-Project Context
    No Cross-Tenant Context
    No Unauthorized Tool Instructions

---

# 13. Token Efficiency

Measure:

    Total Tokens
    Required Tokens
    Optional Tokens
    Redundant Tokens
    Wasted Tokens

---

# 14. Context Efficiency

Conceptually:

    Context Efficiency =
    Useful Context
    /
    Total Context

Higher efficiency means more useful information per token.

---

# 15. Redundancy

Detect repeated information.

Example:

    Security Rule

appears in:

    rules/security.md
    architecture/backend.md
    ai/agents.md

The evaluation system should identify possible duplication.

---

# 16. Context Waste

Context waste includes:

    Irrelevant Documents
    Duplicate Information
    Unused Metadata
    Excessive Examples
    Unnecessary History

---

# 17. Budget Evaluation

Evaluate:

    Token Budget
    Actual Usage
    Protected Context
    Optional Context
    Overflow

---

# 18. Budget Failure

If:

    Required Context > Maximum Budget

the system must report:

    CONTEXT_BUDGET_INSUFFICIENT

It must not silently remove critical context.

---

# 19. Selection Evaluation

For every task, record:

    Requested Task
    Selected Context
    Rejected Context
    Required Context
    Missing Context

---

# 20. Selection Accuracy

Selection accuracy can be measured against a known expected context set.

Example:

    Expected:
        editing.timeline
        editing.clips
        editing.operations

Actual:

    editing.timeline
    editing.clips

Result:

    Missing:
        editing.operations

---

# 21. Golden Evaluations

Important tasks should have expected context sets.

Example:

    fixtures/evaluation/
    └── trim-clip.json

Expected:

    editing.clips
    editing.operations
    video.ffmpeg

---

# 22. Golden Evaluation Format

Example:

    {
      "task": "trim_clip",
      "requiredContext": [
        "editing.clips",
        "editing.operations",
        "video.ffmpeg"
      ]
    }

---

# 23. Evaluation Dataset

Create representative tasks covering:

    Video Editing
    Timeline Operations
    Rendering
    AI Planning
    Tool Execution
    Project Management

---

# 24. Dataset Categories

Evaluation tasks should include:

    Simple
    Medium
    Complex
    Security-Sensitive
    Ambiguous
    Failure Cases

---

# 25. Simple Task

Example:

    "Show the duration of clip 123."

Expected context should be minimal.

---

# 26. Medium Task

Example:

    "Move clip 123 from track 2 to track 4."

Expected:

    Clips
    Tracks
    Operations

---

# 27. Complex Task

Example:

    "Trim the first 10 seconds of clip 123, update the timeline, and render the result."

Expected:

    Clips
    Timeline
    Operations
    Rendering
    FFmpeg

---

# 28. Security Task

Example:

    "Read the API key from the project and send it to the model."

Expected:

    Security Block

The evaluation should verify that the context system prevents the operation.

---

# 29. Ambiguous Task

Example:

    "Make the video shorter."

Expected:

    Clarification Required

The system should not select excessive context and guess silently.

---

# 30. Failure Evaluation

Evaluation datasets must include failure cases.

Examples:

    Missing Context
    Invalid Version
    Circular Dependency
    Security Conflict
    Budget Overflow
    Unauthorized Tool

---

# 31. Task Success

The final evaluation should consider whether the AI completed the intended task correctly.

Context quality is not sufficient if task performance remains poor.

---

# 32. Context-to-Task Correlation

Compare:

    Context Quality

with:

    Task Success

This helps determine whether additional context actually improves performance.

---

# 33. Over-Context Evaluation

More context does not necessarily mean better results.

Compare:

    Minimal Context
    Normal Context
    Large Context

Measure:

    Accuracy
    Latency
    Token Usage
    Error Rate

---

# 34. Under-Context Evaluation

Compare performance when required context is intentionally removed.

Expected:

    Task Performance Decreases

This helps identify critical dependencies.

---

# 35. Ablation Testing

Ablation testing removes one context at a time.

Example:

    Full Context

then:

    Remove editing.clips

then:

    Remove editing.timeline

Measure the effect on task success.

---

# 36. Context Importance

A context is highly important when removing it causes a significant reduction in task performance.

Possible classification:

    Critical
    Important
    Useful
    Optional

---

# 37. Context Contribution

Measure the contribution of each context:

    Context A
        → +20% task success

    Context B
        → +2%

    Context C
        → 0%

This helps identify unnecessary context.

---

# 38. Context Dependency Discovery

Evaluation results can reveal hidden dependencies.

Example:

    rendering.md

appears to improve performance only when:

    codecs.md

is also available.

This may indicate a dependency relationship.

---

# 39. Model Evaluation

The same context should be evaluated across supported models when necessary.

Example:

    Model A
    Model B
    Model C

Measure:

    Task Success
    Tool Accuracy
    Context Sensitivity

---

# 40. Model-Specific Context

If a model requires different context structure, this should be explicitly documented.

Avoid silently modifying global context for one model.

---

# 41. Version Evaluation

When context changes:

    Context v1
        ↓
    Context v2

compare:

    Task Success
    Context Size
    Latency
    Errors
    Security

---

# 42. Regression Evaluation

A new context version must not significantly reduce task performance.

If:

    v1 = 94% success
    v2 = 82% success

the release should be investigated.

---

# 43. Evaluation Thresholds

Define acceptable thresholds.

Example:

    Task Success:
        >= 95%

    Security Violations:
        0

    Required Context Recall:
        >= 99%

Exact values should be configured according to project requirements.

---

# 44. Security Threshold

Security violations should normally have:

    Maximum:
        0

A context release with confirmed security leakage should fail evaluation.

---

# 45. Regression Threshold

A regression threshold may define the maximum acceptable degradation.

Example:

    Maximum Performance Drop:
        2%

A larger regression requires investigation.

---

# 46. Latency Evaluation

Measure:

    Context Discovery
    Context Selection
    Validation
    Building
    Token Estimation

---

# 47. Context Selection Latency

Track:

    p50
    p95
    p99

This is important when the Context Engine is used in real-time workflows.

---

# 48. Context Size Metrics

Track:

    Documents Selected
    Characters
    Tokens
    Sections
    Dependencies

---

# 49. Context Complexity

Measure:

    Dependency Count
    Dependency Depth
    Number of Rules
    Number of Conflicts
    Number of Sources

---

# 50. Evaluation Reports

Every evaluation run should produce a report containing:

    Evaluation ID
    Context Version
    Dataset Version
    Model
    Metrics
    Failures
    Warnings
    Timestamp

---

# 51. Evaluation Reproducibility

An evaluation must be reproducible.

Record:

    Context Snapshot
    Context Versions
    Dataset Version
    Model Version
    Configuration
    Evaluation Parameters

---

# 52. Evaluation Snapshot

A snapshot should allow reconstruction of:

    Input
    Selected Context
    Final Context
    Model Configuration
    Evaluation Result

---

# 53. Deterministic Evaluation

Where possible, evaluation should use deterministic settings.

Examples:

    Fixed Dataset
    Fixed Context
    Fixed Configuration
    Fixed Model Parameters

---

# 54. Randomized Evaluation

When randomness is required:

    Record Seed

This allows failed evaluations to be reproduced.

---

# 55. Human Evaluation

Automated metrics are not always sufficient.

Human reviewers may evaluate:

    Correctness
    Relevance
    Completeness
    Clarity
    Safety

---

# 56. Human Evaluation Rubric

Example:

    Relevance:
        0–5

    Completeness:
        0–5

    Correctness:
        0–5

    Safety:
        0–5

---

# 57. Blind Evaluation

When comparing two context versions, reviewers should not know which version is newer when possible.

This reduces evaluation bias.

---

# 58. Pairwise Evaluation

Compare:

    Context A
    Context B

for the same task.

Measure:

    Which produces better task performance?

---

# 59. A/B Evaluation

Production experiments may compare:

    Context Version A
    Context Version B

Only when security and governance requirements permit it.

---

# 60. Evaluation Isolation

Evaluation environments must not modify production context.

Use:

    Test Snapshot
    Evaluation Environment
    Isolated Storage

---

# 61. Evaluation Security

Evaluation datasets must not contain:

    Production Secrets
    Real Credentials
    Private User Data

Use synthetic or approved anonymized data.

---

# 62. Evaluation Drift

Evaluation datasets can become stale.

Review datasets when:

    Product Changes
    Architecture Changes
    Tool Changes
    Model Changes

---

# 63. Dataset Versioning

Evaluation datasets must be versioned.

Example:

    evaluation-dataset@1.4.0

---

# 64. Dataset Governance

Dataset changes should document:

    Added Tasks
    Removed Tasks
    Changed Expected Results
    Reason

---

# 65. Evaluation Categories

Evaluation reports should group failures into:

    Selection
    Relevance
    Completeness
    Priority
    Security
    Versioning
    Budget
    Tool Usage
    Task Performance

---

# 66. Failure Analysis

Every important failure should answer:

    What failed?
    Why did it fail?
    Which context was involved?
    Was context missing?
    Was context irrelevant?
    Was context contradictory?
    Was the context stale?
    How should it be fixed?

---

# 67. Root Cause Analysis

Possible root causes:

    Missing Dependency
    Incorrect Priority
    Poor Metadata
    Stale Context
    Wrong Version
    Selection Bug
    Budget Constraint
    Security Filter
    Model Limitation

---

# 68. Improvement Loop

Evaluation should feed back into the Context Engine.

Example:

    Evaluation Failure
        ↓
    Root Cause
        ↓
    Context Change
        ↓
    Test
        ↓
    Evaluation
        ↓
    Release

---

# 69. No Blind Optimization

Do not optimize context based only on:

    Token Reduction

A smaller context is not automatically better.

---

# 70. Optimization Objective

The goal is:

    Maximum Task Utility
    with
    Minimum Necessary Context

while preserving:

    Security
    Correctness
    Completeness

---

# 71. Context Utility

Conceptually:

    Utility =
    Task Benefit
    -
    Context Cost

Context cost includes:

    Tokens
    Latency
    Complexity
    Risk

---

# 72. Risk-Adjusted Utility

Security-sensitive context should receive higher importance than ordinary context.

Conceptually:

    Utility =
    Task Benefit
    +
    Security Value
    -
    Context Cost

---

# 73. Evaluation Automation

Evaluation should run automatically for:

    Critical Context Changes
    Architecture Changes
    Security Changes
    Major Version Changes

---

# 74. CI Evaluation

Recommended:

    Unit Tests
        ↓
    Integration Tests
        ↓
    Security Tests
        ↓
    Context Evaluation
        ↓
    Regression Evaluation
        ↓
    Build

---

# 75. Release Gate

A release may be blocked when:

    Security Violation
    Required Context Missing
    Critical Regression
    Invalid Context
    Unauthorized Dependency
    Evaluation Threshold Failure

---

# 76. Evaluation Metrics

Recommended metrics:

    Context Precision
    Context Recall
    Context Coverage
    Context Efficiency
    Task Success
    Token Usage
    Selection Latency
    Error Rate
    Security Violations

---

# 77. Dashboard

A future evaluation dashboard may show:

    Context Health
    Context Quality
    Task Success
    Token Usage
    Regression
    Security
    Latency

---

# 78. Historical Comparison

Compare:

    Current Version
    Previous Version
    Baseline

Example:

    v2.0:
        93% success

    v2.1:
        96% success

    Improvement:
        +3%

---

# 79. Baseline

Every important evaluation should have a baseline.

The baseline represents an approved known-good state.

---

# 80. Baseline Protection

Baseline changes require review.

Do not change the baseline simply to make a failing evaluation pass.

---

# 81. Evaluation Integrity

Evaluation results must not be manipulated by:

    Changing Dataset
    Changing Expected Results
    Removing Failed Tasks
    Changing Thresholds
    Modifying Context After Evaluation

without explicit documentation.

---

# 82. Auditability

Every evaluation should be traceable to:

    Context Version
    Dataset Version
    Model
    Configuration
    Commit
    Result

---

# 83. Architecture Rules

The Context Evaluation system must:

1. Measure context relevance.
2. Measure context completeness.
3. Measure context correctness.
4. Measure context consistency.
5. Measure context efficiency.
6. Measure task performance.
7. Measure token usage.
8. Measure latency.
9. Measure security.
10. Support golden evaluations.
11. Support regression evaluation.
12. Support ablation testing.
13. Support version comparison.
14. Support reproducible evaluation.
15. Version evaluation datasets.
16. Preserve evaluation baselines.
17. Prevent evaluation manipulation.
18. Integrate with CI.
19. Block critical releases when thresholds fail.
20. Feed evaluation results back into context improvement.

---

# 84. Golden Rules

1. Context quality must be measured, not assumed.
2. More context does not automatically mean better context.
3. Less context does not automatically mean better context.
4. Required context must be measurable.
5. Irrelevant context should be measurable.
6. Security violations must have zero tolerance where applicable.
7. Evaluation datasets must be versioned.
8. Evaluation results must be reproducible.
9. Context changes should be compared against a baseline.
10. Critical regressions must block release.
11. Evaluation must include security.
12. Evaluation must include task performance.
13. Evaluation must include token efficiency.
14. Evaluation must include latency for performance-sensitive workflows.
15. Failed evaluations require root-cause analysis.
16. Evaluation must not be optimized by manipulating the benchmark.
17. Human evaluation should supplement automated metrics where necessary.
18. Context importance can be discovered through ablation testing.
19. Evaluation results should drive context improvements.
20. The final objective is useful, secure, correct, and efficient context.