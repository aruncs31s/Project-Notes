# Android Music Sync & Web Dashboard

Comprehensive documentation for this project is available in the main note file:
- **[Android Music Sync.md](Android%20Music%20Sync.md)**

## Quick Overview

**Android Music Sync** is a Linux-centric audio library management, synchronization, and streaming application.

- **Source Code Location**: `/home/aruncs/Devices/lenovo`
- **Web Interface**: `python app.py --web` (serves on `http://localhost:5000`)
- **Key Modules**:
  - `app.py`: Main CLI entry point.
  - `adb_manager.py`: ADB device discovery & Android MediaStore content query.
  - `syncer.py` & `reverse_syncer.py`: Forward & Reverse sync engines.
  - `ui/server.py`: Flask Web Dashboard REST API.
  - `hide_list_db.py`: Persistent SQLite hide list & sync history database.
