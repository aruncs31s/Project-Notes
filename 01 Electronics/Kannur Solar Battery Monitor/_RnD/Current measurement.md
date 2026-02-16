```cpp
const int shuntPin = 34;  // ADC pin
const float shuntRes = 0.1;  // 0.1 ohm
const float adcRef = 3.3;
const int adcMax = 4095;  // 12-bit

void setup() {
  Serial.begin(115200);
}

void loop() {
  int adcValue = analogRead(shuntPin);
  float voltage = (adcValue * adcRef) / adcMax;
  float current = voltage / shuntRes;

  Serial.print("Current: ");
  Serial.print(current);
  Serial.println(" A");

  delay(1000);
}
```