# ESP32 Firmware Code Generation System Documentation

## Overview

The ESP32 Firmware Code Generation System is a comprehensive backend service that automates the process of generating, building, and deploying custom firmware for ESP32 devices in the SKVMS (Solar Battery Monitoring System). The system provides REST APIs for firmware generation with custom configurations and Over-The-Air (OTA) deployment capabilities.

## System Architecture

### Core Components

```
codegen/
├── dto/                    # Data Transfer Objects
│   ├── codegen_request.go  # API request/response structures
│   └── ...
├── builder/               # Build Strategy Implementations
│   ├── strategy.go        # BuildStrategy interface
│   ├── platformio.go      # PlatformIO implementation
│   ├── arduino_cli.go     # Arduino CLI implementation
│   └── resolver.go        # Build tool resolution
├── service.go            # Main orchestration service
├── config_replacer.go    # Firmware configuration replacement
└── repo.go              # Source repository management
```

### Handler Layer

```
handler/http/
└── codegen_handler.go    # HTTP request handlers
```

## Component Details

### 1. Data Transfer Objects (DTO)

#### CodeGenRequest
Defines the parameters for firmware generation:

```go
type CodeGenRequest struct {
    IP          string // ESP32 static IP address
    HostIP      string // Backend server IP
    HOSTSSID    string // WiFi network name
    HOSTPASS    string // WiFi password
    Port        int    // Backend server port (default: 8080)
    Protocol    string // Communication protocol (default: "http")
    Token       string // Device authentication JWT token
    DeviceName  string // Optional device identifier
    BuildTool   string // "platformio" or "arduino-cli"
    BoardFQBN   string // Arduino CLI board specification
}
```

#### CodeGenResponse
Contains the result of a successful build:

```go
type CodeGenResponse struct {
    Message     string // Success message
    BuildTool   string // Tool used for building
    BinarySize  int64  // Size of compiled binary in bytes
    BuildID     string // Unique build identifier
    DownloadURL string // Direct download URL (optional)
}
```

### 2. Build Strategy Pattern

The system uses a strategy pattern to support multiple build tools:

#### BuildStrategy Interface
```go
type BuildStrategy interface {
    Name() string                    // Tool name
    IsAvailable() bool              // Check if tool is installed
    Build(ctx context.Context, projectDir string) (*BuildResult, error)
    Upload(ctx context.Context, projectDir string, deviceIP string) error
}
```

#### PlatformIO Implementation
- **Primary build tool** for ESP32 development
- Uses `platformio.ini` configuration files
- Supports ESP32, ESP8266, and other platforms
- OTA upload via `pio run --target upload --upload-port <ip>`

#### Arduino CLI Implementation
- **Alternative build system**
- Requires explicit `board_fqbn` specification
- Uses Arduino framework and libraries
- More traditional Arduino IDE workflow

### 3. Service Layer

#### CodeGen Service
The main orchestration service that manages the complete firmware generation pipeline:

```go
type Service struct {
    workDir string  // Base directory for builds and repositories
}
```

**Key Methods:**
- `Generate()` - Complete build pipeline
- `Upload()` - Build and OTA upload
- `GetBinaryPath()` - Retrieve built binaries
- `CleanupBuild()` - Remove build artifacts
- `ListAvailableTools()` - Check installed build tools

#### Build Pipeline Flow

1. **Repository Preparation**
   - Clone or update firmware source repository
   - Create isolated build directory

2. **Configuration Replacement**
   - Replace WiFi credentials in `config.h`
   - Set server endpoints and authentication tokens
   - Configure device-specific parameters

3. **Build Execution**
   - Resolve appropriate build strategy
   - Compile firmware using selected tool
   - Generate binary output

4. **Result Management**
   - Store build artifacts with unique ID
   - Provide download URLs and metadata

### 4. Configuration Replacement

The `config_replacer.go` module handles dynamic firmware configuration:

**Replaced Parameters:**
- `BACKEND_HOST` - Server IP address
- `BACKEND_PORT` - Server port number
- `TOKEN` - Device authentication token
- `WIFI_SSID` - WiFi network name
- `WIFI_PASSWORD` - WiFi password

**Replacement Logic:**
```go
func ReplaceConfig(projectDir string, req CodeGenRequest) error {
    // Read config.h template
    // Replace placeholders with actual values
    // Write modified configuration back
}
```

### 5. Repository Management

The `repo.go` module handles firmware source code management:

**Functions:**
- `CloneOrPullRepo()` - Ensure source repository is available
- `CopyRepoForBuild()` - Create isolated build copy
- `CleanupBuild()` - Remove build artifacts

**Repository Structure:**
```
workDir/
├── repo/           # Source repository
└── builds/         # Isolated build directories
    ├── build-123/  # Individual build workspace
    └── build-456/  # Another build workspace
```

## API Endpoints

### Build Endpoints

#### POST /api/codegen/generate
**Legacy endpoint** - Builds firmware and returns build ID.

#### POST /api/codegen/build
**Recommended endpoint** - Builds firmware and returns download URL.

#### POST /api/codegen/build-and-download
Builds firmware and immediately returns binary file.

#### GET /api/codegen/download/:build_id
Downloads compiled firmware binary.

### Deployment Endpoints

#### POST /api/codegen/upload
Builds firmware and uploads via OTA to ESP32 device.

**Request Body:**
```json
{
  "ip": "192.168.1.100",
  "host_ip": "192.168.1.1",
  "host_ssid": "MyWiFi",
  "host_pass": "password123",
  "token": "device-jwt-token",
  "device_ip": "192.168.1.150"
}
```

### Management Endpoints

#### GET /api/codegen/tools
Lists available build tools on the server.

#### DELETE /api/codegen/builds/:build_id
Removes build artifacts to free disk space.

## Build Process Details

### 1. Source Preparation
- Clone ESP32 firmware repository
- Create timestamped build directory
- Copy source to isolated workspace

### 2. Configuration Injection
- Parse `include/config.h` template
- Replace placeholder values with runtime configuration
- Handle multiple WiFi SSID/password definitions

### 3. Build Execution
- Detect available build tools
- Execute compilation using selected strategy
- Capture build output and errors
- Locate generated binary file

### 4. OTA Upload Process
- Use build tool's OTA capabilities
- Connect to ESP32 at specified IP
- Transfer firmware via HTTP
- Verify successful deployment

## Error Handling

### Build Failures
- **Tool not available**: PlatformIO/Arduino CLI not installed
- **Compilation errors**: Source code issues
- **Configuration errors**: Invalid parameters
- **Repository issues**: Source code inaccessible

### OTA Upload Failures
- **Network connectivity**: Device unreachable
- **Authentication issues**: Invalid device credentials
- **Firmware compatibility**: OTA not supported
- **Timeout errors**: Upload process interrupted

### Error Response Format
```json
{
  "error": "descriptive error message",
  "details": "technical error details"
}
```

## Security Considerations

### Authentication
- JWT tokens required for all sensitive operations
- Device-specific tokens for ESP32 authentication
- User authentication for API access

### Data Protection
- WiFi credentials embedded in firmware
- Authentication tokens stored in device flash
- Build artifacts contain sensitive configuration

### Network Security
- OTA updates performed over local network
- HTTPS recommended for API communication
- Secure token generation and validation

## Configuration Files

### PlatformIO Configuration
```ini
[env:esp32dev]
platform = espressif32
board = esp32dev
framework = arduino

[env:esp32-ota]
platform = espressif32
board = esp32dev
framework = arduino
upload_protocol = espota
upload_port = 192.168.1.150
```

### Arduino CLI Configuration
```json
{
  "board_manager": {
    "additional_urls": ["https://dl.espressif.com/dl/package_esp32_index.json"]
  },
  "sketchbook": {
    "path": "/path/to/sketches"
  }
}
```

## Monitoring and Logging

### Structured Logging
- Build process events logged with context
- Error conditions captured with stack traces
- Performance metrics for build duration
- OTA upload progress and status

### Log Levels
- **INFO**: Successful operations, progress updates
- **WARN**: Non-critical issues, fallback behaviors
- **ERROR**: Failed operations, system errors

## Performance Considerations

### Build Optimization
- Isolated build directories prevent conflicts
- Incremental builds not currently supported
- Parallel builds possible with multiple workers

### Resource Management
- Automatic cleanup of build artifacts
- Configurable work directory locations
- Memory-efficient configuration replacement

### Scalability
- Stateless service design
- Horizontal scaling possible
- Build queue management for high load

## Testing and Validation

### Unit Tests
- Build strategy implementations
- Configuration replacement logic
- Repository management functions

### Integration Tests
- Complete build pipeline
- OTA upload simulation
- API endpoint validation

### Firmware Validation
- Binary size verification
- Configuration correctness
- OTA compatibility checks

## Troubleshooting Guide

### Common Issues

#### Build Tool Not Found
```
Error: no build tool available
Solution: Install PlatformIO or Arduino CLI
```

#### Repository Access Failed
```
Error: failed to prepare source repository
Solution: Check repository URL and credentials
```

#### OTA Upload Timeout
```
Error: OTA upload failed
Solution: Verify device IP and network connectivity
```

#### Configuration Replacement Failed
```
Error: failed to replace config
Solution: Check config.h template format
```

## Future Enhancements

### Planned Features
- **Binary upload support**: Upload pre-compiled firmware
- **Build caching**: Reuse successful builds
- **Multi-target builds**: Support for different ESP32 variants
- **Build queuing**: Handle concurrent build requests
- **Firmware versioning**: Track firmware versions and changes
- **Rollback support**: Revert to previous firmware versions

### API Extensions
- **Build status polling**: Real-time build progress
- **Batch operations**: Build multiple configurations
- **Template management**: Custom firmware templates
- **Device management**: Track deployed firmware versions

## Development Guidelines

### Code Organization
- Clear separation of concerns
- Interface-based design for testability
- Comprehensive error handling
- Structured logging throughout

### Testing Strategy
- Unit tests for all components
- Integration tests for API endpoints
- Mock implementations for external dependencies
- Automated testing in CI/CD pipeline

### Documentation
- API documentation with examples
- Code comments for complex logic
- Troubleshooting guides for common issues
- Architecture documentation for new developers

---

*This documentation covers the complete ESP32 firmware code generation system as implemented in the SKVMS backend service. For API usage examples, see the companion API documentation.*</content>
<parameter name="filePath">/home/aruncs/Startups/Sooraj_Sirs/skvms/docs/esp32-codegen-system.md