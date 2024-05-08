---
title: Arduino R4 LDR(Light Dependent Resistor) Serial Monitor & Serial Plotter
categories: [Arduino,communication]
tags: [macos,arduino,LDR,photocell,sensor,serial,communication]
---

## Environment

- HW : Mac-mini M2
- OS : Sonoma 14.4.1
- Arduino Uno R4 WiFi
- Sensor : LDR or PhotoCell
- Resistor : 10kohm

---

## Intro

1. Arduino & PhotoCell-Resister
2. Source Code    

---


## 1. Arduino & PhotoCell-Resister
![ardr4_ldr](/assets/img/ardr4_photocell.jpeg)    

Arduino & Sensor Pin-Map Description
- Arduino Uno R4 WiFi  
  power source: 5V(+), GND(-)   
  Analog Input: A0    
  
- Sensor: PhotoCell   
  PhotoCell A - A0 - Resistor(10kohm) - GND     
  PhotoCell B - 5V    
  *photocell A/B wire 구분 없음
    

## 3. Source Code

ArduinoIDE Source Code Upload & Serial Plotter/Monitor   
  > ArduinoIDE 2.3.2    
  > Top-Left Tab Verify | Upload | Start Debugging    
  > Top-Right Tab Serial Plotter | Serial Monitor   

![ardr4_ide_ldr](/assets/img/ardide_photocell.png)    

```cpp
// PhotoCell
const int photoR = A0; // Photoresistor at Arduino analog pin A0
int sensorValue = 0;  // ADC: Photoresistor (0-1023)

// General Variable
const int baud_rate = 9600;
const int sampletime = 10; //loop cycle 10ms
String stringsum;

// Arduino initial Set-up
void setup() {
  Serial.begin(baud_rate);
}

// Arduino loop
void loop() {
  // Sensor Read & Get data
  sensorValue = analogRead(photoR);
  Serial.println(sensorValue);
  
  while (millis() % sampletime != 0);
}

```
