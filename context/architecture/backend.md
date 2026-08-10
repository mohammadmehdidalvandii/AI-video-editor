# Backend Architecture

## Purpose

This document defines the architecture, responsibilities, boundaries,
and development principles of the backend application.

The backend provides APIs for authentication, project management,
media management, timeline editing, AI interactions, job management,
rendering, and exporting.

The backend is designed around a modular architecture using Node.js,
Express, Sequelize, and PostgreSQL.

---

# 1. Backend Technology

The backend is based on:

- Node.js
- TypeScript
- Express
- Sequelize
- PostgreSQL
- Zod
- Redis
- BullMQ

The backend should remain independent from the frontend implementation.

---

# 2. Backend Responsibilities

The backend is responsible for:

- Authentication
- Authorization
- User management
- Project management
- Media metadata
- Timeline persistence
- Editing operations
- AI requests
- Job creation
- Job management
- Export management
- Application business logic

The backend is NOT responsible for:

- Rendering the frontend
- Direct user interface management
- Long-running FFmpeg execution inside HTTP handlers
- Storing large media binaries inside PostgreSQL

---

# 3. Architecture Style

The backend follows:

- Modular Monolith
- Layered Architecture
- Domain-Based Architecture
- Repository Pattern
- Service Layer Pattern

The backend remains a single deployable API application while
maintaining clear internal module boundaries.

---

# 4. Backend Structure

The initial structure is:

```text
src/
│
├── config/
├── middleware/
│
├── modules/
│   ├── auth/
│   ├── users/
│   ├── projects/
│   ├── media/
│   ├── timeline/
│   ├── editing/
│   ├── subtitles/
│   ├── ai/
│   ├── jobs/
│   └── exports/
│
├── database/
│   ├── models/
│   ├── migrations/
│   └── seeders/
│
├── routes/
├── utils/
│
├── app.ts
└── server.ts