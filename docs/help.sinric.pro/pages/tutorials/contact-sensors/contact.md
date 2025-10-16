---
title: Contact Sensor (MC-38) Tutorial for ESP8266, ESP32, Raspberry Pi Pico W
layout: post
---
 
In this section, we will show you how to create a contact sensor (also known as a door sensor, entry sensor, or window sensor) using an **ESP32**, **ESP8266** or **Raspberry Pi Pico W**, and then view the open/close state in Alexa, Google Home, SmartThings, or the Sinric Pro app.

![Sinric Pro contact sensor tutorial](https://help.sinric.pro/public/img/sinric_pro_contact_sensor_bg.jpg) 

### Prerequisites : 
1. ESP32, ESP8266 or Raspberry Pi Pico W x 1.
2. Contact Sensor (MC-38) x 1.
3. Jumper Wires.

### Quick introduction to Contact Sensor
A contact sensor is a device that detects whether a door, window, or other object is open or closed. A contact sensor comprises two components – a sensor and a magnet – used to determine the status of objects such as doors or windows, whether they are open or closed. 
 
![Sinric Pro Contact Sensor Reed Switch Open Close](https://help.sinric.pro/public/img/sinricpro_pi_contact_sensor_open_close.png) 

The sensor is affixed to the frame of the sliding window, while the magnet is attached to the window itself. When the sliding window is shut, the magnet comes into proximity with the sensor, establishing a connection between the two components. Conversely, when the sliding window is opened, the magnet moves away from the sensor, breaking the connection between them.

### Wiring Contact Sensor
![Sinric Pro contact sensor wiring](https://help.sinric.pro/public/img/sinricpro_pi_contact_sensor_wiring.png) 

Note: We are using INPUT_PULLUP instead of connecting a 10kΩ resistor between the reed switch and GND.

| MCU       | GPIO Pin     |
| --------- | ------- |
| ESP32     |    19   |
| ESP8266   |    4 (D2)  |
| Pico W    |    GP5  |

Let's verify that contact sensor is wired correctly and working. 

 

```cpp
#if defined(ESP8266)
  #define SENSOR_PIN D2
#elif defined(ESP32)  
  #define SENSOR_PIN 26
#elif (ARDUINO_ARCH_RP2040)
  #define SENSOR_PIN 5
#endif

int newState;
int oldState;

void setup() {
  Serial.begin(9600);
  pinMode(SENSOR_PIN, INPUT_PULLUP);
  oldState = digitalRead(SENSOR_PIN); // read the starting state
}

void loop() {
  newState = digitalRead(SENSOR_PIN); // read current state
  if(oldState != newState) {
    if (newState == HIGH) {
      Serial.println("Sliding window is open");
    } else {
      Serial.println("Sliding window is closed");
    }
    oldState = newState;
  }
}

```

[Video: https://help.sinric.pro/public/video/sinricpro-contact-sensor-open-close.mp4](https://help.sinric.pro/public/video/sinricpro-contact-sensor-open-close.mp4)

### Step 1 : Create a new Contact Sensor in Sinric Pro
* [Login](http://portal.sinric.pro) to your Sinric Pro account, go to **Devices** menu on your left and click **Add Device** button (On top left).
* Enter the device name **Front Door**, description **My Front Door** and select the device type as **Contact Sensor**.

![Sinric Pro create device alexa](https://help.sinric.pro/public/img/sinric_pro_contact_sensor_new_device.png)

* Click **Next** the in the Notifications tab

![Sinric Pro temperature sensor device notifications](https://help.sinric.pro/public/img/sinric_pro_contact_sensor_notifications.png)

You can enable toggles for Open, Close here to receive a push notification via the Sinric Pro app when the contact sensor state change between **Open** or **Close**.

* Click Others tab and Click **Save**

* Next screen will show the credentials required to connect the device you just created.

![Sinric Pro copy device id](https://help.sinric.pro/public/img/sinric-pro-create-device-keys.png)

* Copy the **Device Id**, **App Key** and **App Secret** ***Keep these values secure. DO NOT SHARE THEM ON PUBLIC FORUMS !***

### Step 2 : Connect to Sinric Pro 
#### Step 2.1 Install Sinric Pro Library
![Sinric Pro install SinricPro library](https://help.sinric.pro/public/img/sinricpro_arduinoIDE-library-manager.png)
 
You can **generate the code** using **Zero Code** feature or write it by your self. If you do not have programming experice, we recommend to use Zero Code feature in the Portal to generate the code, download and flash.

#### 2.2 Complete Code

```cpp
#ifdef ENABLE_DEBUG
  #define DEBUG_ESP_PORT Serial
  #define NODEBUG_WEBSOCKETS
  #define NDEBUG
#endif  

#include <Arduino.h>
#if defined(ESP8266)
  #include <ESP8266WiFi.h>
#elif defined(ESP32) || defined(ARDUINO_ARCH_RP2040)
  #include <WiFi.h>
#endif

#if defined(ESP8266)
  #define SENSOR_PIN D2
#elif defined(ESP32)  
  #define SENSOR_PIN 26
#elif (ARDUINO_ARCH_RP2040)
  #define SENSOR_PIN 5
#endif

#include "SinricPro.h"
#include "SinricProContactsensor.h"

#define WIFI_SSID "" // Your WiFI SSID name  
#define WIFI_PASS "" // Your WiFi Password.
#define APP_KEY "" // Should look like "de0bxxxx-1x3x-4x3x-ax2x-5dabxxxxxxxx" (Get it from Portal -> Secrets)
#define APP_SECRET "" // Should look like "5f36xxxx-x3x7-4x3x-xexe-e86724a9xxxx-4c4axxxx-3x3x-x5xe-x9x3-333d65xxxxxx" (Get it from Portal -> Secrets)
#define CONTACT_ID "" // Should look like "5dc1564130xxxxxxxxxxxxxx" (Get it from Portal -> Devices)
#define BAUD_RATE 115200 // Change baudrate to your need (used for serial monitor)

int actualContactState;
int lastContactState;

void handleContactSensor() {
  actualContactState = digitalRead(SENSOR_PIN); // read current state
  // If inverted change to !digitalRead(SENSOR_PIN)
  if (actualContactState != lastContactState) { // if state has changed
    Serial.printf("Contact Sensor is %s now\r\n", actualContactState ? "open":"closed");
    lastContactState = actualContactState; // update last known state
    SinricProContactsensor &myContact = SinricPro[CONTACT_ID]; // get contact sensor device
    // actualContactState == true then contact is closed  
    // actualContactState == false then contact is open  
    myContact.sendContactEvent(!actualContactState); // send event with actual state
  }
}

// setup function for WiFi connection
void setupWiFi() {
  Serial.printf("\r\n[Wifi]: Connecting");
#if defined(ESP8266)
  WiFi.setSleepMode(WIFI_NONE_SLEEP);  
  WiFi.setAutoReconnect(true);
#elif defined(ESP32)
  WiFi.setSleep(false);  
  WiFi.setAutoReconnect(true);
#endif
  WiFi.begin(WIFI_SSID, WIFI_PASS);  
  while (WiFi.status() != WL_CONNECTED) {
    Serial.printf(".");
    delay(250);
  }
  IPAddress localIP = WiFi.localIP();
  Serial.printf("connected!\r\n[WiFi]: IP-Address is %d.%d.%d.%d\r\n", localIP[0], localIP[1], localIP[2], localIP[3]);
}

```

 
Now you should be able to see the contact state via Alexa, Google Home, SmartThings and Sinric Pro App

![Sinric Pro Alexa Contact Sensor](https://help.sinric.pro/public/img/sinric_pro_contact_sensor_google_home_alexa_smartthings.jpg)
 

### Troubleshooting
1. Please refer to our [Troubleshooting](https://help.sinric.pro/pages/troubleshooting.html) page for more details.
 
> This document is open source. See a typo? Please create an [issue](https://github.com/sinricpro/help-docs)
