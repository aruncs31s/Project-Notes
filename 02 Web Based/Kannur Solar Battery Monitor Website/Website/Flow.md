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


### Device Creation

1. User Logs In or Creates an Account 
2. Go to My Devices Page If he has no device , there will be Button to add New Devices will be present , 
3. He can only add solar devices or any Higher Level Devices from there
4. After Adding a Solar Device , He will go to its details page , and from there he can add a connected device , which will provide battery and voltage reading for the microcont