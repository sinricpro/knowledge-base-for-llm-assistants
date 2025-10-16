---
title: Dimmable Switch Tutorial using YYAC-3S for ESP32
layout: post
---

In this section we’ll walk through creating a **Dimmable Switch** using **ESP32** and then control the light brightness via **Amazon Alexa, GoogleHome or SmartThings**.

### Prerequisites : 
1. ESP32.
2. YYAC-3S.
3. Dimmable Light Bulb. 
3. Jumper Wires.

**Not all the light bulbs are dimmable.** Please check the manufacturer's label to see if the bulb is dimmable.

### Quick introduction to YYAC-3S
YYAC-3S module can used to control the supply of AC electricity using PWM from an Arduino, ESP8266, ESP32 or Pico W. It can be used to control the speed of AC motors and the brightness of dimmable light bulbs. The circuit consists of an SCR and an optocoupler on the input side. This prevents current from flowing back to the MCU and also separates it from ground, making it safe to use. The module supports a PWM input of 0-255 and can control AC voltage up to 220V with a maximum current of 3A.

### Wiring
  ![High Voltage Connectors](https://help.sinric.pro/public/img/sinric_pro_motion_dimmer_switch_wiring.png) 

Connect the YYAC-3S module to the microcontroller as follows:
- VCC pin to the microcontroller's 5V pin (3.3V does not seems to work).
- GND pin to the ESP32's GND pin.
- PWM pin to a digital pin 16 on the microcontroller.

| MCU       | YYAC-3S PWM Pin     |
| --------- | ------- |
| ESP32     |    16   |

Let's verify that YYAC-3S module is wired correctly and working. 

```cpp
const int pwmPin = 16;  // 16 corresponds to GPIO16 of ESP32

// setting PWM properties
const int freq = 1000; // 1KHz
const int ledChannel = 0;
const int resolution = 8;
 
void setup(){
  ledcSetup(ledChannel, freq, resolution);
  ledcAttachPin(pwmPin, ledChannel);
}
 
void loop(){
  
   /* dutyCycle between 0 to 115 does not seems to have an any effect on the bulb. */

  // increase the brightness. 
  for(int dutyCycle = 115; dutyCycle <= 255; dutyCycle++){   
    ledcWrite(ledChannel, dutyCycle);
    delay(15);
  }

  // decrease the brightness
  for(int dutyCycle = 255; dutyCycle >= 115; dutyCycle--){
    ledcWrite(ledChannel, dutyCycle);   
    delay(15);
  } 
}

```

Arduino IDE Serial Monitor will show light bulb dimming like this:

[Video: https://help.sinric.pro/public/video/sinricpro-swith-with-dimmer-YYAC3S.mp4](https://help.sinric.pro/public/video/sinricpro-swith-with-dimmer-YYAC3S.mp4)

### Step 1 : Create a new device in Sinric Pro
* [Login](http://portal.sinric.pro) to your Sinric Pro account, go to **Devices** menu on your left and click **Add Device** button (On top left).
* Enter the device name **Dimmer**, description **Dimmer** and select the device type as **Switch With Dimming**.

![Sinric Pro create device alexa](https://help.sinric.pro/public/img/sinric_pro_contact_dimmer_new_device.png)

* Click Others tab and Click **Save**

* Next screen will show the credentials required to connect the device you just created.

![Sinric Pro copy device id](https://help.sinric.pro/public/img/sinric-pro-create-device-keys.png)

* Copy the **Device Id**, **App Key** and **App Secret** ***Keep these values secure. DO NOT SHARE THEM ON PUBLIC FORUMS !***

### Step 2 : Connect to Sinric Pro 
#### Step 2.1 Install Sinric Pro Library
![Sinric Pro install SinricPro library](https://help.sinric.pro/public/img/sinricpro_arduinoIDE-library-manager.png)

#### 2.2 Complete Code

```cpp
//Uncomment the following line to enable serial debug output
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
#include "SinricProDimSwitch.h"

#define WIFI_SSID         ""    
#define WIFI_PASS         ""
#define APP_KEY           ""   // Should look like "de0bxxxx-1x3x-4x3x-ax2x-5dabxxxxxxxx"
#define APP_SECRET        ""   // Should look like "5f36xxxx-x3x7-4x3x-xexe-e86724a9xxxx-4c4axxxx-3x3x-x5xe-x9x3-333d65xxxxxx"
#define DIMSWITCH_ID      ""   // Should look like "5dc1564130xxxxxxxxxxxxxx"
#define BAUD_RATE         9600                // Change baudrate to your need

// we use a struct to store all states and values for our dimmable switch
struct {
  bool powerState = false;
  int powerLevel = 0;
} device_state;

// the PWM Pin
const int pwmPin = 16;  // 16 corresponds to GPIO16

// setting PWM properties
const int freq = 1000; // 1KHz
const int ledChannel = 0;
const int resolution = 8;
 
void setPWM(int powerLevel) {
   int dutyCycle = map(powerLevel, 0, 100, 115, 255); // Map power level from 0 to 100, to a value between 115 to 255
   ledcWrite(ledChannel, dutyCycle);
   delay(30);
}

bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Device %s power turned %s \r\n", deviceId.c_str(), state?"on":"off");
  device_state.powerState = state;  
  setPWM(state ? 100: 0);
  return true; // request handled properly
}

bool onPowerLevel(const String &deviceId, int &powerLevel) {
  device_state.powerLevel = powerLevel; 
  setPWM(powerLevel);
  Serial.printf("Device %s power level changed to %d\r\n", deviceId.c_str(), device_state.powerLevel);
  return true;
}

bool onAdjustPowerLevel(const String &deviceId, int &levelDelta) {
  device_state.powerLevel += levelDelta;
  Serial.printf("Device %s power level changed about %i to %d\r\n", deviceId.c_str(), levelDelta, device_state.powerLevel);
  levelDelta = device_state.powerLevel;
  setPWM(levelDelta);
  return true;
}

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

void setupSinricPro() {
  SinricProDimSwitch &myDimSwitch = SinricPro[DIMSWITCH_ID];

  // set callback function to device
  myDimSwitch.onPowerState(onPowerState);
  myDimSwitch.onPowerLevel(onPowerLevel);
  myDimSwitch.onAdjustPowerLevel(onAdjustPowerLevel);

  // setup SinricPro
  SinricPro.onConnected([](){ Serial.printf("Connected to SinricPro\r\n"); }); 
  SinricPro.onDisconnected([](){ Serial.printf("Disconnected from SinricPro\r\n"); });
  SinricPro.begin(APP_KEY, APP_SECRET);
}

void setupYYAC3S(){
  ledcSetup(ledChannel, freq, resolution);
  ledcAttachPin(pwmPin, ledChannel);
} 

// main setup function
void setup() {
  Serial.begin(BAUD_RATE); Serial.printf("\r\n\r\n");
  setupYYAC3S();
  setupWiFi();
  setupSinricPro();
}

void loop() {
  SinricPro.handle();
}

```

 
Now you should be able to control the the brightness level via Alexa, Google Home and SmartThings apps
  
[Video: https://help.sinric.pro/public/video/sinricpro-swith-with-dimmer-google-home-demo.mp4](https://help.sinric.pro/public/video/sinricpro-swith-with-dimmer-google-home-demo.mp4)

![Sinric Pro dimmer with Alexa, SmartThings](https://help.sinric.pro/public/img/sinric_pro_switch_with_dimmer_alexa_google_home_smartthings.png)

### Troubleshooting
- Does not dim ? Please check the manufacturer’s label to see if the light bulb is dimmable.

![dimmable light bulb example](https://help.sinric.pro/public/img/sinric_pro_switch_with_dimmer_dimmable_example.png) 

- PWM not working ?

![Sinric Pro dimmer YYAC-3S via Alexa, SmartThings](https://help.sinric.pro/public/img/sinric_pro_switch_with_dimmer_pwm_YYAC-3S.png)

Above LED lights up when the module recevice the PWM signal from MCU. 

- Please refer to our [Troubleshooting](https://help.sinric.pro/pages/troubleshooting.html) page for more details.

 
> This document is open source. See a typo? Please create an [issue](https://github.com/sinricpro/help-docs)
