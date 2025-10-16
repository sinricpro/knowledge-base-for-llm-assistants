---
title: Temperature Sensor Tutorial for LM35 (LM35DZ), LM335 and LM34
layout: post
---

In this section we’ll walk through creating a **Temperature Sensor** using **ESP32**, **ESP8266** and then view the temperature via **Alexa, Google Home or SmartThings**.

### Prerequisites : 
1. ESP32, ESP8266 x 1.
2. LM35, LM335 and LM34 x 1.
3. Jumper Wires.

### Quick introduction to Temperature Sensor
The LM35/LM34 temperature sensors are a linear integrated circuit temperature sensor that works by measuring the voltage drop between the base and emitter of a diode-connected transistor. The voltage drop between the base and emitter of a diode-connected transistor decreases at a known rate as the temperature increases.  

- LM35 provides temperature measurements in Celsius (°C)

- LM32 provides temperature measurements in Fahrenheit (ºF). 

- LM335 provides temperature measurements in Kelvin (ºK)

### Wiring for LM35 or LM34 with ESP8266
![Sinric Pro LM35 or LM34 wiring](https://help.sinric.pro/public/img/sinricpro_temperature_sensor_LM35-and-LM34_Wiring.png) 

### Wiring for LM335 with ESP8266
![Sinric Pro LM335 wiring](https://help.sinric.pro/public/img/sinricpro_temperature_sensor_LM335_Wiring.png) 

**Pull-up via 2.2k Ohm resistor** 

| MCU       | GPIO Pin     |
| --------- | ------- |
| ESP32     |    36 (ADC0)  |
| ESP8266   |    A0 (ADC0)  |

Let's verify that temperature is wired correctly and working. 

```cpp
#if defined(ESP8266)
  #define LM_PIN    A0
#elif defined(ESP32)
  #define LM_PIN    36
#endif

#define ADC_VREF_mV    5000.0 // 5000 is the voltage provided by MCU. If you connect to 3V change to 3000
#define ADC_RESOLUTION 4096.0

void setup() {
  Serial.begin(9600);
}

float getTemperature() {
  #if defined(ESP8266)
    int analogValue = analogRead(LM_PIN);
    float millivolts = (analogValue / 1024.0) * ADC_VREF_mV; 
    float temperature = millivolts / 10;
    // float fahrenheit = ((temperature * 9) / 5 + 32);
    return temperature;
  #elif defined(ESP32)
    int adcVal = analogRead(LM_PIN);
    float milliVolt = adcVal * (ADC_VREF_mV / ADC_RESOLUTION);
    float temperature = milliVolt / 10;
    return temperature;
  #endif
}

void loop()
{
  float temperature = getTemperature();
  Serial.printf("Temperature: %2.1f °C\r\n", temperature);
  delay(1000);
}

```

 
### Step 1 : Create a new device in Sinric Pro
* [Login](http://portal.sinric.pro) to your Sinric Pro account, go to **Devices** menu on your left and click **Add Device** button (On top left).
* Enter the device name **Temperature Sensor**, description **My Temperature Sensor** and select the device type as **Temperature Sensor**.

![Sinric Pro create device alexa](https://help.sinric.pro/public/img/sinric_pro_temperature_sensor_dht_tutorial_portal_new_device.png)

* Click **Next** the in the Notifications tab

![Sinric Pro temperature sensor device notifications](https://help.sinric.pro/public/img/sinric_pro_temperature_sensor_tutorial_device_notifications.png)

You can set the threshold here to receive a push notification via the Sinric Pro app when the temperature goes **below** or **above** a certain temperature. Use the Retrigger Time to set the delay between notifications.

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
#elif defined(ESP32)
  #include <WiFi.h>
#endif

#if defined(ESP8266)
  #define LM_PIN    A0
#elif defined(ESP32)
  #define LM_PIN    36
#endif

#include "SinricPro.h"
#include "SinricProTemperaturesensor.h"

#define WIFI_SSID         ""   // Your WiFI SSID name 
#define WIFI_PASS         ""   // Your WiFi Password.
#define APP_KEY           ""   // Should look like "de0bxxxx-1x3x-4x3x-ax2x-5dabxxxxxxxx" (Get it from Portal -> Secrets)
#define APP_SECRET        ""   // Should look like "5f36xxxx-x3x7-4x3x-xexe-e86724a9xxxx-4c4axxxx-3x3x-x5xe-x9x3-333d65xxxxxx" (Get it from Portal -> Secrets)
#define TEMP_SENSOR_ID    ""   // Should look like "5dc1564130xxxxxxxxxxxxxx" (Get it from Portal -> Devices)
#define BAUD_RATE         115200              // Change baudrate to your need (used for serial monitor)
#define EVENT_WAIT_TIME   60000               // send event every 60 seconds

float temperature;                            // actual temperature
float humidity;                               // actual humidity
float lastTemperature;                        // last known temperature (for compare)

#define ADC_VREF_mV    5000.0 // 5000 is the voltage provided by MCU. If you connect to 3V change to 3000
#define ADC_RESOLUTION 4096.0

float getTemperature() {
  #if defined(ESP8266)
    int analogValue = analogRead(LM_PIN);
    float millivolts = (analogValue / 1024.0) * ADC_VREF_mV; 
    float temperature = millivolts / 10;
    // float fahrenheit = ((temperature * 9) / 5 + 32);
    return temperature;
  #elif defined(ESP32)
    int adcVal = analogRead(LM_PIN);
    float milliVolt = adcVal * (ADC_VREF_mV / ADC_RESOLUTION);
    float temperature = milliVolt / 10;
    return temperature;
  #endif
}

void handleTemperaturesensor() {
  if (SinricPro.isConnected() == false) {
    Serial.printf("Not connected to Sinric Pro...!\r\n");
    return; 
  }

  static unsigned long last_millis;
  unsigned long        current_millis = millis();
  if (last_millis && current_millis - last_millis < EVENT_WAIT_TIME) return;
  last_millis = current_millis;
  
  float temperature = getTemperature();

  Serial.printf("Temperature: %2.1f °C\r\n", temperature);

  if (isnan(temperature)) { // reading failed... 
    Serial.printf("reading failed!\r\n");  // print error message
    return;                                    // try again next time
  } 

  if (temperature == lastTemperature) {
    Serial.printf("Temperature did not changed. do nothing...!\r\n");
    return; 
  }

  SinricProTemperaturesensor &mySensor = SinricPro[TEMP_SENSOR_ID];  // get temperaturesensor device
  bool success = mySensor.sendTemperatureEvent(temperature, -1); // send event
  if (success) {  
    Serial.printf("Sent!\r\n");
  } else {
    Serial.printf("Something went wrong...could not send Event to server!\r\n"); // Enable ENABLE_DEBUG to see why
  }

  lastTemperature = temperature;  // save actual temperature for next compare  
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
  SinricProTemperaturesensor &mySensor = SinricPro[TEMP_SENSOR_ID]; 
  
  // setup SinricPro
  SinricPro.onConnected([](){ Serial.printf("Connected to SinricPro\r\n"); }); 
  SinricPro.onDisconnected([](){ Serial.printf("Disconnected from SinricPro\r\n"); });
  //SinricPro.restoreDeviceStates(true); // Uncomment to restore the last known state from the server.
  SinricPro.begin(APP_KEY, APP_SECRET);  
}

// main setup function
void setup() {
  Serial.begin(BAUD_RATE); Serial.printf("\r\n\r\n");
   
  setupWiFi();
  setupSinricPro();
}

void loop() {
  SinricPro.handle();
  handleTemperaturesensor();
}

```

 
Now you should be able to view the temperature via Sinric Pro App
  
![Sinric Pro App Temperature Sensor](https://help.sinric.pro/public/img/sinric_pro_app_temperature_sensor.png)

Please note that Google Home App shows the temperature sensor as a Thermostat due to Google Home limitations.

### Troubleshooting
1. Please refer to our [Troubleshooting](https://help.sinric.pro/pages/troubleshooting.html) page for more details.
 
> This document is open source. See a typo? Please create an [issue](https://github.com/sinricpro/help-docs)
