# Frontend Architecture

## Purpose

This document defines the architecture, responsibilities, boundaries,
and major components of the frontend application.

The frontend provides the user interface for project management,
media management, timeline-based editing, AI-assisted editing,
preview, rendering, and exporting.

The frontend must remain independent from video processing
implementation details.

---

# 1. Frontend Technology

The frontend application is based on:

- React
- TypeScript
- Vite
- Tailwind CSS
- Zustand
- TanStack Query
- React Router
- Zod

Additional libraries may be introduced when they provide clear
architectural or product value.

---

# 2. Frontend Responsibilities

The frontend is responsible for:

- User interface
- User interaction
- Client-side state
- Timeline interaction
- Project management UI
- Media management UI
- Video preview
- AI assistant interface
- Job status visualization
- Export interface

The frontend is NOT responsible for:

- Executing FFmpeg
- Executing FFprobe
- Direct database access
- Running background video processing
- Storing large media files in application state

---

# 3. Application Structure

The frontend follows a feature-oriented architecture.

```text
src/
│
├── app/
├── routes/
├── components/
│
├── features/
│   ├── auth/
│   ├── projects/
│   ├── media/
│   ├── editor/
│   ├── timeline/
│   ├── subtitles/
│   ├── ai-assistant/
│   └── exports/
│
├── services/
├── hooks/
├── stores/
├── lib/
└── types/