# Readings

## Today


## 7 Day breakdown

```sql
SELECT 
r.device_id ,
AVG(r.voltage),
FROM_UNIXTIME(
	FLOOR(
		UNIX_TIMESTAMP(
			r.created_at 
		)/300
	) * 300
) as buckets
FROM readings r 
WHERE r.device_id  = 1 AND r.created_at >= NOW() - INTERVAL 7 DAY
GROUP BY buckets
ORDER BY buckets
LIMIT 100 ;
```
