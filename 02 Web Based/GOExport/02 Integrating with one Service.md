---
tags:
  - goexport
  - testing
Status: true
---

# Integrating With One Service

Service_Name: `EtlabBackendGo`

token generation.
```go 
import hmac, hashlib, base64, json, time, sys

def b64url(data: bytes) -> str:
    return base64.urlsafe_b64encode(data).rstrip(b"=").decode()

secret = sys.argv[1]
header = {"alg":"HS256","typ":"JWT"}
now = int(time.time())
payload = {
    "app_name":"test-cli","sub":"test-cli","jti":"test-"+str(now),
    "iat":now,"exp":now+3600
}
h = b64url(json.dumps(header,separators=(",",":")).encode())
p = b64url(json.dumps(payload,separators=(",",":")).encode())
sig = hmac.new(secret.encode(), f"{h}.{p}".encode(), hashlib.sha256).digest()
print(f"{h}.{p}.{b64url(sig)}")
```

