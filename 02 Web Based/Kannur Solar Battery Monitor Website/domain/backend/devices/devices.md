---
tags:
  - project
  - skvms
Status:
---
```sql
SELECT  
		d.id,
		connected_to.parent_id AS parent_id,
		d.name,
		dt.name AS type,
		dd.ip_address,
		dd.mac_address,
		v.name as firmware_version,
		ds.name as current_state,
		cdevice.name as used_by
	FROM devices d
	JOIN device_states ds  ON d.current_state  = ds.id
	JOIN device_types dt 
		ON dt.id = d.device_type
	LEFT JOIN versions v
	  ON v.device_id = d.id
	 AND v.name = (
		 SELECT MAX(v2.name) as version_name
		 FROM versions v2
		 WHERE v2.device_id = d.id
	 )
	LEFT JOIN device_details dd 
		ON dd.device_id = d.id
	LEFT JOIN connected_devices cd 
		ON cd.parent_id = d.id 
	LEFT JOIN connected_devices connected_to 
		ON connected_to.child_id = d.id
	LEFT JOIN devices cdevice ON cdevice.id = connected_to.parent_id
	WHERE dt.hardware_type = ?
	ORDER BY d.created_at DESC
	LIMIT ? OFFSET ?
```



## Delete

When a DELETE request comes to `/:id`, this is the execution flow:

1. **JWTAuth Middleware** (`middleware.JWTAuth(r.jwtSecret`)
    
    - Validates the JWT token from the `Authorization` header
    - Verifies the token signature using the secret
    - If invalid/missing, returns 401 Unauthorized and stops execution
    - If valid, extracts user info and adds it to the request context
2. **CasbinDeviceAuth Middleware** (`middleware.CasbinDeviceAuth("delete")`)
    
    - Checks if the authenticated user has permission to `delete` the device
    - Uses Casbin RBAC model with the device ID from the URL parameter
    - Compares: user role + device resource + "delete" action against policy rules
    - If unauthorized, returns 403 Forbidden and stops execution
    - If authorized, proceeds to the handler
3. **DeleteDevice Handler** (`r.deviceHandler.DeleteDevice`)
    
    - Only executes if both middleware checks pass
    - Receives the `:id` parameter from the URL
    - Performs the actual deletion logic (validation, DB query, cleanup, etc.)
    - Returns the response (success or error)

**Key Point**: Middleware runs in order as a chain. If any middleware returns an error response, the next middleware and handler are **skipped**. This ensures only authenticated users with delete permission can reach the handler.

Would you like me to check your Casbin policy file to understand what roles/users actually have delete permissions?