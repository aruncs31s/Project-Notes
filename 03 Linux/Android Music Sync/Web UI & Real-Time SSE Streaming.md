---
id: Web_UI_and_Real_Time_SSE_Streaming
aliases:
  - Web UI & Real-Time SSE Streaming
  - SSE Duplicate Streaming
  - Duplicates Dashboard UI
tags:
  - projects
  - linux
  - web
  - flask
  - sse
  - javascript
  - audio
  - ui
dg-publish: true
---

# 🌐 Web UI & Real-Time SSE Streaming

## Overview

Because acoustic fingerprinting on newly added tracks requires CPU-intensive waveform decoding, scanning large libraries synchronously through a standard HTTP `GET` request can cause client browser timeouts or appear frozen.

To provide an immediate, responsive user experience, **Android Music Sync** uses **Server-Sent Events (SSE)** via `GET /api/duplicates/stream`. This streams live, per-song scanning logs and timing metrics to an interactive console in the Web Dashboard, followed by the complete clustered payload upon completion.

```
┌─────────────────────────┐                            ┌─────────────────────────┐
│     Web Dashboard       │                            │     Flask Backend       │
│  (ui/static/dashboard.js)│                            │     (ui/server.py)      │
└────────────┬────────────┘                            └────────────┬────────────┘
             │                                                      │
             │ 1. GET /api/duplicates/stream?fingerprint=true       │
             ├─────────────────────────────────────────────────────>│
             │                                                      │ ──┐ Spawn Thread
             │                                                      │   │ 'dup-sse-analysis'
             │                                                      │ <─┘
             │ 2. SSE Event: {"type":"log", "msg":"[START]..."}     │
             │< - - - - - - - - - - - - - - - - - - - - - - - - - - ┤
             │ 3. SSE Event: {"type":"log", "msg":"[CACHE]..."}     │
             │< - - - - - - - - - - - - - - - - - - - - - - - - - - ┤
             │ 4. SSE Event: {"type":"log", "msg":"[FP] (0.3s)..."} │
             │< - - - - - - - - - - - - - - - - - - - - - - - - - - ┤
             │ 5. SSE Event: {"type":"done", "result": {...}}       │
             │< - - - - - - - - - - - - - - - - - - - - - - - - - - ┤
             │ 6. Stream Closed (Sentinel None reached)             │
             │                                                      │
             ▼                                                      ▼
   Render Duplicate Cards                                  Write to Redis Cache
   & Waveform Badges                                       ('duplicates:fp')
```

---

## The SSE Streaming Architecture (`ui/server.py`)

The streaming endpoint is implemented in [`ui/server.py`](file:///Users/aruncs/Desktop/Projects/Android_Music_Sync/ui/server.py) under `/api/duplicates/stream`.

### Background Worker & Queue Communication

```python
@app.route("/api/duplicates/stream", methods=["GET"])
def stream_duplicates():
    use_fp = request.args.get("fingerprint", "false").lower() in ("true", "1", "yes")
    msg_queue = queue.Queue()

    def progress_cb(msg: str):
        """Forward a progress log line from the analysis thread to the SSE queue."""
        msg_queue.put(json.dumps({"type": "log", "msg": msg}))

    def run_analysis():
        try:
            progress_cb(f"[START] Initializing duplicate scan ({'acoustic audio fingerprinting' if use_fp else 'tag & filename matching'})...")
            songs = song_repo.get_all_songs(force_refresh=True, progress_cb=progress_cb)
            
            result = ui_stats.detect_duplicate_songs(
                songs,
                use_fingerprint=use_fp,
                progress_cb=progress_cb,
            )

            # Cache in Redis so subsequent requests are instantaneous
            cache_key = f"{song_repo.CACHE_KEY_DUPLICATES}:{'fp' if use_fp else 'tag'}"
            song_repo._cache_set(cache_key, result)

            # Enqueue final completion payload
            msg_queue.put(json.dumps({"type": "done", "result": result}))
        except Exception as exc:
            msg_queue.put(json.dumps({"type": "error", "msg": str(exc)}))
        finally:
            msg_queue.put(None)  # Sentinel: instructs SSE generator to terminate

    thread = threading.Thread(target=run_analysis, daemon=True, name="dup-sse-analysis")
    thread.start()
```

### Generator & Keepalive Protection
HTTP proxies and browser connections will drop idle TCP connections if no bytes are sent for 15-30 seconds. The generator includes a **keepalive mechanism**:

```python
    def generate():
        yield ": SSE stream open\n\n"
        last_keepalive = time.time()

        while True:
            try:
                item = msg_queue.get(timeout=0.5)
            except queue.Empty:
                now = time.time()
                if now - last_keepalive >= 10.0:
                    last_keepalive = now
                    yield ": keepalive\n\n"
                continue

            if item is None:
                break  # Finished

            yield f"data: {item}\n\n"

    return Response(
        generate(),
        mimetype="text/event-stream",
        headers={
            "Cache-Control": "no-cache",
            "X-Accel-Buffering": "no",  # Disables Nginx buffering
            "Connection": "keep-alive"
        }
    )
```

---

## SSE Event Protocol & Log Codes

The frontend terminal receives messages prefixed with structured bracket codes:

| Log Code | Meaning | Example Payload |
| :--- | :--- | :--- |
| `[START]` | Initialization of a scanning phase | `[START] Acoustic fingerprint scan — 3420 songs` |
| `[CACHE]` | Cache hit: Fingerprint retrieved from SQLite | `[CACHE] (124/3420) Hotel California.flac` |
| `[FP]` | Cache miss: Computed via `fpcalc` with elapsed time | `[FP]    (125/3420) Stairway to Heaven.mp3 (0.3s)` |
| `[SKIP]` | File missing from disk or stat error | `[SKIP]  (126/3420) Track.m4a — file not found` |
| `[FAIL]` | `fpcalc` returned non-zero code or null | `[FAIL]  (127/3420) Corrupt.mp3 — fpcalc returned no result` |
| `[MATCH]` | Duplicate cluster detected | `[MATCH] Duplicate: 'Pink Floyd - Time' (2 copies)` |
| `[WARN]` | Missing utility or fallback triggered | `[WARN]  fpcalc not found — falling back to tag & filename matching` |
| `[DONE]` | All phases completed with summary totals | `[DONE]  12 cluster(s) found — 15 duplicate file(s)` |

---

## Frontend Integration (`dashboard.html` & `dashboard.js`)

### 1. Acoustic Fingerprint Toggle
Located in the Duplicates header toolbar:

```html
<label class="toggle-switch-label" title="Analyze audio waveforms with Chromaprint fpcalc...">
  <input type="checkbox" id="toggle-use-fingerprints" onchange="onToggleFingerprintMatching(this.checked)">
  <span>Acoustic Waveform Matching</span>
</label>
```

- **Persistence**: User selection is saved in browser `localStorage`:
  ```javascript
  let useAudioFingerprinting = localStorage.getItem('antigravity_use_audio_fingerprint') === 'true';
  ```

### 2. Missing `fpcalc` Warning Banner
If the server detects `fpcalc` is missing, it sets `warning` in the response payload. The frontend displays an alert banner explaining that the system fell back to metadata matching:

```javascript
if (result.warning) {
  warnText.textContent = result.warning;
  warnEl.style.display = 'flex';
} else {
  warnEl.style.display = 'none';
}
```

### 3. Cluster Badges & Waveform Visuals
When rendering duplicate cluster cards, the frontend inspects `match_type`:

```javascript
const matchBadge = c.match_type === 'audio_fingerprint'
  ? `<span class="badge badge-yellow" title="Acoustic waveform match via Chromaprint fpcalc">
       ${SVG_WAVEFORM} Waveform Fingerprint
     </span>`
  : (c.match_type === 'filename'
      ? '<span class="badge badge-yellow">Filename</span>'
      : '<span class="badge badge-yellow">Tag Match</span>');
```

Clusters grouped via acoustic fingerprinting display a distinctive **Waveform Fingerprint badge** with an animated SVG audio wave icon, allowing users to immediately recognize tracks identified via audio content rather than filenames or tags.

### 4. Interactive Duplicate Resolution
Each cluster card provides:
- **Auto-select (Keep Best)**: Automatically selects redundant copies while preserving the highest bitrate / quality file.
- **Direct Playback**: Stream any copy in the in-browser HTML5 player to audition sound quality before deletion.
- **Selective Trash**: Safely moves marked files to `tmp/deleted` and logs actions in SQLite.

---

## Alternative REST API (`GET /api/duplicates`)

For non-streaming programmatic access or automated scripts:

```bash
curl -s "http://localhost:5000/api/duplicates?fingerprint=true&refresh=false"
```

- **Query Parameters**:
  - `fingerprint=true`: Requests acoustic fingerprint clustering.
  - `refresh=true`: Forces a fresh filesystem and database scan, bypassing Redis cluster caching.

---

## Next Steps & Connected Modules
- How the multi-phase cascade operates: **[[Multi-Phase Duplicate Detection Engine]]**
- Database schema and cache invalidation: **[[Fingerprint Cache & Database Schema]]**
- Low-level `fpcalc` integration: **[[Chromaprint & fpcalc Integration]]**
- Return to hub note: **[[Audio Fingerprinting]]**
