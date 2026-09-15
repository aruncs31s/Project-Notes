---
id: Android_Music_Sync
aliases:
  - Android Music Sync
  - ADB Music Sync & Web Dashboard
tags:
  - projects
  - linux
  - android
  - adb
  - python
  - flask
  - music
dg-publish: true
---

# 🎵 Android Music Sync & Web Dashboard

## Overview

**Android Music Sync** is a Linux-centric, high-performance audio library management, synchronization, and streaming platform. It bridges the gap between desktop Linux music libraries (e.g. `/home/aruncs/Music`) and Android mobile devices (via Android Debug Bridge / ADB USB & Wi-Fi) as well as HTTP Over-IP peer devices.

The system features:
- **Bi-Directional Synchronization**: Forward sync (`Linux -> Android`) and Reverse sync (`Android -> Linux`) with intelligent track diffing.
- **On-Demand ADB Audio Streaming**: Pulls target tracks directly from connected Android devices into local temporary storage (`/tmp/adb_streamer/`) for immediate desktop playback without full media transfers.
- **Web Dashboard & REST API**: Full-featured Flask web interface with dynamic pagination, live search, device breakdown cards, interactive audio player, and duplicate file management.
- **Duplicate Cluster Detection**: Automatic discovery of identical tracks using metadata matching (`Artist - Title`) and normalized filename heuristics.
- **Persistent Hide List & Sync History**: SQLite-backed databases (`sync_hide_list.db` & `ui/db.db`) for hiding unwanted tracks across runs and logging transfer history.
- **Terminal User Interfaces (TUI)**: Ranger-style dual-pane terminal interface (powered by `npyscreen` and `fzf`) for command-line power users.
- **Downloader & Caching Engine**: Spotify/metadata downloader integration and Redis caching for ultra-fast MediaStore queries.

---

## Key Objectives

1. **Eliminate MTP Instability**: Replace fragile MTP file transfer protocols with fast, reliable ADB `content query` and `adb push/pull` operations.
2. **Seamless Desktop Playback**: Enable instant auditioning and playback of Android device audio libraries over desktop speakers.
3. **Multi-Source Library Aggregation**: Seamlessly track and manage songs across local drives, ADB devices, and networked HTTP peers.
4. **Clean Library Maintenance**: Identify duplicate audio tracks and manage hidden file exclusions persistent across system restarts.

---

## System Architecture

```
                               ┌────────────────────────────────────────┐
                               │           Flask Web Dashboard          │
                               │      (http://localhost:5000)           │
                               └───────────────────┬────────────────────┘
                                                   │ HTTP / REST API
                                                   ▼
┌──────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                       App Entry Point (app.py)                                   │
└────────┬───────────────────────┬──────────────────────────┬────────────────────────────┬─────────┘
         │                       │                          │                            │
         ▼                       ▼                          ▼                            ▼
┌─────────────────┐   ┌────────────────────┐    ┌──────────────────────┐     ┌───────────────────────┐
│   ADB Manager   │   │ Local Music Scanner│    │ Over-IP Peer Network │     │  Terminal TUIs Engine │
│ (adb_manager.py)│   │(song_scanner.py)   │    │  (over_ip/server.py) │     │ (ranger_sync_tui.py)  │
└────────┬────────┘   └──────────┬─────────┘    └───────────┬──────────┘     └───────────┬───────────┘
         │                       │                          │                            │
         ▼                       ▼                          ▼                            ▼
┌──────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                   SQLite & Redis Storage Layer                                   │
│            • sync_hide_list.db (Hide List & Synced Track History)                                │
│            • ui/db.db (Web Dashboard Devices & Peers DB)                                        │
│            • Redis Cache (localhost:8998 - MediaStore query caching)                             │
└──────────────────────────────────────────────────────────────────────────────────────────────────┘
```

---

## Core Components & File Structure

```
Android Music Sync/
├── app.py                      # Main CLI entry point & mode orchestrator
├── adb_manager.py              # ADB device discovery & MediaStore content querying
├── adb_pusher.py               # ADB file transfer engine & MediaStore scanner triggers
├── syncer.py                   # Forward sync workflow (Local -> ADB)
├── reverse_syncer.py           # Reverse sync workflow (ADB -> Local)
├── hide_list_db.py             # SQLite interface for persistent hide-list database
├── audio_metadata.py           # ID3/FLAC audio metadata extraction using Mutagen
├── song_parser.py              # Parser for ADB shell content query output
├── fuzzy_matcher.py            # Fuzzy string matching and ranking algorithms
├── config_manager.py           # Application settings loader (config.json)
├── redis_cache.py              # Redis caching layer for ADB query optimization
├── main_menu_tui.py            # Terminal main navigation menu (npyscreen)
├── fzf_tui.py                  # Interactive fzf fuzzy finder terminal interface
├── ranger_sync_tui.py          # Ranger-style dual-pane sync TUI
├── ranger_reverse_sync_tui.py  # Ranger-style dual-pane reverse sync TUI
├── ui/                         # Web Interface Package
│   ├── server.py               # Flask REST API backend server
│   ├── stats_manager.py        # Dashboard stats, device scanner, & duplicate detector
│   ├── db_manager.py           # SQLite manager for web dashboard state (ui/db.db)
│   ├── templates/
│   │   └── dashboard.html      # Single-page Web Dashboard interface
│   └── static/
│       ├── dashboard.css       # Custom dark-theme styling
│       └── dashboard.js        # Dynamic frontend interactivity, search, & audio player
├── over_ip/                    # HTTP Network Peer Synchronization Package
│   ├── server.py               # HTTP REST API server for peer library sharing
│   ├── client.py               # HTTP client for scanning remote peers
│   ├── song_scanner.py         # Local filesystem recursive audio scanner
│   └── workflow.py             # Over-IP interactive sync workflow
└── downloader/                 # Music Downloader Module (SpotDL & Telegram integration)
```

---

## Component Breakdown & Technical Specifications

### 1. Web Interface & REST API (`ui/`)
- **Backend Server (`ui/server.py`)**: Built with Flask. Serves the web interface and exposes REST API endpoints:
  - `GET /api/dashboard/stats`: Returns song counts, connected device list, synced count, hidden count, and duplicate metrics.
  - `GET /api/songs`: Returns local music library sorted by modification time.
  - `GET /api/duplicates`: Computes and returns duplicate song clusters.
  - `GET /api/devices`: Aggregates Local Folders, ADB devices, and Over-IP HTTP peers.
  - `POST /api/devices/scan-adb`: Triggers live ADB device discovery scan (`adb devices -l`).
  - `POST /api/devices/scan-ip`: Probes saved Over-IP peer hosts.
  - `POST /api/devices/add-ip`: Registers and pings new peer IP address.
  - `GET /api/song/stream`: Serves local audio files for in-browser playback.
  - `POST /api/song/delete`: Safely removes an audio file from disk and cleans up database references.
  - `POST /api/hide` / `POST /api/unhide`: Manages hidden file exclusions.
- **Frontend Dashboard (`ui/static/dashboard.js` & `ui/templates/dashboard.html`)**:
  - Tabbed interface: **Overview**, **Available Devices**, **Local Music Library**, **Duplicates**, **Synced Tracks**, **Hidden Files**.
  - Client-side pagination and real-time fuzzy filter searching.
  - Integrated bottom audio player with HTML5 `<audio>` control.

### 2. ADB Integration (`adb_manager.py` & `adb_pusher.py`)
- **Device Discovery**: Uses `adb devices -l` to identify serial numbers, model names, and connection states.
- **MediaStore Querying**: Executes Android content query:
  ```bash
  adb -s <serial> shell content query \
    --uri content://media/external/audio/media \
    --where "is_music=1" \
    --projection "_id:_display_name:title:artist:album:album_artist:composer:track:year:duration:mime_type:_size:_data"
  ```
- **Pushing Tracks**: Copies local audio files to `/storage/emulated/0/Music/ADB/` and triggers Android MediaStore rescan:
  ```bash
  adb -s <serial> shell am broadcast \
    -a android.intent.action.MEDIA_SCANNER_SCAN_FILE \
    -d file://<remote_filepath>
  ```

### 3. Audio Streaming & On-Demand Playback
- **ADB Audio Streaming**: When an ADB device track is selected for playback, the file is fetched on demand via `adb pull` into `/tmp/adb_streamer/` (or `/tmp/`) and served directly to the audio player.

### 4. Synchronization Engine (`syncer.py` & `reverse_syncer.py`)
- **Forward Sync (`Linux -> Android`)**: Scans local folder (`/home/aruncs/Music`), queries ADB MediaStore, compares tracks by artist/title/filename, and transfers missing tracks to device.
- **Reverse Sync (`Android -> Linux`)**: Queries ADB MediaStore, compares with local music folder, and pulls missing tracks from device to desktop.
- **Interactive Ranger TUI**: Provides a side-by-side terminal UI allowing manual file toggling, instant hide key (`h`), and bulk sync confirmation.

### 5. Duplicate Detection & Library Cleaning
- **Cluster Matching**: Scans all local audio files and groups them into duplicate clusters by:
  1. Normalized `Artist - Title` ID3 metadata comparison.
  2. Normalized filename comparison.
- **Safe Deletion**: Deletes duplicate files from filesystem while updating SQLite hide lists and clearing metadata caches.

---

## Setup & Installation

### Prerequisites
- Operating System: Linux (Ubuntu, Debian, Fedora, Arch, Manjaro)
- Python 3.8 or higher
- Android Platform Tools (`adb`)
- Redis Server (Optional, for caching song query results)

### Installation Steps

1. **Clone Repository / Navigate to Directory**:
   ```bash
   cd /home/aruncs/Devices/lenovo
   ```

2. **Set Up Virtual Environment**:
   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
   ```

3. **Install Dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

4. **Verify ADB Availability**:
   ```bash
   adb devices
   ```
   *Ensure USB Debugging is enabled on your Android device.*

---

## Usage Guide

### 1. Launch Web Dashboard Interface
```bash
python app.py --web
```
Open `http://localhost:5000` in your web browser.

### 2. Launch Interactive Terminal Menu
```bash
python app.py
```

### 3. Run Forward Sync with Dual-Pane TUI (Local -> ADB)
```bash
python app.py --sync -i
```

### 4. Run Reverse Sync with Dual-Pane TUI (ADB -> Local)
```bash
python app.py --reverse-sync -i
```

### 5. Over-IP HTTP Peer Synchronization Server
```bash
python app.py --serve-ip
```

### 6. Spotify Song / Album Downloader
```bash
python app.py -dl "https://open.spotify.com/track/..."
```

---

## Configuration (`config.json`)

```json
{
  "local_sync_folder": "/home/aruncs/Music",
  "remote_adb_folder": "/storage/emulated/0/Music/ADB",
  "download_folder": "songs/download",
  "default_device_serial": null,
  "audio_extensions": [".mp3", ".m4a", ".flac", ".wav", ".ogg", ".opus", ".aac"],
  "use_telegram": false,
  "auto_push_adb": null,
  "redis": {
    "host": "localhost",
    "port": 8998,
    "db": 0,
    "password": "greenIsBest",
    "cache_ttl": 86400
  }
}
```

---

## Future Improvements & Roadmap

- [ ] Automatic mDNS/Zeroconf discovery for Over-IP peer devices on local Wi-Fi.
- [ ] Direct WebRTC/HTTP chunked streaming from Android device without temporary local disk cache.
- [ ] Playlist (.m3u / .pls) bi-directional synchronization support.
- [ ] Desktop notifications for sync completion and ADB connection state changes.

---

## Related Documents & Links
- Workspace Path: `/home/aruncs/Devices/lenovo`
- CLI Entry Point: [app.py](file:///home/aruncs/Devices/lenovo/app.py)
- Web Server API: [server.py](file:///home/aruncs/Devices/lenovo/ui/server.py)
- Web Dashboard Script: [dashboard.js](file:///home/aruncs/Devices/lenovo/ui/static/dashboard.js)
