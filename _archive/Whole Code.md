```cpp
// Collection Motor Control Library..
#ifndef COLLECTION_MOTOR_H
#define COLLECTION_MOTOR_H
#include <Arduino.h>

class CollectionMotor
{
public:
    CollectionMotor(uint8_t motorPin1, uint8_t pwmPin);
    void turnOn();
    void turnOff();
    void setup(uint16_t speed);

    void increaseSpeed();
    void decreaseSpeed();

private:
    void setSpeed(uint16_t speed);
    uint8_t motorPin1;
    uint8_t pwmPin;
    uint16_t motorSpeed;
    // Optional.
    uint16_t currentState;
    int16_t speedIncrement = 10;
};

#endif // COLLECTION_MOTOR_H

#include "collection_motor.h"

CollectionMotor::CollectionMotor(uint8_t motorPin1, uint8_t pwmPin) {
  this->motorPin1 = motorPin1;
  this->pwmPin = pwmPin;
}

void CollectionMotor::setup(uint16_t speed) {
    pinMode(this->motorPin1, OUTPUT);
    pinMode(this->pwmPin, OUTPUT);
    
    digitalWrite(this->motorPin1, LOW); 
    this->setSpeed(speed);
}

void CollectionMotor::turnOn() {
    Serial.println("Turning on collection motor at speed: " + String(this->motorSpeed));

    digitalWrite(this->motorPin1, HIGH); 
    analogWrite(this->pwmPin, this->motorSpeed);
}

void CollectionMotor::turnOff() {
    Serial.println("Turning off collection motor ...");
    analogWrite(this->pwmPin, 0);
    digitalWrite(this->motorPin1, LOW); 
}

void CollectionMotor::setSpeed(uint16_t speed) {
    if (speed > 255) {
        speed = 255; 
    }
    if (speed < 0) {
        speed = 0; 
    }
    this->motorSpeed = speed;
}

void CollectionMotor::increaseSpeed() {
    this->setSpeed(this->motorSpeed + this->speedIncrement);
    Serial.println("Increasing collection motor speed to: " + String(this->motorSpeed));
    if (this->motorSpeed > 0) {
        this->turnOn();
    }
}
void CollectionMotor::decreaseSpeed() {
    this->setSpeed(this->motorSpeed - this->speedIncrement);
    Serial.println("Decreasing collection motor speed to: " + String(this->motorSpeed));
    if (this->motorSpeed > 0) {
        this->turnOn();
    } else {
        this->turnOff();
    }
}
#ifndef CONTROL_H
#define CONTROL_H
#include <Arduino.h>
void setupPins();
/*
 * Motor Control Functions
 */

// Move both motors forward
void moveForward(int speed);

// // Move both motors backward
// void moveBackward(int speed);

// Turn left (Motor A slower/off, Motor B faster)
void turnLeft(int speed);

// Turn right (Motor A faster, Motor B slower/off)
void turnRight(int speed);

// Stop all motors
void stopMotors();
//
// // Variable speed control via serial
// void variableSpeed();
//
#endif // CONTROL_H
#include "control.h"
#include "Arduino.h"
#include "../../include/config.h"

/*
 * Motor Control Functions
 */
/*
 * L298N Motor Driver Interface for ESP32
 * INA1 (Motor A) -> GPIO 19
 * INA2 (Motor A) -> GPIO 18
 * INA3 (Motor B) -> GPIO 17
 * INA4 (Motor B) -> GPIO 16
 */
void setupPins() {
  // Configure motor pins as outputs
  pinMode(MOTOR_A_PIN1, OUTPUT);
  pinMode(MOTOR_A_PIN2, OUTPUT);
  pinMode(MOTOR_B_PIN1, OUTPUT);
  pinMode(MOTOR_B_PIN2, OUTPUT);

  // Initialize all pins to LOW (motors off)
  digitalWrite(MOTOR_A_PIN1, LOW);
  digitalWrite(MOTOR_A_PIN2, LOW);
  digitalWrite(MOTOR_B_PIN1, LOW);
  digitalWrite(MOTOR_B_PIN2, LOW);
}

// Move both motors forward
void moveForward(int speed) {
  digitalWrite(MOTOR_A_PIN1, HIGH);
  digitalWrite(MOTOR_A_PIN2, LOW);
  digitalWrite(MOTOR_B_PIN1, HIGH);
  digitalWrite(MOTOR_B_PIN2, LOW);
}

// Not Supported Dues to using pump motors
// // Move both motors backward
// void moveBackward(int speed) {
//   digitalWrite(MOTOR_A_PIN1, LOW);
//   digitalWrite(MOTOR_A_PIN2, HIGH);
//   digitalWrite(MOTOR_B_PIN1, LOW);
//   digitalWrite(MOTOR_B_PIN2, HIGH);
// }

// Turn left (Motor A slower/off, Motor B faster)
void turnLeft(int speed) {
  digitalWrite(MOTOR_A_PIN1, LOW);
  digitalWrite(MOTOR_A_PIN2, LOW);
  digitalWrite(MOTOR_B_PIN1, HIGH);
  digitalWrite(MOTOR_B_PIN2, LOW);
}

// Turn right (Motor A faster, Motor B slower/off)
void turnRight(int speed) {
  digitalWrite(MOTOR_A_PIN1, HIGH);
  digitalWrite(MOTOR_A_PIN2, LOW);
  digitalWrite(MOTOR_B_PIN1, LOW);
  digitalWrite(MOTOR_B_PIN2, LOW);
}

// Stop all motors
void stopMotors() {
  digitalWrite(MOTOR_A_PIN1, LOW);
  digitalWrite(MOTOR_A_PIN2, LOW);
  digitalWrite(MOTOR_B_PIN1, LOW);
  digitalWrite(MOTOR_B_PIN2, LOW);
  Serial.println("Motors stopped");
}

#ifndef RELAY_H
#define RELAY_H
#include <Arduino.h>

#define RELAY_PIN 5  


// Deprecated: This relay is no longer used in the current design, but the code is kept for reference.
// Function prototypes
// This relay is used to controll the waste collecting motor. 
// Moving to L298N motor driver.
void setupRelay();

void activateRelay();

void deactivateRelay();


#endif // RELAY_H

#include "relay.h"

void setupRelay() {
  pinMode(RELAY_PIN, OUTPUT);
  digitalWrite(RELAY_PIN, HIGH);
}

void activateRelay() { digitalWrite(RELAY_PIN, LOW); }
void deactivateRelay() { digitalWrite(RELAY_PIN, HIGH); }
#ifndef SERVER_H
#define SERVER_H
#include "config.h"

#include <WebServer.h>

extern WebServer server;

// Web server handler functions

void handleUp();
void handleDown();
void handleLeft();
void handleRight();
void handleStop();


[[deprecated("Replaced by handleCollectionMotorOn, which has an improved interface")]] void handleRelayOn();
[[deprecated("Replaced by handleCollectionMotorOff, which has an improved interface")]] void handleRelayOff();

void handleRelayOn();
void handleRelayOff();

void handleCollectionMotorOn();
void handleCollectionMotorOff();

void handleRoot();

void handleIncreaseSpeed();
void handleDecreaseSpeed();

void setupRoutes();

extern const char *htmlPage;
#endif
#include "server.h"
#include <Arduino.h>

void setupRoutes() {
  // Setup web server routes
  server.on("/", handleRoot);
  server.on("/up", handleUp);
  // server.on("/down", handleDown);
  server.on("/left", handleLeft);
  server.on("/right", handleRight);
  server.on("/stop", handleStop);

  // Depricated.
  server.on("/relay-on", handleRelayOn);
  server.on("/relay-off", handleRelayOff);

  server.on("/collection-motor-on", handleCollectionMotorOn);
  server.on("/collection-motor-off", handleCollectionMotorOff);

  server.onNotFound(handleRoot); // Redirect to root for any unknown routes
 
  server.on("/decrease-speed",handleDecreaseSpeed);
  server.on("/increase-speed",handleIncreaseSpeed);

  server.begin();
  Serial.println("Web server started!");
  Serial.println("Open browser and go to: http://" + WiFi.localIP().toString());
}

#include "server.h" page.
#include <Arduino.h>
#include <control.h>
#include <relay.h>

const char *htmlPage = R"rawliteral(
<!DOCTYPE html>
<html>
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no">
  <title>Boat Control</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
      background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 20px;
    }
    .container {
      background: rgba(255, 255, 255, 0.05);
      border-radius: 30px;
      padding: 40px;
      backdrop-filter: blur(10px);
      border: 1px solid rgba(255, 255, 255, 0.1);
    }
    h1 {
      color: #fff;
      text-align: center;
      margin-bottom: 30px;
      font-weight: 300;
      letter-spacing: 2px;
    }
    .controls {
      display: grid;
      grid-template-columns: repeat(3, 80px);
      grid-template-rows: repeat(3, 80px);
      gap: 15px;
      justify-content: center;
    }
    .btn {
      width: 80px;
      height: 80px;
      border: none;
      border-radius: 20px;
      background: linear-gradient(145deg, #2d2d44, #1a1a2e);
      color: #fff;
      font-size: 28px;
      cursor: pointer;
      transition: all 0.2s ease;
      box-shadow: 5px 5px 15px rgba(0,0,0,0.3), -5px -5px 15px rgba(255,255,255,0.05);
      display: flex;
      align-items: center;
      justify-content: center;
    }
    .btn:hover {
      transform: scale(1.05);
      background: linear-gradient(145deg, #3d3d54, #2a2a3e);
    }
    .btn:active {
      transform: scale(0.95);
      box-shadow: 2px 2px 8px rgba(0,0,0,0.3), -2px -2px 8px rgba(255,255,255,0.05);
    }
    .btn-up { grid-column: 2; grid-row: 1; }
    .btn-left { grid-column: 1; grid-row: 2; }
    .btn-stop { grid-column: 2; grid-row: 2; background: linear-gradient(145deg, #e74c3c, #c0392b); }
    .btn-stop:hover { background: linear-gradient(145deg, #ff6b5b, #e74c3c); }
    .btn-right { grid-column: 3; grid-row: 2; }
    .btn-down { grid-column: 2; grid-row: 3; }
    .relay-control {
      margin-top: 30px;
      text-align: center;
    }
    .relay-btn {
      width: 200px;
      height: 60px;
      border: none;
      border-radius: 15px;
      color: #fff;
      font-size: 16px;
      font-weight: 500;
      cursor: pointer;
      transition: all 0.2s ease;
      letter-spacing: 1px;
      margin: 0 10px;
    }
    .relay-on {
      background: linear-gradient(145deg, #27ae60, #229954);
      box-shadow: 5px 5px 15px rgba(0,0,0,0.3), -5px -5px 15px rgba(255,255,255,0.05);
    }
    .relay-on:hover {
      background: linear-gradient(145deg, #2ecc71, #27ae60);
      transform: translateY(-2px);
    }
    .relay-off {
      background: linear-gradient(145deg, #7f8c8d, #5d6d7e);
      box-shadow: 5px 5px 15px rgba(0,0,0,0.3), -5px -5px 15px rgba(255,255,255,0.05);
    }
    .relay-off:hover {
      background: linear-gradient(145deg, #95a5a6, #7f8c8d);
      transform: translateY(-2px);
    }
    .relay-btn:active {
      transform: translateY(0);
    }
    .status {
      text-align: center;
      margin-top: 25px;
      color: rgba(255,255,255,0.6);
      font-size: 14px;
      letter-spacing: 1px;
    }
    #status-text {
      color: #4ade80;
      font-weight: 500;
    }
  </style>
    </head>
    <body>
    <div class="container">
        <h1>BOAT CONTROL</h1>
        <div class="controls">
        <button class="btn btn-up" onclick="sendCmd('up')">Up</button>
        <button class="btn btn-left" onclick="sendCmd('left')">Left</button>
        <button class="btn btn-stop" onclick="sendCmd('stop')">Stop</button>
        <button class="btn btn-right" onclick="sendCmd('right')">Right</button>
        </div>
        <div class="relay-control">
        <button class="relay-btn relay-on" onclick="sendCmd('collection-motor-on')">Start Collecting</button>
        <button class="relay-btn relay-off" onclick="sendCmd('collection-motor-off')">Stop Collecting</button>
        </div>
        <div class="relay-control">
        <button class="relay-btn relay-on" onclick="sendCmd('increase-speed')">Increase Speed</button>
        <button class="relay-btn relay-off" onclick="sendCmd('decrease-speed')">Decrease Speed</button>
        </div>
        <div class="status">Status: <span id="status-text">Ready</span></div>
    </div>
    <script>
        function sendCmd(cmd) {
        fetch('/' + cmd)
            .then(r => r.text())
            .then(t => document.getElementById('status-text').innerText = t)
            .catch(e => document.getElementById('status-text').innerText = 'Error');
        }
    </script>
    </body>
    </html>
    )rawliteral";
#include "server.h"
#include <control.h>
#include <relay.h>
#include <collection_motor.h>

extern CollectionMotor c;

// Handler functions
void handleUp() {
  moveForward(255);
  server.send(200, "text/plain", "Moving Forward");
}

void handleLeft() {
  turnLeft(255);
  server.send(200, "text/plain", "Turning Left");
}

void handleRight() {
  turnRight(255);
  server.send(200, "text/plain", "Turning Right");
}

void handleStop() {
  stopMotors();
  server.send(200, "text/plain", "Stopped");
}

// Relay Handlers

// Depricated.
void handleRelayOn() {
  activateRelay();
  server.send(200, "text/plain", "Relay ON");
}

// Depricated.
void handleRelayOff() {
  deactivateRelay();
  server.send(200, "text/plain", "Relay OFF");
}


void handleCollectionMotorOn() {
  c.turnOn();
  server.send(200, "text/plain", "Collection Motor ON");
}

void handleCollectionMotorOff() {
  c.turnOff();
  server.send(200, "text/plain", "Collection Motor OFF");
}


// Web server handlers
void handleRoot() { server.send(200, "text/html", htmlPage); }


void handleIncreaseSpeed() {
  c.increaseSpeed();
  server.send(200, "text/plain", "Increasing Collection Motor Speed");
}

void handleDecreaseSpeed() {
  c.decreaseSpeed();
  server.send(200, "text/plain", "Decreasing Collection Motor Speed");
}
#ifndef WIFI_H
#define WIFI_H

// WiFi credentials
extern const char *ssid;
extern const char *password;


// Setup Wifi
void setupWifi();

#endif // WIFI_H

#include "config.h"
#include <WiFi.h>
#include "wifi.h"


void setupWifi() {
  Serial.println("Relay initialized.");
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nWiFi connected!");
  Serial.print("IP Address: ");
  Serial.println(WiFi.localIP());
}
#ifndef SERIAL_H
#define SERIAL_H

void setupSerial();
#endif // SERIAL_H
#include <Arduino.h>


void setupSerial() {
  Serial.begin(115200);
  delay(100);
}
#include "wifi.h"
#include <Arduino.h>
#include <WebServer.h>
#include <WiFi.h>
#include <control.h>
#include <relay.h>
#include <server.h>
#include "collection_motor.h"
#include "config.h"
#include "serial.h"

// WiFi credentials - UPDATE THESE
const char *ssid = "pi_wifi";
const char *password = "12345678";


WebServer server(PORT);

CollectionMotor c(MOTOR_PIN1, PWM_PIN);


void setup() {
  // Initialize serial communication
  setupSerial();

  // Initialize motor control pins
  setupPins();

  // // Initialize relay
  // setupRelay();
  // Collection Motor Setup
  c.setup(70); 


  // Connect to WiFi
  setupWifi();

  // Setup web server routes
  setupRoutes();
}

void loop() { server.handleClient(); }
```