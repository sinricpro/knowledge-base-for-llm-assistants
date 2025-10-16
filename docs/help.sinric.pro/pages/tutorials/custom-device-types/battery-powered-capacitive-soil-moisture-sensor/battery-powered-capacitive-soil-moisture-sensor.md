---
title:  Battery Powered Capacitive Soil Moisture Sensor for ESP8266, ESP32, Raspberry Pi Pico W
layout: post
---
 
In this part, we'll guide you on building a battery powered **Capacitive Soil Moisture Sensor** which will wake up every 15 mins and report the soil status to the server.

Please complete the [Capacitive Soil Moisture Sensor](https://help.sinric.pro/pages/tutorials/custom-device-types/capacitive-soil-moisture-sensor/HW-390.html) to understand how to setup the soil moisture sensor with Sinric Pro.

#### Complete Code

```cpp
/*
 * If you encounter any issues:
 * - check the readme.md at https://github.com/sinricpro/esp8266-esp32-sdk/blob/master/README.md
 * - ensure all dependent libraries are installed
 * - see https://github.com/sinricpro/esp8266-esp32-sdk/blob/master/README.md#arduinoide
 * - see https://github.com/sinricpro/esp8266-esp32-sdk/blob/master/README.md#dependencies
 * - open serial monitor and check whats happening
 * - check full user documentation at https://sinricpro.github.io/esp8266-esp32-sdk
 * - visit https://github.com/sinricpro/esp8266-esp32-sdk/issues and check for existing issues or open a new one
 */

 // Custom devices requires SinricPro ESP8266/ESP32 SDK 2.9.6 or later

// Uncomment the following line to enable serial debug output
//#define ENABLE_DEBUG

#ifdef ENABLE_DEBUG
  #define DEBUG_ESP_PORT Serial
  #define NODEBUG_WEBSOCKETS
  #define NDEBUG
#endif

#include <Arduino.h>
#ifdef ESP8266
  #include <ESP8266WiFi.h>
#endif
#ifdef ESP32
  #include <WiFi.h>
#endif
 
#include <SinricPro.h>
#include "CapacitiveSoilMoistureSensor.h"

#define APP_KEY    ""
#define APP_SECRET ""
#define DEVICE_ID  ""

#define SSID       ""
#define PASS       ""

#define BAUD_RATE               115200    // Change baudrate to your need (used for serial monitor)
#define SLEEP_DELAY_IN_SECONDS  60 * 15        // sleep duration. 15 mins.

#if defined(ESP8266)
  const int adcPin = A0;  
#elif defined(ESP32) 
  const int adcPin = 34;  
#elif defined(ARDUINO_ARCH_RP2040)
  const int adcPin = 26;  
#endif

const int VERY_DRY = 720; // TODO: This is when soil is dry. Adjust according to your sensor
const int VERY_WET = 560; // TODO: This is when soil is wet. Adjust according to your sensor

CapacitiveSoilMoistureSensor &capacitiveSoilMoistureSensor = SinricPro[DEVICE_ID];

void updateSoilState(String mode) {
  capacitiveSoilMoistureSensor.sendModeEvent("modeInstance1", mode, "PHYSICAL_INTERACTION");
}

void updateMoistureLevel(int value) {
  capacitiveSoilMoistureSensor.sendRangeValueEvent("rangeInstance1", value);
}

void sendSoilMoisture() {
  int soilMoisture = analogRead(adcPin);
  int percentage = map(soilMoisture, VERY_WET, VERY_DRY, 100, 0); 

  if (percentage < 0) {
    percentage = 0;
  }
  if (percentage > 100) {
    percentage = 100;
  }

  Serial.printf("Soil Moisture: %d. as a percentage: %d%%\r\n", soilMoisture, percentage); 
  
  // Determine Wet or Dry.
  String soilMoistureStr = "";
  if(soilMoisture > NEITHER_DRY_OR_WET) {
    soilMoistureStr = "Dry"; // NOTE: must be Wet, Dry. This is from modes list in template.
  } else {
    soilMoistureStr = "Wet";
  }
  
  // Update Wet or Dry.
  updateSoilState(soilMoistureStr);

  // Update Moisture level label
  updateMoistureLevel(soilMoisture); 
}
 
void setupSensor() {
  pinMode(adcPin, INPUT);
}

void setupWiFi() {
  WiFi.mode(WIFI_STA);
  WiFi.begin(SSID, PASS);
  Serial.printf("[WiFi]: Connecting to %s", SSID);
  while (WiFi.status() != WL_CONNECTED) {
    Serial.printf(".");
    delay(250);
  }
  Serial.printf("connected\r\n");
}

void setup() {
  Serial.begin(115200);
  Serial.setTimeout(2000);
  while(!Serial) { }
  
  reportState(); // Report the status to server
  
  ESP.deepSleep(SLEEP_DELAY_IN_SECONDS * 1000000); // Goto sleep.
}

// Connect to Sinric Pro server synchronously.
void waitForSinricProConnect() {
  SinricPro.begin(APP_KEY, APP_SECRET);  
  
  while (SinricPro.isConnected() == false) { // wait for connect
    SinricPro.handle();
    yield();
  }
  delay(100);
  Serial.printf("waitForSinricProConnect(): Connected to SinricPro ..\n"); 
}

// Disconnect from Sinric Pro server.
void stopSinricPro() {
  SinricPro.handle(); 
  SinricPro.stop();
  
  while (SinricPro.isConnected()) { // wait for disconnect
    SinricPro.handle();
    yield();
  }
}

void stopWiFi(){
  WiFi.disconnect();
}

void reportState() {
  setupSensor();
  setupWiFi();
  waitForSinricProConnect();
  sendSoilMoisture();
  stopSinricPro();
  stopWiFi();
}
 
void loop() {
  
}

``` 

### Troubleshooting
1. Please refer to our [Troubleshooting](https://help.sinric.pro/pages/troubleshooting.html) page for more details.
 
> This document is open source. See a typo? Please create an [issue](https://github.com/sinricpro/help-docs)
