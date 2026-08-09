# Requirements Context

## Purpose

This document defines the functional and non-functional requirements
of the AI Video Editor.

Requirements are derived from the product vision, target users,
and defined use cases.

---

# 1. Functional Requirements

## FR-001 — User Authentication

The system must allow users to:

- Register an account
- Login
- Logout
- Manage their profile
- Maintain authenticated sessions

---

## FR-002 — Project Management

Users must be able to:

- Create projects
- Rename projects
- View projects
- Delete projects
- Open projects
- Manage project metadata

A project represents an independent video editing workspace.

---

## FR-003 — Media Upload

Users must be able to upload:

- Video files
- Audio files
- Image files

The system must support large media files.

Uploaded files must be stored in object storage rather than the
application database.

---

## FR-004 — Media Management

Users must be able to:

- View uploaded media
- Preview media
- Rename media
- Delete media
- Search media
- Add media to the timeline

---

## FR-005 — Media Analysis

The system must automatically analyze uploaded media.

The analysis should extract information such as:

- Duration
- Resolution
- Frame rate
- Video codec
- Audio codec
- Bitrate
- File size
- Audio channels
- Sample rate
- Number of streams

The system should use `ffprobe` for technical media analysis.

---

## FR-006 — Timeline

The editor must provide a timeline-based editing system.

The timeline must support:

- Multiple tracks
- Video tracks
- Audio tracks
- Subtitle tracks
- Clips
- Clip positioning
- Clip duration
- Track ordering
- Timeline time position

---

## FR-007 — Video Editing

The MVP must support basic video operations:

- Cut
- Trim
- Split
- Move
- Delete
- Crop
- Resize
- Rotate
- Playback speed

Editing operations should be represented as structured data.

Example:

```json
{
  "type": "trim",
  "clipId": "clip_123",
  "start": 10,
  "end": 45
}