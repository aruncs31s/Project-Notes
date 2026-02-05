# Minimal Flow
1. User Creates an Account 


## Sensors Page


```bash
curl --location 'http://localhost:8080/api/devices/types/sensors' 
```
```json
{
    "device_type": [
        {
            "id": 3,
            "name": "Sensor"
        },
        {
            "id": 5,
            "name": "VoltageMeter"
        },
        {
            "id": 6,
            "name": "CurrentSensor"
        },
        {
            "id": 7,
            "name": "PowerMeter"
        }
    ]
}
```