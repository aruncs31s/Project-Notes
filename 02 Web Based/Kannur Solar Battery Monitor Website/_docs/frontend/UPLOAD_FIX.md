# Upload Error Fix - 400 Bad Request

## Problem
**Error:** `Upload failed: Request failed with status code 400`

When uploading firmware files (.bin), the server rejected the request with a 400 error.

## Root Cause

The HttpClient was configured to **always send `Content-Type: application/json`** header for all requests, including file uploads. However:

- ✅ JSON requests need: `Content-Type: application/json`
- ✅ File uploads need: `Content-Type: multipart/form-data; boundary=----WebKitFormBoundary...`

The hardcoded JSON content-type prevented FormData from working correctly.

## Solution Applied

### 1. Fixed HttpClient Configuration
**File:** [src/infrastructure/http/HttpClient.ts](src/infrastructure/http/HttpClient.ts)

**Before:**
```typescript
this.client = axios.create({
  baseURL: API_BASE_URL,
  headers: { 
    'Content-Type': 'application/json',  // ❌ Forced for all requests
    'Accept': 'application/json'
  },
  withCredentials: false,
});
```

**After:**
```typescript
this.client = axios.create({
  baseURL: API_BASE_URL,
  headers: { 
    'Accept': 'application/json'
    // ✅ Content-Type removed from defaults
    // Axios will set it automatically based on data type
  },
  withCredentials: false,
});
```

### 2. Smart Content-Type Detection
Added logic in request interceptor to set Content-Type based on data type:

```typescript
if (config.data && !(config.data instanceof FormData)) {
  config.headers['Content-Type'] = 'application/json';
}
// For FormData, axios automatically sets:
// Content-Type: multipart/form-data; boundary=----WebKitFormBoundary...
```

### 3. Better Error Messages
**File:** [src/components/FirmwareUploadModal.tsx](src/components/FirmwareUploadModal.tsx)

Enhanced error handling to show specific messages:

- **400:** Bad Request - Invalid file or parameters
- **413:** File too large (max 10MB)
- **415:** Unsupported file type (must be .bin)
- **500:** Server error
- **Network errors:** Connection issues

## How It Works Now

### For JSON Requests (Regular API calls):
```typescript
await httpClient.post('/devices', { name: 'Device 1' });
// Content-Type: application/json ✅
```

### For File Uploads (FormData):
```typescript
const formData = new FormData();
formData.append('firmware', file);
formData.append('device_id', '123');
await httpClient.post('/codegen/upload', formData);
// Content-Type: multipart/form-data; boundary=----WebKit... ✅
```

## Testing

1. **Open your application**
2. **Navigate to a Microcontroller device**
3. **Click "Upload Firmware"**
4. **Select a .bin file**
5. **Click "Upload Firmware" button**

✅ The upload should now work correctly!

## Files Modified

1. ✅ `src/infrastructure/http/HttpClient.ts` - Fixed Content-Type handling
2. ✅ `src/components/FirmwareUploadModal.tsx` - Better error messages

## Additional Improvements Made

### Better Error Handling
- Shows specific error messages for different status codes
- Displays file size validation (max 10MB)
- Validates .bin file extension
- Shows upload progress

### Validation
```typescript
// File type validation
if (!file.name.toLowerCase().endsWith('.bin')) {
  setMessage('Please select a valid firmware file (.bin)');
  return;
}

// File size validation (max 10MB)
if (file.size > 10 * 1024 * 1024) {
  setMessage('File size must be less than 10MB');
  return;
}
```

## Server Requirements

Your backend server should accept:
- **Endpoint:** `POST /api/codegen/upload`
- **Content-Type:** `multipart/form-data`
- **Form fields:**
  - `firmware` (file): The .bin firmware file
  - `device_id` (string): The device ID

Example successful response:
```json
{
  "message": "Firmware uploaded successfully"
}
```

## Common Issues & Solutions

### Still Getting 400?
Check if your backend:
1. ✅ Accepts `multipart/form-data`
2. ✅ Expects form field name `firmware` (not `file`)
3. ✅ Expects form field name `device_id`
4. ✅ Validates file extensions (.bin)
5. ✅ Has size limits configured

### Getting 413 (Payload Too Large)?
- Frontend limits to 10MB
- Check backend server limits:
  ```go
  // Example for Go
  maxFileSize := 10 * 1024 * 1024 // 10MB
  ```

### Getting CORS errors?
Ensure backend allows:
```go
// Example headers
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: Content-Type, Authorization
```

## Build Status
✅ **Build successful!**  
✅ **No TypeScript errors**  
✅ **Ready to deploy**

---

**Status: FIXED** ✅  
**Issue:** 400 Bad Request on file upload  
**Solution:** Allow axios to auto-detect Content-Type for FormData
