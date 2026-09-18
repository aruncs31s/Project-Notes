---
id: Audio_Fingerprinting
aliases:
  - Audio Fingerprinting
  - Acoustic Fingerprinting
  - Chromaprint Duplicate Detection
tags:
  - projects
  - linux
  - audio
  - fingerprinting
  - chromaprint
  - python
  - duplicate-detection
dg-publish: true
---

# 🔊 Audio Fingerprinting in Android Music Sync

## Executive Summary

In **Android Music Sync**, audio fingerprinting is an acoustic duplicate detection subsystem designed to identify duplicate tracks across local music repositories regardless of differences in **file formats** (`.mp3`, `.m4a`, `.flac`, `.opus`, `.ogg`), **bitrates**, **ID3 tag metadata**, or **filenames**.

Standard deduplication approaches rely on filename hashing or ID3 tag comparisons (`Artist - Title`). These methods fail when:
- A track is converted from FLAC to MP3 or AAC.
- Files have poor or missing metadata tags (`Track 01.mp3`, `Unknown Artist`).
- YouTube/Spotify downloaders append varying suffixes (e.g. `Song (Official Video) [Lyrics].mp3` vs `01. Song.flac`).

Android Music Sync solves this by integrating **Chromaprint's `fpcalc`** binary with a **persistent SQLite caching engine**, a **multi-phase deduplication cascade**, and **real-time Server-Sent Events (SSE)** in the Web Dashboard.

---

## High-Level Architecture

```mermaid
flowchart TD
    subgraph UI_Layer ["Web Interface & Client"]
        Toggle["Fingerprint Toggle (localStorage)"]
        SSE_Client["Dashboard SSE Client"]
        Dup_Cards["Duplicate Cluster Cards (Waveform Badge)"]
    end

    subgraph Backend_Server ["Flask API Server (ui/server.py)"]
        API_Dup["GET /api/duplicates"]
        API_Stream["GET /api/duplicates/stream"]
        Worker["Thread: dup-sse-analysis"]
        Msg_Queue["Queue (Event Stream & Keepalive)"]
    end

    subgraph Core_Engine ["Deduplication Pipeline (ui/stats_manager.py)"]
        Detect["detect_duplicate_songs()"]
        Phase1["Phase 1: Acoustic Fingerprint Scan"]
        Phase2["Phase 2: Tag Matching (Artist - Title)"]
        Phase3["Phase 3: Normalized Filename Matching"]
    end

    subgraph Fingerprint_Module ["Acoustic Engine (audio_fingerprint.py)"]
        Avail["is_fpcalc_available()"]
        Gen["generate_audio_fingerprint()"]
        Fpcalc["Chromaprint CLI (fpcalc -json -length 120)"]
    end

    subgraph Storage_Layer ["Persistence & Caching"]
        Mem_Map["In-Memory Fingerprint Map"]
        SQLite_DB[("central_db.db \n audio_fingerprints Table")]
        Redis_Cache[("Redis Cache \n duplicates:fp")]
    end

    Toggle --> API_Stream
    API_Stream --> Worker
    Worker --> Detect
    Detect --> Phase1
    Phase1 --> Mem_Map
    Mem_Map -.->|Cache Miss| Gen
    Gen --> Fpcalc
    Gen -->|Store New FP| SQLite_DB
    Phase1 -->|Remaining Unmatched| Phase2
    Phase2 -->|Remaining Unmatched| Phase3
    Worker --> Msg_Queue
    Msg_Queue --> SSE_Client
    SSE_Client --> Dup_Cards
```

---

## Obsidian Note Index (MOC)

The audio fingerprinting documentation is split into specialized modular notes. Explore each topic below:

| Note | Focus Area | Key Concepts |
| :--- | :--- | :--- |
| **[[Chromaprint & fpcalc Integration]]** | Low-level acoustic hashing | Perceptual audio hashing, `fpcalc` binary, CLI parameters, `audio_fingerprint.py` module |
| **[[Fingerprint Cache & Database Schema]]** | Persistence & Optimization | SQLite `audio_fingerprints` table, mtime/size validation, in-memory map preloading |
| **[[Multi-Phase Duplicate Detection Engine]]** | Algorithmic pipeline | 3-Phase cascade (Waveform $\rightarrow$ ID3 Tags $\rightarrow$ Filename), cluster naming heuristics, graceful degradation |
| **[[Web UI & Real-Time SSE Streaming]]** | Frontend & API integration | Server-Sent Events (`/api/duplicates/stream`), live progress logging, cluster cards, auto-selection |

---

## Technical Specifications Summary

| Feature | Implementation Detail |
| :--- | :--- |
| **Underlying Technology** | [Chromaprint](https://acoustid.org/chromaprint) (`fpcalc`) by AcoustID |
| **Python Wrapper** | [`audio_fingerprint.py`](file:///Users/aruncs/Desktop/Projects/Android_Music_Sync/audio_fingerprint.py) |
| **Analysis Length** | First 120 seconds of audio (`-length 120`) |
| **Process Timeout** | 20 seconds per track execution limit |
| **Database Table** | `audio_fingerprints` in `database/db.db` |
| **Cache Invalidation** | File size (`st_size`) and modification time (`st_mtime`) verification |
| **Clustering Complexity** | $O(N)$ with SQLite/in-memory hash grouping |
| **Web API Endpoints** | `GET /api/duplicates?fingerprint=true`, `GET /api/duplicates/stream?fingerprint=true` |
| **UI Integration** | HTML5 Dashboard toggle, live SSE progress console, acoustic waveform badge |

---

## Quick Testing & Verification

To verify that audio fingerprinting and `fpcalc` are functional on your system:

```bash
# 1. Check fpcalc availability via CLI
which fpcalc

# 2. Test fingerprint generation manually on an audio file
fpcalc -json -length 120 "/path/to/song.mp3"

# 3. Run the automated fingerprint test suite
pytest tests/test_audio_fingerprint.py -v
```

---

## Related Project Notes
- Main Project Documentation: **[[Android Music Sync]]**
- Repository README: **[README.md](README.md)**
