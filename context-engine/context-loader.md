# Context Loader

## Purpose

The Context Loader is responsible for discovering, reading, parsing, and normalizing context sources before they are passed to the Context Selector.

It does not decide whether a context is relevant.

Its responsibility is:

    Discover
      ↓
    Load
      ↓
    Parse
      ↓
    Normalize
      ↓
    Return Context

---

# 1. Responsibilities

The Context Loader is responsible for:

- Loading context files
- Reading context metadata
- Parsing Markdown
- Normalizing context documents
- Resolving context paths
- Detecting missing files
- Detecting invalid files
- Returning structured context objects

---

# 2. Non-Responsibilities

The Context Loader must not:

- Decide context relevance
- Build AI prompts
- Execute AI requests
- Execute tools
- Validate user permissions
- Modify project state
- Execute database mutations

These responsibilities belong to other components.

---

# 3. Loading Pipeline

The loading process follows:

    Context Request
        ↓
    Path Resolver
        ↓
    File Loader
        ↓
    Parser
        ↓
    Metadata Extractor
        ↓
    Normalizer
        ↓
    Context Document

---

# 4. Context Sources

The initial implementation supports:

    Markdown Files

Future implementations may support:

    JSON
    YAML
    Database
    API
    Vector Database
    Remote Storage

---

# 5. Markdown Context

The current project uses Markdown as the primary static context format.

Example:

    context/
    ├── product/
    ├── architecture/
    ├── video/
    ├── editing/
    ├── ai/
    └── rules/

Markdown provides:

- Human readability
- Git versioning
- Easy editing
- Easy review
- AI compatibility

---

# 6. Root Context Directory

The Context Loader must receive the root context directory through configuration.

Example:

    CONTEXT_ROOT=./context

The loader must not hardcode environment-specific absolute paths.

---

# 7. Path Resolution

Context paths should be resolved relative to the configured context root.

Example:

    context/editing/timeline.md

must resolve through:

    CONTEXT_ROOT
        +
    editing/timeline.md

---

# 8. Path Security

Context paths must never allow traversal outside the context root.

Reject paths such as:

    ../../secret.md

or equivalent traversal attempts.

---

# 9. Allowed Extensions

The initial loader should support:

    .md

Future support may include:

    .json
    .yaml
    .yml

Unsupported file types should be rejected.

---

# 10. File Discovery

The loader may discover available context files recursively.

Example:

    context/
      ↓
    product/
    architecture/
    video/
    editing/
    ai/
    rules/

The discovery result should contain normalized file references.

---

# 11. Explicit Loading

The system should also support loading a specific context document.

Example:

    editing/timeline.md

This avoids scanning the entire context directory for every request.

---

# 12. Batch Loading

Multiple context documents may be loaded together.

Example:

    [
      "editing/timeline.md",
      "editing/clips.md",
      "editing/operations.md"
    ]

The loader should process them consistently.

---

# 13. File Encoding

Context files should use:

    UTF-8

Invalid encoding should produce a structured error.

---

# 14. Empty Files

Empty context documents should be detected.

Depending on configuration:

    Warning

or:

    Error

Critical context should not silently become empty.

---

# 15. File Size

Context files should have reasonable size limits.

This protects the system from accidentally loading extremely large files.

Example:

    Maximum Context File Size

The exact value belongs to application configuration.

---

# 16. Metadata

The loader should extract metadata when available.

Possible metadata:

    id
    name
    category
    version
    priority
    dependencies
    description

---

# 17. Metadata Format

Future context files may use front matter.

Example:

    ---
    id: editing.timeline
    category: editing
    version: 1.0.0
    priority: 80
    dependencies:
      - editing.clips
    ---

    # Timeline

    ...

The loader should keep metadata separate from content.

---

# 18. Content Separation

A loaded context document should conceptually contain:

    {
      metadata: {},
      content: "..."
    }

The Markdown body should not be mixed with metadata internally.

---

# 19. Normalized Context

The loader should return a normalized structure.

Example:

    {
      id: "editing.timeline",
      category: "editing",
      version: "1.0.0",
      priority: 80,
      dependencies: [],
      source: "editing/timeline.md",
      content: "..."
    }

---

# 20. Context ID

Every context document should have a stable identifier.

Example:

    editing.timeline
    video.ffmpeg
    rules.security

The ID should not depend on temporary filesystem paths.

---

# 21. Context Category

The loader should identify the context category.

Examples:

    product
    architecture
    video
    editing
    ai
    rules

---

# 22. Category Validation

The loader should validate category values.

Unknown categories may:

- Produce a warning
- Be rejected

depending on configuration.

---

# 23. Version

Context versions should be available to the Context Engine.

Example:

    1.0.0

If a document has no explicit version, the loader may assign a default version according to project policy.

---

# 24. Priority

Priority should be represented as metadata when possible.

Example:

    priority: 80

If priority is not explicitly defined, the Context Engine may derive it from category.

The loader itself should not implement complex priority logic.

---

# 25. Dependencies

Dependencies should be represented explicitly.

Example:

    dependencies:
      - editing.timeline
      - editing.clips

The loader reads dependencies.

Dependency resolution belongs to the Context Engine.

---

# 26. Content Integrity

The loader should be able to calculate a content hash.

Example:

    SHA-256

The hash can be used for:

- Cache validation
- Change detection
- Context snapshots
- Debugging

---

# 27. Change Detection

A context document has changed when its content hash changes.

Example:

    Old Hash
        ↓
    New Hash
        ↓
    Different
        ↓
    Context Changed

---

# 28. Cache Integration

The Context Loader may use a cache.

Pipeline:

    Request
      ↓
    Cache Lookup
      ↓
    Hit → Return
      ↓
    Miss
      ↓
    Read File
      ↓
    Parse
      ↓
    Cache
      ↓
    Return

---

# 29. Cache Key

A cache key should uniquely identify the context version.

Example:

    context:editing.timeline:1.0.0:<hash>

---

# 30. Cache Invalidation

Cache entries must be invalidated when:

- File changes
- Version changes
- Dependencies change
- Context configuration changes

---

# 31. Loader Errors

Errors should be structured.

Possible errors:

    CONTEXT_FILE_NOT_FOUND
    CONTEXT_FILE_READ_ERROR
    CONTEXT_FILE_INVALID
    CONTEXT_FILE_TOO_LARGE
    CONTEXT_UNSUPPORTED_FORMAT
    CONTEXT_PATH_INVALID
    CONTEXT_METADATA_INVALID

---

# 32. Missing Context

A missing optional context may produce:

    Warning

A missing required context should produce:

    Error

The Context Selector or Resolver determines whether the context is required.

---

# 33. Permission Errors

Filesystem permission errors must not be silently ignored.

Example:

    CONTEXT_FILE_READ_ERROR

The error should contain enough information for debugging without exposing sensitive filesystem details.

---

# 34. Error Boundaries

The loader must isolate failures.

If:

    editing/timeline.md

fails to load, unrelated context files should not automatically be considered invalid.

---

# 35. Batch Loading Failure

For batch loading, the loader should return structured results.

Example:

    {
      loaded: [],
      failed: []
    }

This allows the Context Engine to determine whether the failure is critical.

---

# 36. Context Ordering

The loader should preserve deterministic ordering.

For directory discovery:

    Alphabetical Order

or another explicitly defined ordering may be used.

Final priority ordering belongs to the Context Priority Resolver.

---

# 37. No Hidden Transformation

The loader should not rewrite the meaning of context content.

It may:

- Parse
- Normalize
- Extract metadata

It must not:

- Summarize
- Rewrite
- Change instructions
- Remove rules

---

# 38. Markdown Parsing

Markdown parsing should preserve the original semantic content.

Headings, lists, code blocks, and paragraphs should remain understandable to downstream components.

---

# 39. Code Blocks

Code blocks inside context documents must remain intact.

Example:

    ```typescript
    const example = true;
    ```

The loader must not execute code found inside context documents.

---

# 40. Links

Links inside context documents should be treated as content.

The loader must not automatically fetch external URLs.

External retrieval must be handled by a separate, controlled component.

---

# 41. Images

Image references inside Markdown should not automatically trigger external downloads.

If visual context is required, a dedicated media retrieval mechanism should handle it.

---

# 42. External Files

The initial loader should only load files explicitly located inside the configured context root.

External filesystem access must be disabled by default.

---

# 43. Remote Context

Remote context may be supported in the future.

Example:

    Context API
        ↓
    Remote Loader
        ↓
    Normalized Context

Remote loading must include:

- Authentication
- Timeout
- Size limit
- Validation
- Integrity checks

---

# 44. Database Context

Database-backed context should use a dedicated adapter.

Do not place database queries directly inside the file loader.

Architecture:

    Context Loader
        ↓
    Context Source Adapter
        ├── File
        ├── Database
        ├── API
        └── Vector Store

---

# 45. Interface

The loader should expose a stable abstraction.

Conceptually:

    load(contextId)
    loadMany(contextIds)
    discover()
    exists(contextId)

Implementation details may change.

---

# 46. Dependency Injection

The Context Loader should receive dependencies through injection where practical.

Examples:

    File System
    Parser
    Cache
    Logger
    Configuration

This improves testing and portability.

---

# 47. Testing

The loader must be independently testable.

Tests should cover:

    File Loading
    File Discovery
    Path Resolution
    Path Traversal
    Metadata Parsing
    Encoding
    Missing Files
    Invalid Files
    Large Files
    Cache
    Hash Generation

---

# 48. Mock Filesystem

Unit tests should avoid depending on the developer's actual repository when possible.

Use:

    Mock Filesystem

or:

    Temporary Test Directory

---

# 49. Determinism

Given the same:

    Context Files
    Configuration

the loader should produce the same normalized context representation.

---

# 50. Performance

The loader should minimize unnecessary filesystem operations.

Prefer:

    Batch Discovery
    Caching
    Content Hashing
    Lazy Loading

when appropriate.

---

# 51. Lazy Loading

The loader should support loading only requested contexts.

Example:

    Editing Request

does not require:

    product/users.md

unless explicitly required.

---

# 52. Eager Loading

Eager loading may be used for small, frequently accessed critical contexts.

Example:

    rules/security.md
    rules/ai-rules.md

The strategy should be configurable.

---

# 53. Critical Context

Critical contexts may be preloaded or cached.

Examples:

    rules/security.md
    rules/architecture.md
    rules/ai-rules.md

Failure to load critical rules should prevent unsafe AI execution.

---

# 54. Security Rules

The Context Loader must:

1. Prevent path traversal.
2. Restrict access to the context root.
3. Validate file types.
4. Limit file size.
5. Never execute file content.
6. Never expose secrets.
7. Avoid uncontrolled external requests.
8. Sanitize error output.
9. Validate metadata.
10. Keep user/project contexts isolated.

---

# 55. Architecture Rules

The Context Loader must:

1. Remain independent from AI providers.
2. Separate loading from selection.
3. Separate loading from priority resolution.
4. Support multiple context sources through adapters.
5. Return normalized context documents.
6. Support caching.
7. Support versioning.
8. Support deterministic behavior.
9. Remain independently testable.
10. Avoid business logic.

---

# 56. Golden Rules

1. Load context; do not decide relevance.
2. Never trust filesystem paths from external input.
3. Never allow path traversal.
4. Never execute Markdown content.
5. Never automatically fetch external links.
6. Keep metadata separate from content.
7. Keep context IDs stable.
8. Preserve original context meaning.
9. Return structured errors.
10. Support deterministic loading.
11. Cache static context when beneficial.
12. Hash content for change detection.
13. Support explicit and batch loading.
14. Keep critical context failures visible.
15. Keep the loader small and focused.
