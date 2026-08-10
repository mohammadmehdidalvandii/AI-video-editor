# Architecture Overview

## Purpose

This document defines the high-level architecture of the AI Video
Editor.

It describes the major system components, their responsibilities,
communication patterns, and architectural boundaries.

The goal is to provide a clear architectural model that can be used
by developers and AI systems when making implementation decisions.

---

# Architecture Style

The system follows a combination of:

- Monorepo Architecture
- Modular Monolith
- Layered Architecture
- Domain-Based Architecture
- Asynchronous Worker Architecture
- Queue-Based Processing
- AI Agent Architecture
- Pipeline Architecture

The architecture is designed to remain simple during the MVP stage
while providing a clear path toward horizontal scaling.

---

# High-Level System

```text
                    AI VIDEO EDITOR
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
        ▼                  ▼                  ▼
    FRONTEND             API               AI LAYER
      React            Express             Agent
        │                  │                  │
        │                  ▼                  ▼
        │              Database            Context
        │              PostgreSQL          Tools
        │                  │                  │
        │                  └──────┬───────────┘
        │                         │
        │                    Job Queue
        │                    Redis/BullMQ
        │                         │
        │              ┌──────────┴──────────┐
        │              ▼                     ▼
        │           Worker 1              Worker N
        │              │                     │
        │           FFmpeg                FFmpeg
        │           FFprobe               FFprobe
        │              │                     │
        └──────────────┴──────────┬──────────┘
                                  ▼
                            Object Storage