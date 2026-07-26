# ESP32 Firmware Code Generation API Documentation

This document describes the ESP32 firmware code generation and deployment API for the SKVMS (Solar Battery Monitoring System). The API allows you to generate custom ESP32 firmware with specific configurations and deploy it via Over-The-Air (OTA) updates.

## Overview

The code generation system provides a complete pipeline for:
- Generating ESP32 firmware with custom WiFi credentials, server endpoints, and authentication tokens
- Building firmware using PlatformIO or Arduino CLI
- Downloading compiled binaries
- Uploading firmware to ESP32 devices via OTA
- Managing build artifacts

## Base URL
All endpoints are prefixed with `/api/codegen`

## Authentication
All endpoints except `/tools` require JWT authentication via `Authorization: Bearer <token>` header.

---

## Build Endpoints

### 1. Generate Firmware (Legacy)
**POST** `/generate` 🔐

Generates ESP32 firmware and returns a build ID for later download.

**Request Body:**
```json
{
  "ip": "192.168.1.100",
  "host_ip": "192.168.1.1",
  "host_ssid": "MyWiFiNetwork",
  "host_pass": "wifi_password",
  "port": 8080,
  "protocol": "http",
  "token": "your-device-jwt-token",
  "device_name": "ESP32-Sensor-01",
  "build_tool": "platformio",
  "board_fqbn": "esp32:esp32:esp32"
}
```

**Response (200 OK):**
```json
{
  "message": "Firmware built successfully",
  "build_tool": "PlatformIO",
  "binary_size_bytes": 524288,
  "build_id": "12345-acde"
}
```

---

### 2. Build Firmware with Download URL
**POST** `/build` 🔐

Generates ESP32 firmware and returns a download URL for the compiled binary.

**Request Body:** Same as `/generate`

**Response (200 OK):**
```json
{
  "message": "Firmware built successfully",
  "build_tool": "PlatformIO",
  "binary_size_bytes": 524288,
  "build_id": "12345-acde",
  "download_url": "http://localhost:8080/api/codegen/download/12345-acde"
}
```

---

### 3. Build and Download Binary
**POST** `/build-and-download` 🔐

Generates firmware and immediately returns the binary file for download.

**Request Body:** Same as `/generate`

**Response:** Binary file download with headers:
```
Content-Type: application/octet-stream
Content-Disposition: attachment; filename=firmware.bin
X-Build-ID: 12345-acde
X-Build-Tool: PlatformIO
```

---

### 4. Download Built Firmware
**GET** `/download/:build_id` 🔐

Downloads a previously built firmware binary.

**Path Parameters:**
- `build_id` (string): Build ID returned from generation endpoints

**Response:** Binary file download

---

## Deployment Endpoints

### 5. Upload via OTA
**POST** `/upload` 🔐

Generates firmware and uploads it directly to an ESP32 device via Over-The-Air update.

**Request Body:**
```json
{
  "ip": "192.168.1.100",
  "host_ip": "192.168.1.1",
  "host_ssid": "MyWiFiNetwork",
  "host_pass": "wifi_password",
  "port": 8080,
  "protocol": "http",
  "token": "your-device-jwt-token",
  "device_name": "ESP32-Sensor-01",
  "build_tool": "platformio",
  "board_fqbn": "esp32:esp32:esp32",
  "device_ip": "192.168.1.150"
}
```

**Response (200 OK):**
```json
{
  "message": "Firmware uploaded successfully via OTA",
  "device_ip": "192.168.1.150"
}
```

---

## Management Endpoints

### 6. List Available Build Tools
**GET** `/tools`

Returns a list of available build tools on the server.

**Response (200 OK):**
```json
{
  "available_tools": ["PlatformIO", "Arduino CLI"]
}
```

---

### 7. Cleanup Build Artifacts
**DELETE** `/builds/:build_id` 🔐

Removes build artifacts for a specific build ID.

**Path Parameters:**
- `build_id` (string): Build ID to clean up

**Response (200 OK):**
```json
{
  "message": "build cleaned up",
  "build_id": "12345-acde"
}
```

---

## Request Parameters

### Required Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `ip` | string | Static IP address for the ESP32 device |
| `host_ip` | string | Backend server IP address |
| `host_ssid` | string | WiFi network name |
| `host_pass` | string | WiFi password |
| `token` | string | Device authentication JWT token |

### Optional Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `port` | integer | 8080 | Backend server port |
| `protocol` | string | "http" | Communication protocol |
| `device_name` | string | "" | Optional device identifier |
| `build_tool` | string | auto | Build tool: "platformio" or "arduino-cli" |
| `board_fqbn` | string | "" | Arduino CLI board FQBN |

### OTA Upload Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `device_ip` | string | IP address of target ESP32 device |

---

## Build Tools

### PlatformIO
- **Default choice** for ESP32 development
- Supports ESP32, ESP8266, and other platforms
- Uses `platformio.ini` configuration
- Command: `pio run --target upload --upload-port <device_ip>`

### Arduino CLI
- Alternative build system
- Requires specifying `board_fqbn` (e.g., "esp32:esp32:esp32")
- Uses Arduino framework
- Command: `arduino-cli compile --fqbn <board_fqbn>`

---

## Workflow Examples

### Complete OTA Deployment

1. **Get Device Token:**
```http
POST /api/device-auth/token
Authorization: Bearer <user-jwt>
```

2. **Upload Firmware:**
```http
POST /api/codegen/upload
Authorization: Bearer <user-jwt>
Content-Type: application/json

{
  "ip": "192.168.1.100",
  "host_ip": "192.168.1.1",
  "host_ssid": "MyWiFi",
  "host_pass": "password123",
  "token": "<device-token>",
  "device_ip": "192.168.1.150"
}
```

### Build and Manual Download

1. **Build Firmware:**
```http
POST /api/codegen/build
Authorization: Bearer <user-jwt>
Content-Type: application/json

{
  "ip": "192.168.1.100",
  "host_ip": "192.168.1.1",
  "host_ssid": "MyWiFi",
  "host_pass": "password123",
  "token": "<device-token>"
}
```

2. **Download Binary:**
```http
GET /api/codegen/download/12345-acde
Authorization: Bearer <user-jwt>
```

---

## Error Responses

### Common Errors

- **400 Bad Request:** Invalid request body or missing required fields
- **401 Unauthorized:** Missing or invalid JWT token
- **404 Not Found:** Build ID not found
- **500 Internal Server Error:** Build failed or OTA upload failed

### Error Response Format
```json
{
  "error": "error message",
  "details": "detailed error information"
}
```

---

## Firmware Configuration

The generated firmware includes the following configurations:

- **WiFi Credentials:** SSID and password for network connection
- **Server Endpoint:** Backend server IP and port
- **Authentication:** JWT token for device authentication
- **Device Identity:** Static IP and device name
- **Communication Protocol:** HTTP/HTTPS settings

---

## Prerequisites

### For Building
- PlatformIO or Arduino CLI installed on server
- ESP32 development environment configured
- Firmware source repository accessible

### For OTA Upload
- ESP32 device running OTA-capable firmware
- Device accessible on the same network
- Device IP address known and reachable

---

## Security Considerations

- All sensitive endpoints require JWT authentication
- Device tokens are used for ESP32 authentication
- OTA updates should be performed over secure networks
- Build artifacts are cleaned up after use
- Binary files contain authentication credentials

---

## API Usage Examples

### cURL Examples

**Build Firmware:**
```bash
curl -X POST http://localhost:8080/api/codegen/build \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "ip": "192.168.1.100",
    "host_ip": "192.168.1.1",
    "host_ssid": "MyWiFi",
    "host_pass": "password123",
    "token": "device-jwt-token"
  }'
```

**OTA Upload:**
```bash
curl -X POST http://localhost:8080/api/codegen/upload \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "ip": "192.168.1.100",
    "host_ip": "192.168.1.1",
    "host_ssid": "MyWiFi",
    "host_pass": "password123",
    "token": "device-jwt-token",
    "device_ip": "192.168.1.150"
  }'
```

**Download Binary:**
```bash
curl -X GET http://localhost:8080/api/codegen/download/12345-acde \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -o firmware.bin
```

---

## Troubleshooting

### Build Failures
- Check that PlatformIO/Arduino CLI is installed
- Verify firmware source repository is accessible
- Check server logs for detailed error messages

### OTA Upload Failures
- Ensure ESP32 is running OTA-compatible firmware
- Verify device IP address is correct and reachable
- Check network connectivity between server and device
- Confirm device is not in use by another process

### Authentication Issues
- Verify JWT token is valid and not expired
- Check token has necessary permissions
- Ensure device tokens are properly generated

---

*Last updated: February 7, 2026*</content>
<parameter name="filePath">/home/aruncs/Startups/Sooraj_Sirs/skvms/docs/esp32-codegen-api.md