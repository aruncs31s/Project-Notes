# Kannur Solar Battery Monitor Current State

The report is divided into x 
1. Hardware wise 
2. Software wise

## Hardware Side

Currently the device supports the following
1. Voltage Monitoring
2. Remote turning on and off

with the help of the smart bms the following can be done
1. Charging Current
2. Charging Voltage
3. Instantanious power 
4. Remaining time calculation etc.

*note that the the above claim should be verified and it can be verified only after obtaining the uart connector and the charger*

---

## Software Side

A fully managed website by the admin it can be used to 
1. View Voltage , Current, Power
2. Get History of the reading
3. Get History of events , like when turn on or of and by whom etc.

This can be run inside the lan or can be hosted. 

Device Management
1. Anyone can create device 
2. But only one with write access to it can manage the device like turning it on or of
3. One or meany microcontrollers can be assigned to a single 
