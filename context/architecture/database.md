# Database Architecture

## Purpose

This document defines the database architecture, entities,
relationships, ownership boundaries, and persistence principles
of the AI Video Editor.

The database stores application state and metadata.

Large binary media files must not be stored directly inside the
relational database.

---

# 1. Database Technology

The primary database is:

- PostgreSQL

The application uses:

- Sequelize
- Sequelize Migrations
- Sequelize Transactions

PostgreSQL is the source of truth for persistent application data.

---

# 2. Database Responsibilities

The database stores:

- Users
- Projects
- Media metadata
- Timeline state
- Tracks
- Clips
- Editing operations
- Subtitles
- Jobs
- Exports
- AI-related persistent state

The database does not store:

- Original video binaries
- Rendered video binaries
- Large audio files
- Large image files
- Temporary FFmpeg files

Large assets are stored in object storage.

---

# 3. Storage Architecture

```text
                    Application
                         │
             ┌───────────┴───────────┐
             ▼                       ▼
        PostgreSQL              Object Storage
             │                       │
       Metadata/State          Media/Binaries