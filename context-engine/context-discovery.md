# Context Discovery

## Purpose

Context Discovery مسئول پیدا کردن Contextهای بالقوه مرتبط با یک درخواست، Task، پروژه، محیط یا وضعیت فعلی سیستم است.

Discovery مشخص می‌کند:

- چه Contextهایی وجود دارند؟
- کدام Contextها ممکن است مرتبط باشند؟
- از کجا باید آن‌ها را پیدا کرد؟
- چه Contextهایی باید وارد مرحله Selection شوند؟
- چگونه بدون Load کردن همه Contextها، Candidateهای مناسب پیدا شوند؟

Discovery تصمیم نهایی درباره استفاده از Context را نمی‌گیرد.

---

# 1. Core Principle

Discovery باید:

> Candidate پیدا کند، نه اینکه تصمیم نهایی بگیرد.

Flow:

    Request
       ↓
    Discovery
       ↓
    Candidates
       ↓
    Selector
       ↓
    Priority
       ↓
    Loader
       ↓
    Builder

---

# 2. Discovery vs Selection

Discovery پاسخ می‌دهد:

    چه Contextهایی ممکن است مرتبط باشند؟

Selection پاسخ می‌دهد:

    کدام Contextها واقعاً باید استفاده شوند؟

Example:

    Query:
        How should authentication be implemented?

Discovery:

    security.authentication
    security.authorization
    security.jwt
    security.oauth
    backend.api

Selector بعداً تصمیم می‌گیرد کدام Contextها واقعاً لازم هستند.

---

# 3. Discovery Input

Discovery ممکن است این موارد را دریافت کند:

    User Query
    Task
    Project
    Environment
    Active Context
    Context Metadata
    Context Index
    Context Graph
    Previous Context
    System Policies
    Security Context
    Version Constraints

---

# 4. Discovery Output

خروجی Discovery مجموعه‌ای از Candidateها است.

Example:

    {
      "candidates": [
        {
          "contextId": "security.authentication",
          "score": 0.94
        },
        {
          "contextId": "security.jwt",
          "score": 0.87
        }
      ]
    }

---

# 5. Discovery Candidate

هر Candidate بهتر است شامل موارد زیر باشد:

    contextId
    version
    source
    matchReason
    score
    matchedTags
    matchedKeywords
    matchedSections
    discoveryMethods

---

# 6. Discovery Sources

Discovery می‌تواند از منابع مختلف استفاده کند:

    Filesystem
    Metadata Registry
    Database
    Search Index
    Vector Store
    Git Repository
    API
    Context Graph
    Cache
    Memory

---

# 7. Metadata Discovery

Metadata یکی از مهم‌ترین منابع Discovery است.

Discovery می‌تواند براساس:

    id
    name
    description
    category
    scope
    tags
    keywords
    contextType
    relationships
    dependencies

Context پیدا کند.

---

# 8. ID Discovery

اگر Query مستقیماً به Context ID اشاره کند:

    Use backend.database

Discovery باید بتواند:

    backend.database

را مستقیماً پیدا کند.

Direct ID Match معمولاً باید از قوی‌ترین Discovery Signals باشد.

---

# 9. Name Discovery

Example:

    PostgreSQL Architecture

ممکن است به:

    architecture.database.postgresql

متصل شود.

---

# 10. Keyword Discovery

Query:

    database transaction

می‌تواند Contextهایی با:

    database
    transaction
    postgresql
    acid

را پیدا کند.

---

# 11. Tag Discovery

Context:

    tags:
        backend
        database
        postgresql
        transaction

Query:

    backend database

می‌تواند این Context را Candidate کند.

---

# 12. Semantic Discovery

Discovery می‌تواند از Semantic Search استفاده کند.

Example:

    Query:
        How do I safely handle multiple database writes?

ممکن است کلمه `transaction` مستقیماً در Query وجود نداشته باشد.

Semantic Discovery می‌تواند:

    database.transactions

را پیدا کند.

---

# 13. Lexical Discovery

Lexical Search براساس تطابق کلمات عمل می‌کند.

Example:

    Query:
        PostgreSQL transaction

Context:

    PostgreSQL Transaction Architecture

Match بالا خواهد بود.

---

# 14. Hybrid Discovery

بهترین معماری معمولاً ترکیبی است:

    Lexical Search
        +
    Semantic Search
        +
    Metadata Filtering
        +
    Graph Traversal

سپس:

    Merge
       ↓
    Deduplicate
       ↓
    Rank
       ↓
    Candidates

---

# 15. Discovery Pipeline

    Request
       ↓
    Normalize
       ↓
    Extract Signals
       ↓
    Metadata Search
       ↓
    Keyword Search
       ↓
    Semantic Search
       ↓
    Graph Expansion
       ↓
    Deduplication
       ↓
    Candidate Filtering
       ↓
    Candidate Ranking
       ↓
    Candidate Set

---

# 16. Query Normalization

قبل از Discovery باید Query نرمال شود.

Example:

    How can I configure postgres transactions?

ممکن است به:

    postgres
    postgresql
    database
    transaction
    transactions

نرمال شود.

Normalization باید deterministic باشد.

---

# 17. Signal Extraction

Discovery باید Signalهای مهم را استخراج کند.

Example:

    Domain:
        backend

    Technology:
        PostgreSQL

    Concept:
        transaction

    Intent:
        configuration

---

# 18. Domain Detection

Domain می‌تواند شامل موارد زیر باشد:

    frontend
    backend
    database
    security
    deployment
    testing
    AI
    video

Domain Detection باعث کاهش Search Space می‌شود.

---

# 19. Technology Detection

Technology Signals:

    React
    Node.js
    PostgreSQL
    Redis
    Docker
    Kubernetes
    FFmpeg

این Signalها می‌توانند Candidateها را محدود کنند.

---

# 20. Intent Detection

Intent ممکن است شامل:

    explain
    implement
    debug
    design
    compare
    configure
    validate
    test
    optimize
    migrate

باشد.

---

# 21. Context Type Detection

Discovery می‌تواند نوع Context مورد نیاز را تشخیص دهد.

Example:

    How should authentication work?

ممکن است نیازمند:

    RULE
    ARCHITECTURE
    SECURITY
    REFERENCE

باشد.

---

# 22. Scope Filtering

اگر Task مربوط به Backend است:

    scope:
        backend

Contextهای کاملاً نامرتبط می‌توانند حذف یا جریمه شوند.

---

# 23. Environment Filtering

اگر Task مربوط به Production است:

    environment:
        production

Contextهای Development ممکن است Candidate ضعیف‌تری باشند.

---

# 24. Security Filtering

Discovery نباید Contextهایی را که User اجازه دسترسی به آن‌ها ندارد وارد Candidate Set کند.

Security باید بخشی از Discovery Pipeline باشد.

---

# 25. Access Control

هر Candidate باید از نظر دسترسی بررسی شود:

    Can User Access?
    Can Agent Access?
    Can Project Access?
    Can Environment Access?

---

# 26. Source Filtering

ممکن است فقط Sourceهای خاص مجاز باشند.

Example:

    Allowed Sources:
        internal-git
        approved-registry

---

# 27. Version Filtering

Discovery باید Versionهای ناسازگار را شناسایی کند.

Example:

    Required:
        >=2.0.0

Context:

    1.3.0

نباید Candidate معتبر برای این Constraint باشد.

---

# 28. Status Filtering

Contextهای زیر معمولاً نباید Candidate عادی باشند:

    RETIRED
    BLOCKED
    ARCHIVED

---

# 29. Deprecated Context

Contextهای Deprecated می‌توانند فقط برای موارد خاص Discover شوند:

    Migration
    Legacy Support
    Historical Analysis

---

# 30. Freshness Filtering

Contextهای Stale ممکن است:

    Excluded
    Penalized
    Marked

شوند.

---

# 31. Authority Filtering

Discovery باید Authority Context را بشناسد.

Examples:

    SYSTEM
    POLICY
    PROJECT
    DOMAIN
    USER
    EXTERNAL

Authority در مراحل بعدی Resolution نیز استفاده می‌شود.

---

# 32. Trust Filtering

Contextها ممکن است:

    TRUSTED
    VERIFIED
    UNVERIFIED
    UNKNOWN
    BLOCKED

باشند.

Context با Trust برابر `BLOCKED` نباید در استفاده عادی وارد Pipeline شود.

---

# 33. Discovery Index

برای Discovery سریع، سیستم می‌تواند Index داشته باشد.

Example:

    context-index/
        by-id
        by-tag
        by-keyword
        by-category
        by-scope
        by-owner
        by-version

---

# 34. Inverted Index

برای Keyword Search می‌توان از Inverted Index استفاده کرد.

Example:

    transaction
        ↓
    database.transactions
        ↓
    postgresql.transactions

---

# 35. Vector Index

Semantic Discovery می‌تواند از Vector Index استفاده کند.

Flow:

    Query Embedding
        ↓
    Vector Search
        ↓
    Nearest Contexts

---

# 36. Graph Discovery

Context Graph می‌تواند Contextهای مرتبط را پیدا کند.

Example:

    security.authentication
          ↓
    security.jwt
          ↓
    security.tokens
          ↓
    security.authorization

---

# 37. Graph Expansion

Graph Expansion باید محدود باشد.

Example:

    maxDepth:
        2

تا کل Context Graph وارد Candidate Set نشود.

---

# 38. Dependency Expansion

اگر:

    api.orders

به:

    database.orders

وابسته باشد، Discovery می‌تواند Dependency را Candidate کند.

---

# 39. Parent Expansion

Discovery ممکن است Parent Context را نیز پیدا کند.

Example:

    backend.database.postgresql

Parent:

    backend.database

---

# 40. Child Expansion

Child Contextها معمولاً فقط در صورت نیاز اضافه می‌شوند.

Example:

    backend.database

نباید الزاماً تمام Childهای خود را وارد Candidate Set کند.

---

# 41. Reference Expansion

Referenceها می‌توانند Candidate ایجاد کنند، اما معمولاً نسبت به Required Dependency اهمیت کمتری دارند.

---

# 42. Conflict Discovery

Discovery باید Conflictهای شناخته‌شده را نیز شناسایی کند.

Example:

    database.postgresql

Conflict:

    database.mongodb-primary

Conflict information بعداً توسط Resolution استفاده می‌شود.

---

# 43. Candidate Deduplication

ممکن است یک Context از چند روش پیدا شود:

    Metadata Search
    Semantic Search
    Keyword Search
    Graph Search

همگی ممکن است:

    database.transactions

را پیدا کنند.

باید فقط یک Candidate باقی بماند.

---

# 44. Deduplication Key

کلید مناسب می‌تواند:

    contextId + version

باشد.

---

# 45. Candidate Evidence

Discovery باید بتواند توضیح دهد چرا Context پیدا شده است.

Example:

    {
      "contextId": "database.transactions",
      "evidence": [
        "keyword: transaction",
        "tag: database",
        "semantic-score: 0.91"
      ]
    }

---

# 46. Explainable Discovery

Discovery باید تا حد امکان قابل توضیح باشد.

Example:

    Selected as candidate because:

    - Query matched "transaction"
    - Context scope = backend
    - Tag matched "database"
    - Semantic similarity = 0.91

---

# 47. Discovery Score

Candidate می‌تواند Score داشته باشد.

Example:

    Discovery Score =
        lexicalScore
        +
        semanticScore
        +
        metadataScore
        +
        scopeScore
        +
        freshnessScore

فرمول واقعی باید با سیاست Selector/Priority هماهنگ باشد.

---

# 48. Discovery vs Priority

Discovery:

    Candidate Relevance

Priority:

    Importance

Authority:

    Decision Influence

این سه مفهوم نباید با یکدیگر یکی شوند.

---

# 49. Discovery Threshold

ممکن است حداقل Score تعریف شود.

Example:

    semanticScore >= 0.75

Candidateهای ضعیف‌تر حذف می‌شوند.

Threshold باید configurable باشد.

---

# 50. Candidate Limit

Discovery نباید تعداد نامحدودی Candidate تولید کند.

Example:

    maxCandidates:
        50

---

# 51. Top-K Discovery

می‌توان فقط Top-K Candidate را به Selector تحویل داد.

Example:

    Top 20

---

# 52. Discovery Budget

Discovery باید Resource Budget داشته باشد.

Possible limits:

    Max Search Time
    Max Candidates
    Max Graph Depth
    Max Vector Results
    Max Metadata Queries

---

# 53. Discovery Timeout

اگر Discovery بیش از زمان مشخص طول بکشد:

    DISCOVERY_TIMEOUT

باید ایجاد شود.

---

# 54. Partial Discovery

در صورت Timeout ممکن است سیستم نتایج موجود را برگرداند.

اما باید وضعیت:

    partial: true

قابل مشاهده باشد.

---

# 55. Discovery Failure

Possible failures:

    INDEX_UNAVAILABLE
    VECTOR_STORE_UNAVAILABLE
    METADATA_UNAVAILABLE
    SOURCE_UNAVAILABLE
    TIMEOUT
    INVALID_QUERY
    ACCESS_DENIED

---

# 56. Failure Strategy

سیستم می‌تواند Strategyهای زیر داشته باشد:

    Retry
    Fallback
    Partial Result
    Fail Closed
    Fail Open

Fail Open نباید برای Security Contextها بدون Policy استفاده شود.

---

# 57. Discovery Fallback

Example:

    Semantic Search
        ↓
      Failure
        ↓
    Keyword Search
        ↓
    Metadata Search

---

# 58. Discovery Cache

Discovery Results می‌توانند Cache شوند.

Cache Key ممکن است شامل:

    query
    project
    environment
    version
    securityContext

باشد.

---

# 59. Cache Invalidation

Cache باید هنگام تغییر موارد زیر Invalid شود:

    Context Added
    Context Removed
    Context Updated
    Metadata Changed
    Access Policy Changed
    Index Changed

---

# 60. Discovery Consistency

Discovery نباید Context قدیمی را بدون اطلاع سیستم برگرداند.

Index و Source باید Consistency Policy مشخصی داشته باشند.

---

# 61. Event-Driven Discovery

تغییر Context می‌تواند Event ایجاد کند:

    context.created
    context.updated
    context.deleted
    context.deprecated

این Eventها می‌توانند Index را به‌روزرسانی کنند.

---

# 62. Incremental Indexing

به‌جای Rebuild کل Index:

    Changed Context
        ↓
    Update Relevant Index Entries

---

# 63. Full Reindex

گاهی باید کل Index دوباره ساخته شود.

Examples:

    Schema Change
    Index Corruption
    Migration
    Major Architecture Change

---

# 64. Discovery Registration

Context جدید:

    Created
       ↓
    Validated
       ↓
    Registered
       ↓
    Indexed
       ↓
    Discoverable

---

# 65. Unregistered Context

Contextی که Register نشده است نباید به‌عنوان Context فعال Discover شود.

---

# 66. Discovery and Lifecycle

Lifecycle Status باید در Discovery لحاظ شود.

Example:

    DRAFT

ممکن است فقط برای:

    Development
    Testing

قابل Discovery باشد.

---

# 67. Discovery and Version

Discovery ممکن است چند Version را پیدا کند.

Example:

    database@1.0.0
    database@2.0.0
    database@3.0.0

Resolution یا Selector نسخه مناسب را تعیین می‌کند.

---

# 68. Latest Version

Latest الزاماً Best نیست.

Example:

    Latest:
        3.0.0

    Compatible:
        2.5.0

اگر Consumer فقط `2.x` را پشتیبانی کند:

    2.5.0

مناسب‌تر است.

---

# 69. Version Compatibility

Discovery باید Compatibility Information را همراه Candidate نگه دارد.

---

# 70. Multi-Tenant Discovery

در Multi-Tenant Systems:

    Tenant
       ↓
    Allowed Namespace
       ↓
    Discovery

هر Tenant نباید Context Tenant دیگر را Discover کند.

---

# 71. Project Isolation

Contextهای Project A نباید بدون اجازه در Project B Discover شوند.

---

# 72. Environment Isolation

Contextهای Production نباید بدون Policy به‌عنوان Context معتبر Development استفاده شوند.

---

# 73. User-Specific Context

User Context ممکن است شامل:

    Preferences
    Working Style
    Temporary Instructions

باشد.

این Contextها باید Scope مشخص داشته باشند.

---

# 74. Session Discovery

Contextهای Session می‌توانند Discovery را محدود کنند.

Example:

    Current Project:
        video-editor

Discovery ابتدا Contextهای همین Project را بررسی می‌کند.

---

# 75. Task-Aware Discovery

Task یکی از مهم‌ترین Discovery Signals است.

Example:

    Task:
        Optimize PostgreSQL queries

Potential Candidates:

    database.performance
    database.indexing
    postgresql.query-optimization

---

# 76. Context-Aware Discovery

Contextهای قبلی نیز می‌توانند Discovery را بهبود دهند.

Example:

    Current Context:
        backend.database

Query:

    How should caching work?

Potential Candidates:

    backend.cache
    database.cache
    redis

---

# 77. Negative Context

Discovery ممکن است Contextهای ممنوع را نیز بشناسد.

Example:

    do-not-use:
        legacy.database

این موارد باید قبل از Selection فیلتر شوند.

---

# 78. Exclusion Rules

Metadata ممکن است مشخص کند:

    exclude:
        frontend.*

---

# 79. Discovery Policies

Policy می‌تواند تعیین کند:

    Allowed Sources
    Allowed Scopes
    Minimum Trust
    Minimum Freshness
    Maximum Candidates
    Maximum Graph Depth
    Allowed Context Types

---

# 80. Discovery Security Boundary

Discovery نباید Contextهایی را که User مجاز به مشاهده آن‌ها نیست حتی به شکل Candidate Metadata افشا کند.

---

# 81. Sensitive Metadata

موارد زیر ممکن است Sensitive باشند:

    contextId
    name
    description
    tags
    owner
    source

Discovery باید Access Control را روی Metadata نیز اعمال کند.

---

# 82. Namespace Discovery

Namespace می‌تواند Search Space را محدود کند.

Example:

    Query Scope:
        video.*

Discovery نباید Contextهای:

    finance.*
    security.*
    unrelated.*

را بدون دلیل وارد Candidate Set کند.

---

# 83. Namespace Wildcards

ممکن است از Pattern استفاده شود:

    backend.*
    security.authentication.*
    video.rendering.*

Wildcardها باید deterministic باشند.

---

# 84. Exact Match Priority

در صورت وجود Exact Match:

    Exact ID
        >
    Exact Name
        >
    Keyword Match
        >
    Semantic Match

این ترتیب باید با Policy واقعی سیستم قابل تغییر باشد.

---

# 85. Discovery Ordering

Discovery باید Ordering قابل پیش‌بینی داشته باشد.

مثلاً:

    Exact Match
    Metadata Match
    Lexical Match
    Semantic Match
    Graph Match

---

# 86. Discovery Reproducibility

یک Query یکسان با شرایط یکسان باید نتیجه قابل تکراری تولید کند.

برای Reproducibility باید موارد زیر ثابت یا Snapshot شوند:

    Index Version
    Context Version
    Metadata Version
    Search Configuration
    Ranking Configuration

---

# 87. Discovery Snapshot

برای اجرای قابل بازتولید می‌توان Snapshot ثبت کرد.

Example:

    discoverySnapshot:
        indexVersion: "12"
        metadataVersion: "5"
        rankingVersion: "2"

---

# 88. Discovery Audit

Discovery باید در صورت نیاز ثبت کند:

    Query
    User
    Project
    Environment
    Candidate Count
    Search Methods
    Filters
    Index Version
    Duration

---

# 89. Discovery Trace

Example:

    Discovery Trace

    Query
        ↓
    Normalize
        ↓
    Metadata Search
        ↓
    32 Candidates
        ↓
    Semantic Search
        ↓
    18 Candidates
        ↓
    Security Filter
        ↓
    12 Candidates
        ↓
    Deduplication
        ↓
    9 Candidates

---

# 90. Discovery Metrics

مهم‌ترین Metrics:

    discovery_requests_total
    discovery_duration
    discovery_candidates_count
    discovery_cache_hit_rate
    discovery_cache_miss_rate
    discovery_timeout_total
    discovery_failure_total
    discovery_zero_result_total
    discovery_partial_result_total

---

# 91. Discovery Quality Metrics

کیفیت Discovery را می‌توان با:

    Recall
    Precision
    Candidate Coverage
    False Positive Rate
    False Negative Rate

اندازه‌گیری کرد.

---

# 92. Zero-Result Discovery

اگر Discovery هیچ Candidate پیدا نکند:

    ZERO_RESULTS

باید ثبت شود.

---

# 93. Zero-Result Strategy

ممکن است سیستم:

    Broaden Query
    Remove Optional Filters
    Search Parent Context
    Search Semantic Index
    Use Fallback Source
    Ask for Clarification

را انجام دهد.

---

# 94. Over-Discovery

اگر تعداد Candidateها بیش از حد باشد:

    OVER_DISCOVERY

ممکن است رخ دهد.

مثال:

    Query:
        database

نتیجه:

    5000 contexts

این نتیجه برای مرحله بعد مناسب نیست.

---

# 95. Under-Discovery

اگر Context مرتبط وجود داشته باشد اما پیدا نشود:

    UNDER_DISCOVERY

رخ داده است.

این مشکل معمولاً از:

    Poor Indexing
    Poor Metadata
    Poor Embeddings
    Wrong Query Processing
    Excessive Filtering

ناشی می‌شود.

---

# 96. Discovery Recall

Recall نشان می‌دهد چند Context مرتبط واقعاً پیدا شده‌اند.

Example:

    Relevant Contexts:
        10

    Discovered:
        8

    Recall:
        80%

---

# 97. Discovery Precision

Precision نشان می‌دهد چه مقدار از Candidateها واقعاً مرتبط بوده‌اند.

Example:

    Candidates:
        20

    Relevant:
        12

    Precision:
        60%

---

# 98. Discovery Tradeoff

Discovery معمولاً باید بین:

    High Recall

و:

    High Precision

تعادل ایجاد کند.

Selector بعداً می‌تواند Candidateها را دقیق‌تر محدود کند.

---

# 99. Discovery Evaluation Dataset

برای Evaluation باید Queryهای واقعی ذخیره شوند.

Example:

    Query:
        How should JWT refresh tokens work?

Expected Contexts:

    security.jwt
    security.tokens
    authentication.refresh

---

# 100. Discovery Regression Testing

هر تغییر در:

    Index
    Embedding Model
    Metadata
    Ranking
    Filtering

باید روی Dataset تست شود.

---

# 101. Discovery Test Types

Tests:

    Exact Match Tests
    Keyword Tests
    Semantic Tests
    Metadata Tests
    Security Tests
    Version Tests
    Dependency Tests
    Conflict Tests
    Regression Tests

---

# 102. Security Discovery Testing

باید بررسی شود:

    Unauthorized Contexts Are Not Returned
    Restricted Metadata Is Not Leaked
    Cross-Tenant Contexts Are Not Returned
    Blocked Contexts Are Not Returned

---

# 103. Discovery Abuse Prevention

Discovery باید در برابر:

    Query Flooding
    Extremely Large Queries
    Wildcard Abuse
    Expensive Semantic Searches
    Graph Expansion Abuse

محافظت شود.

---

# 104. Rate Limiting

Discovery API ممکن است Rate Limit داشته باشد.

Example:

    100 requests/minute

مقدار واقعی باید براساس Capacity تعیین شود.

---

# 105. Query Size Limit

Query باید Maximum Size داشته باشد.

Example:

    maxQueryLength:
        10KB

---

# 106. Graph Depth Limit

Graph Discovery باید محدودیت داشته باشد.

Example:

    maxDepth:
        3

---

# 107. Result Size Limit

Discovery نباید Candidateهای نامحدود تولید کند.

Example:

    maxCandidates:
        100

---

# 108. Discovery Cost

هر Discovery Method هزینه متفاوتی دارد.

Example:

    ID Search:
        Very Low

    Metadata Search:
        Low

    Keyword Search:
        Low

    Vector Search:
        Medium

    Graph Expansion:
        Medium/High

---

# 109. Cost-Aware Discovery

سیستم می‌تواند ابتدا روش‌های ارزان‌تر را اجرا کند.

Example:

    Exact ID
        ↓
    Metadata
        ↓
    Keyword
        ↓
    Semantic
        ↓
    Graph

---

# 110. Early Termination

اگر Candidate کافی با کیفیت مناسب پیدا شد:

    Stop Discovery

این کار هزینه را کاهش می‌دهد.

---

# 111. Discovery Parallelization

Searchهای مستقل می‌توانند موازی اجرا شوند:

    Metadata Search
         │
         ├── Keyword Search
         ├── Semantic Search
         └── Graph Search

سپس:

    Merge

---

# 112. Discovery Backpressure

در Load بالا Discovery باید بتواند:

    Queue
    Rate Limit
    Reduce Search Depth
    Use Cache
    Degrade Gracefully

---

# 113. Discovery Availability

اگر یکی از Search Sources از دسترس خارج شد، کل Discovery الزاماً نباید Fail شود.

Example:

    Vector Store:
        DOWN

ولی:

    Metadata Index:
        UP

Discovery می‌تواند با Metadata ادامه دهد.

---

# 114. Graceful Degradation

مثال:

    Full Discovery:
        Metadata + Lexical + Semantic + Graph

در شرایط محدود:

    Degraded Discovery:
        Metadata + Lexical

---

# 115. Discovery Health

Health وضعیت منابع را مشخص می‌کند:

    Metadata Index:
        HEALTHY

    Vector Index:
        DEGRADED

    Graph:
        HEALTHY

---

# 116. Discovery Dependency Health

Discovery باید وابستگی‌های خود را Monitor کند.

---

# 117. Discovery Registration Lifecycle

Context Lifecycle:

    CREATED
       ↓
    VALIDATED
       ↓
    REGISTERED
       ↓
    INDEXED
       ↓
    DISCOVERABLE
       ↓
    DEPRECATED
       ↓
    RETIRED

---

# 118. Context Removal

وقتی Context حذف می‌شود:

    Remove From Index
    Remove From Cache
    Update Graph
    Update References
    Emit Event

---

# 119. Context Update

وقتی Context تغییر می‌کند:

    Validate
       ↓
    Update Metadata
       ↓
    Update Index
       ↓
    Invalidate Cache
       ↓
    Emit Event

---

# 120. Discovery and Sources

Sources مشخص می‌کنند Context از کجا آمده است.

Discovery مشخص می‌کند Context چطور پیدا شود.

---

# 121. Discovery and Metadata

Metadata Discovery را قابل جستجو می‌کند.

---

# 122. Discovery and Structure

Structure امکان Discovery در سطح:

    Package
    Context
    Section
    Chunk

را فراهم می‌کند.

---

# 123. Discovery and Selector

Discovery Candidate تولید می‌کند.

Selector Candidateها را ارزیابی و انتخاب می‌کند.

---

# 124. Discovery and Priority

Discovery:

    Relevance Signal

Priority:

    Importance Signal

---

# 125. Discovery and Loader

Discovery فقط ID/Reference مناسب را پیدا می‌کند.

Loader بعداً Content را Load می‌کند.

این Separation باعث جلوگیری از Load غیرضروری می‌شود.

---

# 126. Discovery and Builder

Builder نباید مسئول Search باشد.

Builder باید Contextهای انتخاب‌شده را Compose کند.

---

# 127. Discovery and Validation

Contextهای Discover شده باید قبل از استفاده Validation شوند.

---

# 128. Discovery and Security

Security باید:

    Access
    Trust
    Classification
    Source Authorization

را کنترل کند.

---

# 129. Discovery and Evaluation

Evaluation مشخص می‌کند Discovery چقدر خوب عمل می‌کند.

Metrics:

    Recall
    Precision
    Coverage
    Latency
    Cost

---

# 130. Discovery and Observability

Observability باید:

    Duration
    Candidate Count
    Search Method
    Cache Hit
    Errors
    Zero Results

را ثبت کند.

---

# 131. Discovery and Maintenance

Maintenance مسئول:

    Index Cleanup
    Duplicate Cleanup
    Metadata Repair
    Broken Reference Repair
    Reindexing

است.

---

# 132. Discovery and Versioning

Index باید Version Context را بشناسد.

Example:

    contextId:
        database

    version:
        2.1.0

---

# 133. Discovery and Lifecycle

Lifecycle تعیین می‌کند Context:

    Discoverable
    Deprecated
    Retired

باشد یا نه.

---

# 134. Discovery and Governance

Governance تعیین می‌کند:

    Who Can Register
    Who Can Modify
    Who Can Approve
    Which Sources Are Trusted
    Which Contexts Are Discoverable

---

# 135. Discovery API

ممکن است API زیر وجود داشته باشد:

    GET /contexts/search?q=database

یا:

    POST /contexts/discover

Example:

    {
      "query": "How should database transactions work?",
      "scope": "backend",
      "environment": "production",
      "limit": 20
    }

---

# 136. Discovery Response

Example:

    {
      "query": "How should database transactions work?",
      "candidates": [
        {
          "contextId": "database.transactions",
          "version": "2.1.0",
          "score": 0.94,
          "reason": [
            "keyword-match",
            "semantic-match",
            "scope-match"
          ]
        }
      ],
      "partial": false
    }

---

# 137. Discovery Error Response

Example:

    {
      "error": {
        "code": "DISCOVERY_TIMEOUT",
        "message": "Discovery exceeded configured time limit."
      }
    }

---

# 138. Discovery Contract

Discovery باید Contract مشخصی بین:

    Request
    Search
    Filtering
    Candidate
    Selector

ایجاد کند.

---

# 139. Candidate Contract

هر Candidate باید حداقل داشته باشد:

    contextId
    version
    discoveryScore
    source
    evidence

---

# 140. Discovery Determinism

برای شرایط یکسان:

    Same Query
    Same Index
    Same Metadata
    Same Policy
    Same Version

باید نتیجه قابل پیش‌بینی باشد.

---

# 141. Discovery Explainability

هر Candidate مهم باید بتواند توضیح دهد:

    Why Found?
    From Which Source?
    Which Signal Matched?
    Which Filter Applied?
    Which Version Was Found?

---

# 142. Discovery Audit Record

Example:

    {
      "requestId": "req-123",
      "query": "database transactions",
      "indexVersion": "12",
      "candidateCount": 8,
      "durationMs": 42,
      "methods": [
        "metadata",
        "lexical",
        "semantic"
      ]
    }

---

# 143. Discovery Architecture

Recommended architecture:

    ┌──────────────────────┐
    │      Request         │
    └──────────┬───────────┘
               ↓
    ┌──────────────────────┐
    │ Query Normalization   │
    └──────────┬───────────┘
               ↓
    ┌──────────────────────┐
    │ Signal Extraction     │
    └──────────┬───────────┘
               ↓
        ┌──────┴───────┐
        ↓              ↓
    Metadata        Lexical
        │              │
        └──────┬───────┘
               ↓
          Semantic Search
               ↓
          Graph Expansion
               ↓
          Merge Results
               ↓
         Deduplication
               ↓
       Security Filtering
               ↓
       Candidate Ranking
               ↓
          Candidate Set
               ↓
            Selector

---

# 144. Discovery Data Flow

```text
User Request
      ↓
Query Analyzer
      ↓
Search Strategy
      ↓
Multiple Discovery Sources
      ↓
Candidate Collection
      ↓
Deduplication
      ↓
Filtering
      ↓
Scoring
      ↓
Evidence Generation
      ↓
Selector

[ ] Candidate has valid ID
[ ] Candidate is accessible
[ ] Candidate version is compatible
[ ] Candidate status is valid
[ ] Candidate source is trusted
[ ] Candidate is not blocked
[ ] Candidate is not expired
[ ] Candidate has evidence
[ ] Candidate is not duplicated

No Results
Too Many Results
Stale Index
Broken Metadata
Unauthorized Context
Version Conflict
Search Timeout
Vector Store Failure
Graph Failure
Index Corruption
148. Discovery Security Rules

Discovery must:

Never expose unauthorized Contexts.
Never expose restricted metadata without permission.
Never bypass Context access policies.
Never treat external Context as trusted automatically.
Respect Tenant boundaries.
Respect Project boundaries.
Respect Environment boundaries.
Reject blocked Contexts.
Validate Source trust.
Audit sensitive Discovery operations.
149. Discovery Performance Rules

Discovery should:

Use indexes.
Avoid loading full Context content.
Prefer metadata filtering before expensive retrieval.
Use caching where safe.
Limit graph traversal.
Limit result size.
Limit query size.
Support parallel search.
Support early termination.
Support graceful degradation.
150. Discovery Architecture Rules

The Discovery system must:

Separate Discovery from Selection.
Search Context metadata.
Support lexical search.
Support semantic search.
Support hybrid search.
Support Context Graph traversal.
Support dependency discovery.
Support relationship discovery.
Support namespace filtering.
Support scope filtering.
Support environment filtering.
Support version filtering.
Support lifecycle filtering.
Support security filtering.
Support trust filtering.
Support freshness filtering.
Support Candidate deduplication.
Support Candidate evidence.
Support explainable Discovery.
Support configurable thresholds.
Support Candidate limits.
Support Search budgets.
Support timeouts.
Support partial results.
Support fallback strategies.
Support Discovery caching.
Support cache invalidation.
Support incremental indexing.
Support full reindexing.
Support Context registration.
Support Context removal.
Support Context updates.
Support Discovery metrics.
Support Discovery tracing.
Support Discovery auditing.
Support Discovery evaluation.
Support Recall measurement.
Support Precision measurement.
Support Regression Testing.
Support Multi-Tenant isolation.
Support Project isolation.
Support Environment isolation.
Support Query rate limiting.
Support Query size limits.
Support Graph depth limits.
Support Result size limits.
Support graceful degradation.
Support deterministic behavior.
Support reproducible Discovery.
Integrate with Sources.
Integrate with Metadata.
Integrate with Structure.
Integrate with Selector.
Integrate with Priority.
Integrate with Loader.
Integrate with Composition.
Integrate with Resolution.
Integrate with Validation.
Integrate with Security.
Integrate with Evaluation.
Integrate with Observability.
Integrate with Lifecycle.
Integrate with Maintenance.
Integrate with Versioning.
Integrate with Governance.
151. Golden Rules
Discovery finds candidates; Selection makes the final choice.
Discovery must not load unnecessary full Context content.
Every Candidate must have a stable Context ID.
Discovery must respect Security boundaries.
Discovery must respect Scope.
Discovery must respect Version compatibility.
Discovery must respect Lifecycle status.
Discovery must respect Trust.
Discovery must respect Freshness.
Discovery must support both exact and semantic retrieval.
Metadata should be the first-class Discovery source.
Semantic Search should complement, not blindly replace, metadata search.
Graph traversal must have bounded depth.
Candidate generation must have bounded size.
Discovery must avoid duplicate Candidates.
Every important Candidate should have evidence.
Discovery should be explainable.
Discovery should be measurable.
Discovery should be observable.
Discovery should be auditable where required.
Discovery must support failure handling.
Discovery must support graceful degradation.
Discovery should use caching when safe.
Cache invalidation must follow Context changes.
Context registration must update Discovery indexes.
Context removal must remove stale index entries.
Context updates must invalidate stale Discovery state.
Discovery must not treat Latest Version as automatically Best Version.
Discovery must not confuse Relevance with Priority.
Discovery must not confuse Priority with Authority.
Discovery must not expose unauthorized metadata.
Discovery must preserve Tenant isolation.
Discovery must preserve Project isolation.
Discovery must preserve Environment isolation.
Discovery should optimize for high Recall before final Selection.
Selector should handle final Candidate reduction.
Discovery configuration must be versioned when reproducibility matters.
Discovery results should be reproducible under identical conditions.
Discovery quality must be continuously evaluated.
Discovery failures must never silently produce unsafe Context.
Discovery must remain independent from Context Loading.
Discovery must remain independent from Context Building.
Discovery must remain independent from final Resolution.
Discovery should minimize search cost.
Discovery should maximize useful Context coverage.
Discovery should return the smallest useful Candidate Set.
Discovery must preserve Context identity across structural changes.
Discovery must support Context lifecycle changes.
Discovery must support Context version changes.
Discovery must remain deterministic, observable, secure, and explainable.