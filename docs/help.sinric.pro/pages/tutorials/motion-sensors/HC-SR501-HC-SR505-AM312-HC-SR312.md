---
title: Motion Sensor Tutorial for HC-SR501, HC-SR505, Mini AM312, HC-SR312 
layout: post
---

In this section we’ll walk through creating a PIR **Motion Sensor** using **ESP32**, **ESP8266** or **Raspberry Pi Pico W** and then view the motion changes via **Alexa or SmartThings**.

### Prerequisites : 
1. ESP32, ESP8266 or Raspberry Pi Pico W x 1.
2. HC-SR501, HC-SR505, Mini AM312, HC-SR312 x 1.
3. Jumper Wires.

| PIR       | Working voltage  | Delay time | Blocking time | Trigger       | Distance
| --------- | -------          |-------     |-------        |-------        |-------
| HC-SR312  |2.7-12V           | 2 seconds  | 2 seconds     | Repeatable    | 3-5 meters
| HC-SR505  |4.5-20V           | 2 seconds  | 2 seconds     | Repeatable    | 3 meters
| HC-SR501  |4.5-20V           | Adjustable  | Adjustable    | Adjustable    | 3 meters to 7 meters

### Quick introduction to PIR Motion Sensors
Passive infrared (PIR) motion sensors detect the presence of people or animals by measuring changes in infrared radiation. PIR sensors work by detecting the infrared radiation emitted by all objects that have a temperature above absolute zero. When a person or animal moves into the field of view of a PIR sensor, the sensor detects a change in the amount of infrared radiation and triggers an alarm or other output. PIR sensors are a versatile and cost-effective way to detect motion. They are easy to use and can be installed in a variety of locations.

The output of the PIR Motion Sensor is:

- ``HIGH`` when a movement is detected.
- ``LOW``  when no movement is detected.

To get accurate measurements, **wait for the PIR sensor to calibrate properly, this will normally take from 10 to 60 seconds** after turning it on.  
 
The HC-SR501 has adjustable configurations:

![Sinric Pro esp8266 HC-SR501 ](https://help.sinric.pro/public/img/sinric_pro_pir_sensor.png) 

###### Sensitivity Adjustment
- Measuring distance is between 3 and 7 meters.

- Turning clockwise or right - Decreases the sensivity. Fully right upto 3 meters.

- Counter Turning clockwise or left - Decreases the sensivity. Fully left about 7 meters.

###### Time-Delay Adjustment
- Delay time that defines how long the output of the HC-SR501 stays ``HIGH`` after a motion is detected. It can be adjusted from 1 second to about 5 minutes.

- Turning clockwise or right - Increase the delay. Fully right upto 5 mins.

- Counter Turning clockwise or left - Decreases the delay. Fully left about 3 seconds.

###### Jumper Set
- **Single Trigger Mode (L)**: Triggers a single motion. The time delay potentiometer determines how long the pin will stay ``HIGH``. Any further motion detection is blocked until turns to ``LOW``.

**Example**: The motion detector's time delay is set to 3 seconds, but it cannot detect motion for about 6 seconds after detecting motion.

![Sinric Pro esp8266 PIR Single Trigger Mode](https://help.sinric.pro/public/img/sinric_pro_motion_pir_single_trigger.png) 

- **Multiple Trigger Mode (H)**: Triggers series of motions. The time delay potentiometer determines how long the pin will stay ``HIGH``. In multiple trigger mode, the time delay is reset each time motion is detected, so there is no blocking of further detection.
 
**Example**: The time delay is 3 seconds. After motion is detected, the time delay period restarts. However, detection is still blocked for 3 seconds after the time delay expires. This 3-second delay allows the sensor to rest before starting to detect motion again..

![Sinric Pro esp8266 PIR Multiple Trigger Mode](https://help.sinric.pro/public/img/sinric_pro_motion_pir_multi_trigger.png) 

### Wiring
![Sinric Pro esp8266 PIR wiring](https://help.sinric.pro/public/img/sinric_pro_pir_sensor_wiring.png) 

| MCU       | PIR Pin     |
| --------- | ------- |
| ESP32     |    16   |
| ESP8266   |    5 (D1)    |
| Pico W    |    GP5  |

Let's verify that motion sensor is wired correctly and working. 

```cpp
#if defined(ESP8266)
  #define SENSOR_PIN        D1
#elif defined(ESP32) 
  #define SENSOR_PIN        26
#elif (ARDUINO_ARCH_RP2040)
  #define SENSOR_PIN        5
#endif

int newState;
int oldState;

void setup() {
  Serial.begin(9600);
  pinMode(SENSOR_PIN, INPUT);
  
  Serial.printf("Wait 10 secs for the PIR sensor to calibrate properly\r\n");
  delay(1000 * 10);
  oldState = digitalRead(SENSOR_PIN); // read the starting state
}

void loop() {
  newState = digitalRead(SENSOR_PIN); // read current state

  if(oldState != newState) {
    if (newState == HIGH) {
      Serial.println("Movement detected!");
    } else {
      Serial.println("Movement not detected!");
    }
    oldState = newState;
  }  
   delay(100);
}

```

Arduino IDE Serial Monitor will show the motion detections like this:

[Video: https://help.sinric.pro/public/video/sinricpro-motion-sensor-demo.mp4](https://help.sinric.pro/public/video/sinricpro-motion-sensor-demo.mp4)

 
### Step 1 : Create a new device in Sinric Pro
* [Login](http://portal.sinric.pro) to your Sinric Pro account, go to **Devices** menu on your left and click **Add Device** button (On top left).
* Enter the device name **Motion Sensor**, description **My Motion Sensor** and select the device type as **Motion Sensor**.

![Sinric Pro create device alexa](https://help.sinric.pro/public/img/sinric_pro_contact_motion_new_device.png)

* Click **Next** the in the Notifications tab

![Sinric Pro motion sensor device notifications](https://help.sinric.pro/public/img/sinric_pro_motion_sensor_notifications.png)

You can set the threshold here to receive a push notification via the Sinric Pro app when the motion is detected at **Daytime** or **Nighttime**. Use the Retrigger Time to set the delay between push notifications.

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
// Uncomment the following line to enable serial debug output
//#define ENABLE_DEBUG

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

#include "SinricPro.h"
#include "SinricProMotionsensor.h" 

#define WIFI_SSID         ""   // Your WiFI SSID name 
#define WIFI_PASS         ""   // Your WiFi Password.
#define APP_KEY           ""   // Should look like "de0bxxxx-1x3x-4x3x-ax2x-5dabxxxxxxxx" (Get it from Portal -> Secrets)
#define APP_SECRET        ""   // Should look like "5f36xxxx-x3x7-4x3x-xexe-e86724a9xxxx-4c4axxxx-3x3x-x5xe-x9x3-333d65xxxxxx" (Get it from Portal -> Secrets)
#define MOTION_SENSOR_ID  ""   // Should look like "5dc1564130xxxxxxxxxxxxxx" (Get it from Portal -> Devices)
#define BAUD_RATE         115200              // Change baudrate to your need (used for serial monitor)
 
#if defined(ESP8266)
  #define SENSOR_PIN        D1
#elif defined(ESP32) 
  #define SENSOR_PIN        26
#elif (ARDUINO_ARCH_RP2040)
  #define SENSOR_PIN        5
#endif 

int newState;
int oldState;

void handleMotionSensor() {
  newState = digitalRead(SENSOR_PIN); // read current state

  if(oldState != newState) {
    if (newState == HIGH) {
      Serial.println("Movement detected!");
    } else {
      Serial.println("Movement not detected!");
    }

    // newState == true (HIGH) : if motion has been detected 
    // newState == false (LOW): if no motion has been detected

    SinricProMotionsensor &myMotionsensor = SinricPro[MOTION_SENSOR_ID]; // get motion sensor device
    myMotionsensor.sendMotionEvent(newState);

    oldState = newState;
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
 
// setup function for SinricPro
void setupSinricPro() {
  // add device to SinricPro
  SinricProMotionsensor &mySensor = SinricPro[MOTION_SENSOR_ID];
   
  // setup SinricPro
  SinricPro.onConnected([](){ Serial.printf("Connected to SinricPro\r\n"); }); 
  SinricPro.onDisconnected([](){ Serial.printf("Disconnected from SinricPro\r\n"); });
  //SinricPro.restoreDeviceStates(true); // Uncomment to restore the last known state from the server.
  SinricPro.begin(APP_KEY, APP_SECRET);  
}

void setupMotionSensor() {
  pinMode(SENSOR_PIN, INPUT);
  Serial.printf("Wait 10 secs for the PIR sensor to calibrate properly\r\n");
  delay(1000 * 10);  
}

// main setup function
void setup() {
  Serial.begin(BAUD_RATE); Serial.printf("\r\n\r\n");
  
  setupMotionSensor();
  setupWiFi();
  setupSinricPro();
}

void loop() {
  SinricPro.handle();
  handleMotionSensor();
}

```

 
Now you should be able to view the motions via Alexa, SmartThings 
  
![Sinric Pro motions via Alexa, SmartThings](https://help.sinric.pro/public/img/sinric_pro_contact_motion_home_alexa_smartthings.png)

via Portal

![Sinric Pro Portal Temperature Sensor](https://help.sinric.pro/public/img/sinric_pro_contact_motion_portal.png)

Please note that Google Home not supported.

### Troubleshooting
1. Please refer to our [Troubleshooting](https://help.sinric.pro/pages/troubleshooting.html) page for more details.

 
> This document is open source. See a typo? Please create an [issue](https://github.com/sinricpro/help-docs)
