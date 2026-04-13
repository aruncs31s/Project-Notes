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