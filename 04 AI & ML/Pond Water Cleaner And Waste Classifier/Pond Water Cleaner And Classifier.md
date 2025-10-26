# Pond Water Cleaner And Classifier

- [[Machine Learning]]
- [[Final Presentaion]]
This project involves a waste cleaner placed in a pond, which uses YOLO object detection to identify waste in front, moves towards the detected objects, and separates them using a servo motor. 

## Components Required:
1. Raspberry Pi 5
2. Web Cam
3. Motor Driver(L298N)
4. DC Motors
5. Ultrasonic Sensor (optional, for additional obstacle avoidance)
6. 2x Servo Motors (for dual bin sorting)
7. Organic Waste Bin
8. Non-Organic Waste Bin
9. Sorting Mechanism/Gate

## Components Block Diagram

```mermaid
graph TD
    A[Raspberry Pi 5] --> B[Web Cam]
    A --> C[Motor Driver L298N]
    A --> D[Servo Motor 1 - Collection]
    A --> E[Servo Motor 2 - Sorting Gate]
    A --> F[Ultrasonic Sensor]
    A --> G[YOLO Object Detection Model]
    
    B --> G
    C --> H[DC Motors]
    D --> I[Collection Mechanism]
    E --> J[Sorting Gate]
    
    J --> K[Organic Waste Bin]
    J --> L[Non-Organic Waste Bin]
    
    style A fill:#e1f5fe
    style B fill:#f3e5f5
    style G fill:#ffeb3b
    style K fill:#e8f5e8
    style L fill:#fff3e0
```

## System Flow Diagram

```mermaid
graph TD
    A[Web Cam Feed] --> B[YOLO Object Detection]
    B --> C{Waste Detected?}
    
    C -->|Yes| D[Extract Bounding Box]
    C -->|No| E[Continue Scanning]
    
    D --> F[Classify Waste Type]
    F --> G[Get Confidence Score]
    G --> H{Confidence > Threshold?}
    
    H -->|Yes| I{Organic or Non-Organic?}
    H -->|No| E
    
    I --> J[Calculate Object Position]
    J --> K[Send Movement Commands]
    K --> L[DC Motors Move Robot]
    
    L --> M{At Collection Distance?}
    M -->|Yes| N[Activate Collection Servo]
    M -->|No| L
    
    N --> O[Collect Waste]
    O --> P[Activate Sorting Gate Servo]
    
    P --> Q{Waste Classification?}
    Q -->|Organic| R[Gate Position: Organic Bin]
    Q -->|Non-Organic| S[Gate Position: Non-Organic Bin]
    
    R --> T[Drop Waste in Organic Bin]
    S --> U[Drop Waste in Non-Organic Bin]
    
    T --> V[Reset Gate & Collection]
    U --> V
    V --> E
    E --> W[Random Movement]
    W --> A
    
    style B fill:#ffeb3b
    style F fill:#ffeb3b
    style T fill:#e8f5e8
    style U fill:#fff3e0
```

