# Android Music Sync & Web Dashboard

Comprehensive documentation for this project is organized into Obsidian notes:

- **[Android Music Sync.md](Android%20Music%20Sync.md)** — Main project architecture, sync engine, ADB workflows, and configuration.

### 🔊 Audio Fingerprinting Documentation Suite
- **[Audio Fingerprinting.md](Audio%20Fingerprinting.md)** (`[[Audio Fingerprinting]]`) — Main Map of Content (MOC) and system overview.
- **[Chromaprint & fpcalc Integration.md](Chromaprint%20%26%20fpcalc%20Integration.md)** (`[[Chromaprint & fpcalc Integration]]`) — `fpcalc` binary integration, perceptual acoustic hashing, and `audio_fingerprint.py`.
- **[Fingerprint Cache & Database Schema.md](Fingerprint%20Cache%20%26%20Database%20Schema.md)** (`[[Fingerprint Cache & Database Schema]]`) — SQLite persistence, size/mtime cache invalidation, and in-memory preloading.
- **[Multi-Phase Duplicate Detection Engine.md](Multi-Phase%20Duplicate%20Detection%20Engine.md)** (`[[Multi-Phase Duplicate Detection Engine]]`) — 3-Phase cascading duplicate detection pipeline (`ui/stats_manager.py`).
- **[Web UI & Real-Time SSE Streaming.md](Web%20UI%20%26%20Real-Time%20SSE%20Streaming.md)** (`[[Web UI & Real-Time SSE Streaming]]`) — Server-Sent Events endpoint (`/api/duplicates/stream`), frontend logs, and UI badges.

## Quick Overview

**Android Music Sync** is a Linux-centric audio library management, synchronization, and streaming application.

- **Source Code Location**: `/Users/aruncs/Desktop/Projects/Android_Music_Sync`
- **Web Interface**: `python app.py --web` (serves on `http://localhost:5000`)
- **Key Modules**:
  - `app.py`: Main CLI entry point.
  - `audio_fingerprint.py`: Acoustic audio fingerprinting engine via Chromaprint.
  - `adb_manager.py`: ADB device discovery & Android MediaStore content query.
  - `syncer.py` & `reverse_syncer.py`: Forward & Reverse sync engines.
  - `ui/server.py`: Flask Web Dashboard REST API & SSE streaming.
  - `database/db_manager.py`: Centralized SQLite database manager (audio fingerprints, hide list, playlists).
