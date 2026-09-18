---
id: Chromaprint_and_fpcalc_Integration
aliases:
  - Chromaprint & fpcalc Integration
  - Chromaprint
  - fpcalc Integration
tags:
  - projects
  - linux
  - audio
  - fingerprinting
  - chromaprint
  - fpcalc
dg-publish: true
---

# 🧬 Chromaprint & fpcalc Integration

## Overview

Acoustic fingerprinting in **Android Music Sync** relies on **Chromaprint**, the open-source audio fingerprinting library developed by the AcoustID project. The system interfaces with Chromaprint via its standalone command-line executable: `fpcalc`.

Unlike cryptographic hashes (such as MD5 or SHA-256) where flipping a single bit or editing an ID3 metadata tag results in a completely different checksum, an **acoustic fingerprint** captures the perceptual audio characteristics of the sound waveform itself.

```
┌─────────────────┐       ┌──────────────────────┐       ┌────────────────────────┐
│  Audio File     │       │  Perceptual Analysis │       │  Acoustic Fingerprint  │
│  (MP3/FLAC/M4A) │ ────> │  • Decoding (FFmpeg) │ ────> │  • Base64-like String  │
│  Waveform PCM   │       │  • FFT & Chromagram  │       │  • Exact Audio Length  │
└─────────────────┘       └──────────────────────┘       └────────────────────────┘
```

---

## Why Perceptual Fingerprinting?

| Comparison Point | Cryptographic Checksum (MD5/SHA256) | Metadata Tag Matching | Acoustic Fingerprint (Chromaprint / fpcalc) |
| :--- | :--- | :--- | :--- |
| **Transcoded Files** (e.g. FLAC $\rightarrow$ MP3) | ❌ Completely different hash | ⚠️ Depends on tag accuracy | ✅ **Identical fingerprint match** |
| **Bitrate Variations** (320kbps vs 128kbps) | ❌ Different hash | ⚠️ Depends on tag accuracy | ✅ **Identical fingerprint match** |
| **Missing / Wrong ID3 Tags** | ❌ Unaffected (still different) | ❌ Fails (grouped as Unknown) | ✅ **Identical fingerprint match** |
| **Different Filenames** | ❌ File-agnostic hash | ⚠️ Fails if filename matching | ✅ **Identical fingerprint match** |
| **Audio Silence / Truncation** | ❌ Different | ⚠️ Independent | ⚠️ Matches if first 120s identical |

---

## The `fpcalc` Command-Line Interface

Chromaprint distributes `fpcalc`, a fast C++ utility statically linked or dynamically bound against FFmpeg decoders.

### Execution Syntax in Android Music Sync
The application invokes `fpcalc` using:

```bash
fpcalc -json -length 120 "/path/to/audio/file.mp3"
```

### CLI Flag Explanations
1. **`-json`**: Forces `fpcalc` to emit structured JSON to `stdout` instead of raw key-value text pairs.
2. **`-length 120`**: Restricts analysis to the first **120 seconds** (2 minutes) of audio:
   - *Performance trade-off*: Significantly speeds up analysis for long tracks, podcasts, or live recordings while providing more than enough acoustic entropy to prevent false positives.
   - *Shorter files*: If a song is shorter than 120 seconds, `fpcalc` analyzes the entire duration without error.
3. **Output Format**:
   ```json
   {
     "duration": 214.32,
     "fingerprint": "AQAAUImUREmWJEqSHM2THc2j48mD8_iVo0-O5jl64kcTpsWVo3mOZjmqJ0f7I8-h78GVo3mOZjmqJ0cf"
   }
   ```

---

## System Requirements & Installation

`fpcalc` is an external dependency that must be accessible on the system `$PATH`.

### Linux (Debian / Ubuntu / Pop!_OS)
```bash
sudo apt update
sudo apt install libchromaprint-tools
```

### Linux (Arch / Manjaro)
```bash
sudo pacman -S chromaprint
```

### Linux (Fedora / RHEL)
```bash
sudo dnf install chromaprint-tools
```

### macOS (Homebrew)
```bash
brew install chromaprint
```

### Building from Source (with FFmpeg)
If the package is not available in distro repositories:
```bash
git clone https://github.com/acoustid/chromaprint.git
cd chromaprint
cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_TOOLS=ON .
make -j$(nproc)
sudo make install
```

---

## Python Implementation: `audio_fingerprint.py`

The core Python wrapper module is located at [`audio_fingerprint.py`](file:///Users/aruncs/Desktop/Projects/Android_Music_Sync/audio_fingerprint.py).

### 1. Executable Availability Check
Before running scans, the system verifies `fpcalc` using `shutil.which`:

```python
def is_fpcalc_available() -> bool:
    """Check if the 'fpcalc' (Chromaprint) executable is available on the system PATH."""
    return shutil.which("fpcalc") is not None
```

If `False`, the duplicate scanner logs a warning and falls back gracefully to metadata and filename matching (see **[[Multi-Phase Duplicate Detection Engine]]**).

### 2. Fingerprint Generation Routine

```python
def generate_audio_fingerprint(
    filepath: str, length: int = 120, timeout: int = 20
) -> Optional[Dict[str, Any]]:
    """
    Generate an acoustic fingerprint for an audio file using fpcalc.

    Args:
        filepath: Absolute or relative path to the audio file.
        length: Maximum length (in seconds) of the audio to analyze (default 120s).
        timeout: Maximum execution timeout in seconds (default 20s).

    Returns:
        Dict with 'duration' (float) and 'fingerprint' (str), or None on failure.
    """
    if not is_fpcalc_available():
        logger.warning("[AudioFingerprint] 'fpcalc' executable not found on system PATH.")
        return None

    if not os.path.isfile(filepath):
        logger.debug(f"[AudioFingerprint] File not found: {filepath}")
        return None

    try:
        cmd = ["fpcalc", "-json", "-length", str(length), filepath]
        res = subprocess.run(
            cmd,
            stdout=subprocess.PIPE,
            stderr=subprocess.PIPE,
            text=True,
            timeout=timeout,
        )
        if res.returncode == 0 and res.stdout.strip():
            data = json.loads(res.stdout)
            fingerprint = data.get("fingerprint")
            duration = data.get("duration")

            if fingerprint and duration is not None:
                return {
                    "duration": round(float(duration), 2),
                    "fingerprint": str(fingerprint).strip(),
                }
    except subprocess.TimeoutExpired:
        logger.warning(f"[AudioFingerprint] fpcalc timed out after {timeout}s for {filepath}")
    except Exception as err:
        logger.error(f"[AudioFingerprint] Error generating fingerprint for {filepath}: {err}")

    return None
```

### Key Implementation Safeguards
- **Subprocess Timeout**: Enforces a strict 20-second timeout (`timeout=20`) to prevent hanging on corrupt or indefinitely buffering audio containers.
- **Null Safety**: Validates that both `fingerprint` and `duration` are present and non-empty.
- **Duration Precision**: Rounds `duration` to 2 decimal places to ensure consistent float comparisons.
- **Error Silencing**: Catches `OSError` and JSON decode errors, returning `None` so scanning continues without crashing.

---

## Next Steps & Connected Modules
- How fingerprints are persistently stored to avoid re-analysis: **[[Fingerprint Cache & Database Schema]]**
- How fingerprint groupings are combined with tag and filename passes: **[[Multi-Phase Duplicate Detection Engine]]**
- Return to hub note: **[[Audio Fingerprinting]]**
