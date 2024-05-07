---
title: Arduino R4 방수 온도계(DS18B20) Serial Monitor & BLE 통신 
categories: [Arduino,communication]
tags: [macos,arduino,ds18b20,ble,bluetooth,serial,communication]
---

## Environment

- HW : Mac-mini M2
- OS : Sonoma 14.4.1
- Arduino Uno R4 WiFi
- Sensor : 방수 온도계, DS18B20

---

## Intro

1. Arduino Uno R4 WiFi
2. DS18B20
3. Source Code    
  3.1. Aruino Serial Monitoring, Arduino IDE or vscode     
  3.2. Aruino BLE 통신: DS18B20 없이 통신 기능만 테스트, LightBlue App 사용법   
  3.3. Aruino BLE 통신 + DS18B20 Sensor 통합    

---


## 1. Arduino Uno R4 WiFi
![ardr4](/assets/img/ardr4.jpeg#left)    

- Microcontroller: Renesas RA4M1 (Arm® Cortex®-M4)
- Analog Input: 6 pins
- Digital I/O: 14 pins
- ADC resolution: 14-bit
- ESP32-S3: WiFi/BLE module
    802.11 b/g/n 2.4G
    BLE, Bluetooth5

    
## 2. DS18B20
![ardr4_ds18b20](/assets/img/ardr4_ds18b20.jpeg)

방수형 DS18B20은 DALLAS Semiconductor(18B20)을 stain-less steel tube(prob) 타입으로 제작된 Digital 온도센서   
1-wire bus protocol library를 이용하여 MCU와 통신   
Analog Sensor LM35 or TMP36의 아두이노에서 모니터링 방법은 아래 사이트 참고   
[Analog Sensor with Arduino](https://jinozblog.tistory.com/207)    
    

Arduino & Sensor Pin-Map Description
- Arduino Uno R4 WiFi  
  power source: 5V(+), GND(-)   
  Digital I/O: D8    
  Built-In LED: D13
  
- Sensor: DS18B20: Arduino Pin-Map   
  VCC: 5V    
  GND: GND    
  DAT: D8(8-pin)        
  


## 3. Source Code

#### Arduino IDE or vscode Library Install

Arduino IDE 2.3.2   
  > Tools > Manage Libraris   
  > search: DallasTemperature, OneWire    
  > INSTALL   


vscode(Visual Studio Code) Editor   
  > cmd + shift + p   
  > Arudino: Library Manager    
  > search: DallasTemperature, OneWire    
  > INSTALL   

[참고: Arduino IDE & vscode 설정 방법](https://jinozblog.tistory.com/207)    


#### 3.1. Aruino Serial Monitoring

code review   
  > DS18B20 DallasTemperature sensor library    
  > OneWire Communication library   
  > Aruino IDE Serial Monitor or vscode Serial Monitor를 통해 센서값 확인   

```cpp
// Temp.-Sensor
#include <OneWire.h>
#include <DallasTemperature.h>

// Temp.-Sensor: Set-Up
#define ONE_WIRE_BUS 8
OneWire Wire(ONE_WIRE_BUS);
DallasTemperature sensor(&Wire);
char value[8];

// General Variable
const int baud_rate = 9600;
const int sampletime = 1000; //loop cycle 1000ms
String stringsum;

// Arduino initial Set-up
void setup() {
  Serial.begin(baud_rate);
}

// Arduino loop
void loop() {
  // Temp.-Sensor Read & Get data
  sensor.requestTemperatures();
  dtostrf(sensor.getTempCByIndex(0) , 5, 2, value);
  
  // separate multi-datas to string: 'ia' data-1 'b' data-2 'f'
  stringsum = "ia" + String(sampletime) + "b" + String(value) + "f";
  Serial.println(stringsum);
  
  while (millis() % sampletime != 0);
}

```


#### 3.2. Aruino BLE 통신: DS18B20 없이 통신 기능만 테스트

code review   
  > ArduinoBLE library    
  > Aruino R4 SP32-S3 WiFi/BLE Module은 동시에 사용할 수 없다.    
  > 여기서 주목해야할 code는 sensingCharacteristic    
  >> void setup()    
  >> // Set sensing string   
  >> sensingCharacteristic.setValue(sensing);   
  >   
  >> void loop()   
  >> // Write string    
  >> sensingCharacteristic.writeValue(stringsum);   


Serial Monitor를 통해 통신 확인하거나 LightBlue App.을 통해 확인 가능    

![lightblue_app_galaxy_s20_android](/assets/img/LightBlue_android.png)
![lightblue_app_iphone_mini12_ios](/assets/img/LightBlue_ios.png)


```cpp
#include <ArduinoBLE.h>

// General Variable
const int baud_rate = 9600;
const int sampletime = 1000; //loop cycle 1000ms
String stringsum;

// Arduino Built-in LED
const int ledPin = LED_BUILTIN; // BLE Connect/Dis-connect Viewer

// Arduino BLE Define
static const char* sensing = "Hello, persona!"; // TX, RX char Variable "sensing"
BLEService sensingService("0454e724-abec-43e1-8bbd-46db509439c4");  // User defined service
BLEStringCharacteristic sensingCharacteristic("0454e724-abec-43e1-8bbd-46db509439c4",  BLERead | BLENotify, 15);

// Arduino initial Set-up
void setup() {
  Serial.begin(baud_rate);
  pinMode(LED_BUILTIN, OUTPUT); // initialize the built-in LED pin
  
  if (!BLE.begin()) {   // initialize BLE
  Serial.println("starting BLE failed!");
  while (1);
  }

  BLE.setLocalName("ArduinoR4_persona");  // Set name for connection
  BLE.setAdvertisedService(sensingService); // Advertise service
  sensingService.addCharacteristic(sensingCharacteristic); // Add characteristic to service
  BLE.addService(sensingService); // Add service
  
  // assign event handlers for connected, disconnected to peripheral
  BLE.setEventHandler(BLEConnected, blePeripheralConnectHandler);
  BLE.setEventHandler(BLEDisconnected, blePeripheralDisconnectHandler);
  
  // assign event handlers for characteristic
  sensingCharacteristic.setValue(sensing); // Set sensing string
  BLE.advertise();  // Start advertising
  Serial.println(("Bluetooth® device active, waiting for connections..."));
}

// Arduino loop
void loop() {
  BLE.poll();
  stringsum = "ia" + String(sampletime) + "f";
  Serial.println(stringsum);
  sensingCharacteristic.writeValue(stringsum);
  while (millis() % sampletime != 0);
}

// Arduino BLE Connection Status Action Function
void blePeripheralConnectHandler(BLEDevice central) {
  Serial.print("Connected event, central: ");
  Serial.println(central.address());
  Serial.println("LED on");
  digitalWrite(LED_BUILTIN, HIGH);
}

// Arduino BLE Dis-connection Status Action Function
void blePeripheralDisconnectHandler(BLEDevice central) {
  Serial.print("Disconnected event, central: ");
  Serial.println(central.address());
  Serial.println("LED off");
  digitalWrite(LED_BUILTIN, LOW);
}

```


#### 3.3. Aruino BLE 통신 + DS18B20 Sensor 통합

code review   
  > ArduinoBLE Code에 DS18B20 Sensor Read Code를 적절하게 배치      
  > 선언부: DallasTemperature & OneWire   
  > void setup(): 3.2. BLE와 동일 수정 없음   
  > void loop(): stringsum = "ia" + String(sampletime) + "b" + String(value) + "f";   
  > Mac-mini M2에서 ArduinoBLE mode 연결 안됨.   


```cpp
#include <ArduinoBLE.h>
// Temp.-Sensor
#include <OneWire.h>
#include <DallasTemperature.h>

// Temp.-Sensor: Set-Up
#define ONE_WIRE_BUS 8
OneWire Wire(ONE_WIRE_BUS);
DallasTemperature sensor(&Wire);
char value[8];

// General Variable
const int baud_rate = 9600;
const int sampletime = 1000; //loop cycle 1000ms
String stringsum;

// Arduino Built-in LED
const int ledPin = LED_BUILTIN; // BLE Connect/Dis-connect Viewer

// Arduino BLE Define
static const char* sensing = "Hello, persona!"; // TX, RX char Variable "sensing"
BLEService sensingService("0454e724-abec-43e1-8bbd-46db509439c4");  // User defined service
BLEStringCharacteristic sensingCharacteristic("0454e724-abec-43e1-8bbd-46db509439c4",  BLERead | BLENotify, 15);

// Arduino initial Set-up
void setup() {
  Serial.begin(baud_rate);
  pinMode(LED_BUILTIN, OUTPUT); // initialize the built-in LED pin
  
  if (!BLE.begin()) {   // initialize BLE
  Serial.println("starting BLE failed!");
  while (1);
  }

  BLE.setLocalName("ArduinoR4_persona");  // Set name for connection
  BLE.setAdvertisedService(sensingService); // Advertise service
  sensingService.addCharacteristic(sensingCharacteristic); // Add characteristic to service
  BLE.addService(sensingService); // Add service
  
  // assign event handlers for connected, disconnected to peripheral
  BLE.setEventHandler(BLEConnected, blePeripheralConnectHandler);
  BLE.setEventHandler(BLEDisconnected, blePeripheralDisconnectHandler);
  
  // assign event handlers for characteristic
  sensingCharacteristic.setValue(sensing); // Set sensing string
  BLE.advertise();  // Start advertising
  Serial.println(("Bluetooth® device active, waiting for connections..."));
}

// Arduino loop
void loop() {
  BLE.poll();
  // Temp.-Sensor Read
  sensor.requestTemperatures();
  dtostrf(sensor.getTempCByIndex(0) , 5, 2, value);
  
  stringsum = "ia" + String(sampletime) + "b" + String(value) + "f";
  Serial.println(stringsum);
  sensingCharacteristic.writeValue(stringsum);
  while (millis() % sampletime != 0);
}

// Arduino BLE Connection Status Action Function
void blePeripheralConnectHandler(BLEDevice central) {
  Serial.print("Connected event, central: ");
  Serial.println(central.address());
  Serial.println("LED on");
  digitalWrite(LED_BUILTIN, HIGH);
}

// Arduino BLE Dis-connection Status Action Function
void blePeripheralDisconnectHandler(BLEDevice central) {
  Serial.print("Disconnected event, central: ");
  Serial.println(central.address());
  Serial.println("LED off");
  digitalWrite(LED_BUILTIN, LOW);
}

```

