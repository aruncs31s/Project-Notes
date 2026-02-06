# ESP32 CodeGen Quick Reference

## API Endpoints Summary

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| POST | `/api/codegen/generate` | Build firmware, return build ID | ✅ |
| POST | `/api/codegen/build` | Build firmware, return download URL | ✅ |
| POST | `/api/codegen/build-and-download` | Build and download binary directly | ✅ |
| GET | `/api/codegen/download/:build_id` | Download built firmware | ✅ |
| POST | `/api/codegen/upload` | Build and upload via OTA | ✅ |
| GET | `/api/codegen/tools` | List available build tools | ❌ |
| DELETE | `/api/codegen/builds/:build_id` | Cleanup build artifacts | ✅ |

## Request Parameters

### Required
- `ip`: ESP32 static IP address
- `host_ip`: Backend server IP
- `host_ssid`: WiFi network name
- `host_pass`: WiFi password
- `token`: Device JWT token

### Optional
- `port`: Server port (default: 8080)
- `protocol`: "http" or "https" (default: "http")
- `device_name`: Device identifier
- `build_tool`: "platformio" or "arduino-cli" (auto-detected)
- `board_fqbn`: Arduino CLI board specification

### OTA Only
- `device_ip`: Target ESP32 IP address

## Build Tools

### PlatformIO (Recommended)
- **Installation**: `pip install platformio`
- **Pros**: Modern, fast, extensive library support
- **OTA**: Built-in ESP OTA support

### Arduino CLI
- **Installation**: Download from arduino.cc
- **Pros**: Arduino IDE compatibility
- **Requirements**: Specify `board_fqbn` (e.g., "esp32:esp32:esp32")

## Common Workflows

### 1. OTA Deployment
```bash
# 1. Get device token
POST /api/device-auth/token

# 2. Upload firmware
POST /api/codegen/upload
{
  "ip": "192.168.1.100",
  "host_ip": "192.168.1.1",
  "host_ssid": "MyWiFi",
  "host_pass": "password123",
  "token": "device-token",
  "device_ip": "192.168.1.150"
}
```

### 2. Build for Manual Download
```bash
# 1. Build firmware
POST /api/codegen/build
{
  "ip": "192.168.1.100",
  "host_ip": "192.168.1.1",
  "host_ssid": "MyWiFi",
  "host_pass": "password123",
  "token": "device-token"
}
# Returns: {"download_url": "http://localhost:8080/api/codegen/download/12345"}

# 2. Download binary
GET /api/codegen/download/12345
```

### 3. Direct Download
```bash
POST /api/codegen/build-and-download
# Returns binary file directly
```

## Configuration Files Modified

The system replaces these values in `include/config.h`:

- `BACKEND_HOST`: Server IP address
- `BACKEND_PORT`: Server port
- `TOKEN`: Device authentication token
- `WIFI_SSID`: WiFi network name
- `WIFI_PASSWORD`: WiFi password

## Error Codes

| HTTP Status | Meaning |
|-------------|---------|
| 200 | Success |
| 400 | Bad request (invalid parameters) |
| 401 | Unauthorized (missing/invalid JWT) |
| 404 | Build not found |
| 500 | Server error (build/OTA failed) |

## Build Process Flow

1. **Validate request** → Check required parameters
2. **Prepare repository** → Clone/update source code
3. **Create build directory** → Isolated workspace
4. **Replace configuration** → Inject custom values
5. **Select build tool** → PlatformIO or Arduino CLI
6. **Compile firmware** → Generate binary
7. **Store results** → Save with unique build ID
8. **Return response** → Build ID, download URL, or binary

## OTA Upload Process

1. **Build firmware** (if not already built)
2. **Locate binary** → Find compiled .bin file
3. **Execute OTA command** → Tool-specific upload
4. **Monitor progress** → Capture output/logs
5. **Verify success** → Check return codes
6. **Cleanup** → Remove temporary files

## File Structure

```
workDir/
├── repo/              # Firmware source repository
└── builds/            # Build artifacts
    ├── build-123/     # Individual build workspace
    │   ├── src/       # Modified source code
    │   ├── .pio/      # PlatformIO build files
    │   └── firmware.bin # Compiled binary
    └── build-456/     # Another build
```

## Security Notes

- ✅ JWT authentication required for builds/uploads
- ✅ Device tokens embedded in firmware
- ✅ Build artifacts cleaned up automatically
- ⚠️ WiFi credentials stored in device flash
- ⚠️ OTA performed over local network only

## Troubleshooting

### Build Fails
- Check PlatformIO/Arduino CLI installation
- Verify repository access
- Check server disk space
- Review build logs

### OTA Fails
- Verify ESP32 IP address
- Check network connectivity
- Ensure device supports OTA
- Confirm device is not busy

### Authentication Issues
- Validate JWT token
- Check token expiration
- Verify user permissions

## Performance Tips

- Use PlatformIO for faster builds
- Clean up old builds regularly
- Monitor disk usage
- Consider build caching for repeated configs

## Development Commands

```bash
# Check available tools
GET /api/codegen/tools

# Clean up specific build
DELETE /api/codegen/builds/12345

# Monitor server logs
tail -f logs/codegen.log
```

---

*For detailed API documentation, see `esp32-codegen-api.md`*
*For system architecture, see `esp32-codegen-system.md`*</content>
<parameter name="filePath">/home/aruncs/Startups/Sooraj_Sirs/skvms/docs/esp32-codegen-quickref.md