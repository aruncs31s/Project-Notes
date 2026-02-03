# Example: How to Structure a Project

This document shows a concrete example of how to structure and document a project using the guidelines in this repository.

## Example Project: Smart Plant Watering System

Let's walk through documenting an electronics project from start to finish.

### Step 1: Setup Project Directory

```bash
cd "01 Electronics"
mkdir "Smart Plant Watering System"
cd "Smart Plant Watering System"

# Create directory structure
mkdir -p attachments/images
mkdir -p attachments/diagrams  
mkdir -p attachments/docs
mkdir -p code
mkdir -p versions
```

**Resulting structure:**
```
01 Electronics/
└── Smart Plant Watering System/
    ├── attachments/
    │   ├── images/
    │   ├── diagrams/
    │   └── docs/
    ├── code/
    └── versions/
```

### Step 2: Create Main Documentation File

Copy the template and rename:
```bash
cp ../../templates/Electronics-Project-Template.md "Smart Plant Watering System.md"
```

### Step 3: Fill in Basic Information

Open `Smart Plant Watering System.md` and update:

```markdown
---
id: Smart_Plant_Watering_System
tags:
  - project
  - electronics
  - iot
  - arduino
dg-publish: true
---

# Smart Plant Watering System

## Status
🚧 In Progress

## Overview

An automated plant watering system that monitors soil moisture and 
waters plants when needed. Uses Arduino Nano, soil moisture sensor, 
relay module, and a water pump.

## Objectives

- [x] Monitor soil moisture levels
- [x] Automatically water when soil is dry
- [ ] Add LCD display for status
- [ ] Implement WiFi connectivity
- [ ] Create mobile app for remote monitoring
```

### Step 4: Add System Architecture

Create a block diagram in Excalidraw and save as `block-diagram.excalidraw.md` in `attachments/diagrams/`

Then reference it:

```markdown
## System Architecture

### Block Diagram

![[block-diagram.excalidraw.md]]

### Components List

| Component | Specification | Quantity | Purpose |
|-----------|---------------|----------|---------|
| Microcontroller | Arduino Nano | 1 | Main controller |
| Soil Moisture Sensor | Capacitive | 1 | Measure soil moisture |
| Relay Module | 5V Single Channel | 1 | Control water pump |
| Water Pump | 3-6V DC Mini Pump | 1 | Pump water |
| Power Supply | 5V 2A Adapter | 1 | Power system |
| Tubing | 4mm ID | 1m | Water delivery |
```

### Step 5: Document Circuit Design

Take a photo of your breadboard circuit or create a schematic:
- Save image as `circuit-breadboard.jpg` in `attachments/images/`
- Save schematic as `circuit-schematic.png` in `attachments/diagrams/`

```markdown
## Circuit Design

### Breadboard Prototype

![[circuit-breadboard.jpg]]

### Schematic Diagram

![[circuit-schematic.png]]

### Circuit Explanation

**Power Supply:**
- 5V adapter connected to Arduino Nano via USB or Vin pin
- Relay and pump powered from 5V rail

**Sensor Interface:**
- Soil moisture sensor connected to analog pin A0
- Sensor VCC to 5V, GND to GND

**Relay Control:**
- Relay signal pin connected to digital pin D7
- Relay controls 5V supply to water pump
```

### Step 6: Add Code

Save your Arduino code in `code/plant_watering.ino`:

```cpp
// code/plant_watering.ino
const int moisturePin = A0;
const int relayPin = 7;
const int threshold = 300;  // Adjust based on sensor

void setup() {
  pinMode(relayPin, OUTPUT);
  digitalWrite(relayPin, LOW);
  Serial.begin(9600);
}

void loop() {
  int moisture = analogRead(moisturePin);
  Serial.print("Moisture: ");
  Serial.println(moisture);
  
  if (moisture < threshold) {
    // Soil is dry, water the plant
    digitalWrite(relayPin, HIGH);
    delay(2000);  // Water for 2 seconds
    digitalWrite(relayPin, LOW);
    delay(10000); // Wait 10 seconds before next check
  }
  
  delay(1000);
}
```

Document it in your main file:

```markdown
## Software/Firmware

### Key Code Snippets

```cpp
// Read soil moisture
int moisture = analogRead(moisturePin);

// Water if soil is dry
if (moisture < threshold) {
  digitalWrite(relayPin, HIGH);  // Turn on pump
  delay(2000);                   // Water for 2s
  digitalWrite(relayPin, LOW);   // Turn off pump
}
```

### Configuration

```cpp
// Pin definitions
const int moisturePin = A0;     // Analog input for sensor
const int relayPin = 7;         // Digital output for relay

// Threshold (lower = drier soil)
const int threshold = 300;      // Adjust based on calibration
```
```

### Step 7: Document Testing

Take photos during testing:
- `test-setup.jpg`
- `working-prototype.jpg`

Save in `attachments/images/`

```markdown
## Testing & Calibration

### Test Procedure

1. **Sensor Calibration**:
   - Measured dry soil: 800
   - Measured wet soil: 200
   - Set threshold: 300

2. **Pump Test**:
   - Pump flow rate: ~100ml/min
   - Optimal watering time: 2 seconds
   - Water volume per cycle: ~3ml

3. **Integration Test**:
   - System runs continuously for 24 hours
   - Waters plant 3 times per day
   - No false triggers observed

### Results

| Test | Expected | Actual | Status |
|------|----------|--------|--------|
| Dry soil detection | < 300 | 280 | ✅ Pass |
| Wet soil detection | > 500 | 650 | ✅ Pass |
| Pump activation | 2s duration | 2.1s | ✅ Pass |
| Power consumption | < 500mA | 380mA | ✅ Pass |

### Photos

![[test-setup.jpg]]
![[working-prototype.jpg]]
```

### Step 8: Document Challenges

```markdown
## Challenges & Solutions

### Challenge 1: Sensor Reading Fluctuations
**Problem**: Soil moisture readings were unstable, jumping between values
**Solution**: 
- Added 100µF capacitor between sensor VCC and GND
- Implemented moving average of 10 readings
- Stabilized readings significantly

**Code:**
```cpp
// Moving average for stable readings
int readMoisture() {
  long sum = 0;
  for(int i = 0; i < 10; i++) {
    sum += analogRead(moisturePin);
    delay(10);
  }
  return sum / 10;
}
```

### Challenge 2: Pump Sometimes Didn't Start
**Problem**: Water pump occasionally failed to start when relay activated
**Solution**: 
- Added 1N4007 flyback diode across pump terminals
- Increased delay before measuring to ensure pump started
- Problem resolved
```

### Step 9: Add Bill of Materials

Keep track of costs in a table:

```markdown
## Bill of Materials (BOM)

| Item | Quantity | Unit Price | Total | Supplier |
|------|----------|------------|-------|----------|
| Arduino Nano | 1 | $4.50 | $4.50 | Amazon |
| Capacitive Soil Sensor | 1 | $3.20 | $3.20 | AliExpress |
| Relay Module | 1 | $2.00 | $2.00 | Local Store |
| Mini Water Pump | 1 | $3.50 | $3.50 | Amazon |
| 5V Power Adapter | 1 | $5.00 | $5.00 | Amazon |
| Tubing (1m) | 1 | $2.00 | $2.00 | Hardware Store |
| Jumper Wires | 10 | $0.10 | $1.00 | Local Store |
| **Total** | | | **$21.20** | |

*Prices as of 2025-10-20*
```

### Step 10: Plan Future Improvements

```markdown
## Future Improvements

- [x] Basic moisture sensing and watering ✅ 2025-10-15
- [ ] Add LCD display to show moisture level
- [ ] Implement WiFi (ESP8266) for remote monitoring
- [ ] Create mobile app for notifications
- [ ] Add multiple sensors for different plants
- [ ] Design custom PCB
- [ ] Add water level sensor for reservoir
- [ ] Implement data logging and analytics
```

### Step 11: Add Resources

```markdown
## Resources

### Datasheets
- [Arduino Nano Pinout](https://content.arduino.cc/assets/Pinout-NANO_latest.pdf)
- [Capacitive Soil Sensor Guide](https://how2electronics.com/capacitive-soil-moisture-sensor-arduino/)

### References
- [Similar project inspiration](https://create.arduino.cc/projecthub/electropeak/automatic-plant-watering-system-f10fdb)
- [Soil moisture sensor comparison](https://www.youtube.com/watch?v=udmJyncDvw0)

### Related Projects
- [[Greenhouse Automation]] - Future expansion
- [[Solar Battery Monitor]] - Similar power management concepts
```

## Final Directory Structure

After completing documentation, the project looks like:

```
01 Electronics/
└── Smart Plant Watering System/
    ├── Smart Plant Watering System.md    # Main documentation
    ├── attachments/
    │   ├── images/
    │   │   ├── circuit-breadboard.jpg
    │   │   ├── test-setup.jpg
    │   │   └── working-prototype.jpg
    │   ├── diagrams/
    │   │   ├── block-diagram.excalidraw.md
    │   │   └── circuit-schematic.png
    │   └── docs/
    │       ├── arduino-nano-pinout.pdf
    │       └── receipt-components.pdf
    ├── code/
    │   ├── plant_watering.ino
    │   └── README.md
    └── versions/
        └── Version 1.md
```

## Key Takeaways

### Do's ✅

1. **Use descriptive names** for all files and images
2. **Document as you build** - don't wait until the end
3. **Include photos** of your actual project
4. **Add code comments** that explain the logic
5. **Track costs** in BOM for future reference
6. **Link related projects** to build a knowledge web
7. **Update challenges** as you solve problems

### Don'ts ❌

1. Don't use generic names like `IMG_1234.jpg`
2. Don't leave sections empty - use "TBD" if needed
3. Don't forget to update frontmatter tags
4. Don't skip the testing section
5. Don't ignore failed attempts - document them!

## Templates vs Reality

The template is a guide, not a strict requirement. This example:
- Used most sections from the template
- Skipped "Power Consumption Analysis" (not critical)
- Added extra sections for specific needs
- Customized based on project type

**Remember**: Adapt the template to your project, not the other way around!

## Progression Over Time

Projects evolve. Here's how documentation might grow:

### Week 1: Initial Documentation
- Basic overview
- Component list
- Initial circuit design

### Week 2: Implementation
- Code added
- Testing section filled
- Photos of prototype

### Month 1: Completion
- All sections complete
- Challenges documented
- Future improvements listed

### Month 3: Updates
- Created Version 2 with improvements
- Added Version 1 to versions/ folder
- Updated main documentation

## Tips for Success

1. **Start minimal**: Get basics down first
2. **Take photos early**: Capture work in progress
3. **Document failures**: They're valuable learning
4. **Update regularly**: Don't let it get stale
5. **Link liberally**: Connect related content
6. **Use checklists**: Track progress visually

## Example Repository Structure

Here's how multiple projects fit together:

```
Project-Notes/
├── 01 Electronics/
│   ├── Smart Plant Watering System/        # This example
│   │   ├── Smart Plant Watering System.md
│   │   ├── attachments/
│   │   └── code/
│   ├── Weather Station/
│   │   └── ...
│   └── LED Cube/
│       └── ...
├── 02 Web Based/
│   └── Plant Monitor Dashboard/            # Web interface for above
│       └── ...
└── templates/                              # Templates for new projects
    └── ...
```

## Next Steps

After documenting your project:

1. Update the category index file (e.g., `Electronics Projects.md`)
2. Link from related projects
3. Share with others who might build on it
4. Keep updating as project evolves
5. Start your next project!

---

This example demonstrates the recommended structure in practice. Your projects don't need to be perfect - just documented well enough that you (or others) can understand and reproduce them later!

*Last Updated: 2025-10-23*
