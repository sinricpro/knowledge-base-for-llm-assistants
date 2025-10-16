---
title:  How to Build a Gas Sensor to detect alcohol, ethanol, smoke in the air using a MQ-3 using ESP32, ESP8266 or Raspberry Pi Pico W for Alexa
layout: post
---

In this section, we'll guide you through building an gas sensor using an **ESP32**, **Raspberry Pi Pico W** or **ESP8266**. You'll then be able to monitor the alcohol concentration in the air through **Amazon Alexa** and **receive push notifications**  via the Sinric Pro app when the sensor detects a certain level.

### Prerequisites : 
1. ESP32, ESP8266 x 1.
2. MQ-3 x 1.
3. Jumper Wires.

![Sinric Pro MQ135 air quality](https://help.sinric.pro/public/img/sinricpro-gas-sensor-intro.jpg)

### Quick introduction to MQ-3
The MQ-3 is a semiconductor gas sensor used in a variety of applications to detect alcohol concentrations in air. It is a low-cost, high-sensitivity sensor that is easy to use. The MQ-3 is based on the principle of tin dioxide (SnO2) semiconductor gas sensing. When alcohol molecules are present in the air, they are adsorbed onto the surface of the SnO2. This adsorption causes the conductivity of the SnO2 to change, which can be measured as an electrical signal. The MQ-3 is sensitive to a wide range of alcohol concentrations, from 0.05 ppm to 5000 ppm. It is also sensitive to other gases, such as ammonia, benzene, and toluene, but it is most sensitive to alcohol.

The MQ3 sensor requires a power supply of 5V and consumes about 800mW of current. The digital output can be connected to a ESP32, ESP8266 or Pico W to measure the alcohol concentration.

**How to calibrate the sensor ?**

Leave it powered on for 24-48 hours to burn it in and stabilize.

Specification sheet: [MQ-3](https://www.sparkfun.com/datasheets/Sensors/MQ-3.pdf)

### Wiring
![Sinric Pro esp8266 MQ-3 wiring](https://help.sinric.pro/public/img/sinricpro_mq3-wiring-diagram.png) 

| MCU       | A0 Pin                    |  VCC Pin  |
| --------- | -------                   | -------   |
| ESP32     |    34 (Analog ADC1_CH6)   |   5V      |
| ESP8266   |    A0                     |   5V/VIN  |
| Pico W    |    GP26 (ADC0)            |   5V      |
 

**Note:** on ESP32, ADC2 (GPIO04, GPIO02, GPIO15) is unstable when Wi-Fi is being used.

Let's verify that the sensor is wired correctly and working. 

```cpp
#if defined(ESP8266)
  const int adcPin = A0;  
#elif defined(ESP32) 
  const int adcPin = 34;  
#elif defined(ARDUINO_ARCH_RP2040)
  const int adcPin = 26;  
#endif

int sensorVal;

void setup() {
 Serial.begin(9600);
 Serial.println("warming up for 20 seconds ..!");
 delay(20000);
}

void loop() {
 sensorVal = analogRead(adcPin);
 Serial.printf("value: %d\r\n", sensorVal);
 delay(2000); // delay 2s
}

```

Reading when not exposed to alcohol (around 760):

![Sinric Pro MQ3 test reading](https://help.sinric.pro/public/img/sinricpro_mq3-test-reading.png)

When exposed to Jack Daniel's Single Barrel Tennessee Whiskey (around 850):

![Sinric Pro MQ3 test reading](https://help.sinric.pro/public/img/sinricpro_mq3-test-jack-daniels-reading.png)

 
### Step 1 : Connect to Sinric Pro 
#### Step 1.1 : Creating a custom device type for Gas Sensor.
Sinric Pro does not have a built-in device type for Gas Sensor so we are going to create a custom device type for Gas Sensor using Device Template feature.

1. Alcohol Detected/Not Detected. 

**Note**: You can use the device template import feature mentioned below to skip creating the full template.

* [Login](https://portal.sinric.pro/devicetemplates/new) to your Sinric Pro account and goto **Device Templates**

* Click **Add Device Template**. Enter name and description as **Gas Sensor** and select **Gas Sensor** as device type

![Sinric Pro gas ensor device template](https://help.sinric.pro/public/img/sinricpro-gas-sensor-device-template-basic-info.png) 

* Click **Capabilities**. 

Here we must select the features of our Gas Sensor. We want to know whether *Alcohol* is *Detected, Not-Detected*. So let's drag a **Mode** and **Push Notification** capability.

![Sinric Pro custom device type for gas sensor](https://help.sinric.pro/public/img/gas-sensor-set-template-drop-range-mode.png) 

- What's Mode capability?

  Mode capability can be use to represents a value out of list of predefined values. eg: current wash cycle mode of a washing machine.

- What's Push Notification capability?

  Notification capability provides the ability to send a push notification message from the code.

Click on **Configure** button and setup the two capabilities like below.

![Sinric Pro moisture sensor template mode and range settings](https://help.sinric.pro/public/img/sinricpro-gas-sensor-device-template-mode.png)  

Click on **Save** to save.

![Sinric Pro moisture sensor template mode and range settings](https://help.sinric.pro/public/img/sinricpro-gas-sensor-template-range-and-mode-configured.png)  

Click on **Save** to save the template.

Now you can see the template we just created.

## Import an existing template?
If you are feeling lazy setup all the Modes values, you can use the import feature.

![Sinric Pro gas sensor import template](https://help.sinric.pro/public/img/capacitive-soil-moisture-sensor-import-template.png)

Paste this Template:

```cpp
{
  "name": "Gas Sensor",
  "description": "Gas Sensor",
  "deviceTypeId": "654d7ef928f14e9ac82ad57d",
  "capabilities": [
    {
      "id": "5ff0b41b994fd31b7d5e8961",
      "mode": {
        "instanceId": "modeInstance1",
        "locale": "en-US",
        "modeName": "Alcohol",
        "nonControllable": true,
        "modeValues": [
          "Detected",
          "Not-Detected"
        ]
      }
    },
    {
      "id": "62a707bd9e2f39090569e2d3"
    }
  ]
}

```

* [Login](http://portal.sinric.pro) to your Sinric Pro account, go to **Devices** menu on your left and click **Add Device** button (On top left).

* Enter the device name **Gas Sensor**, description **Gas Sensor** and select the device type as **Gas Sensor** from **Your Device Templates**.

![Sinric Pro create device alexa](https://help.sinric.pro/public/img/sinricpro-gas-sensor-device-new-device-info.png)

* Click Others tab and Click **Save**

* Next screen will show the credentials required to connect the device you just created.

![Sinric Pro copy device id](https://help.sinric.pro/public/img/sinric-pro-create-custom-device-keys.png)

* Copy the **Device Id**, **App Key** and **App Secret** ***Keep these values secure. DO NOT SHARE THEM ON PUBLIC FORUMS !***

* Click on **Code Generator** to Generate the code. Click on **Download** to download the code.

![Sinric Pro copy device id](https://help.sinric.pro/public/img/sinric-pro-create-custom-device-code-download.png)
 

### Step 2 : Connect to Sinric Pro 
#### Step 2.1 Install Sinric Pro Library 
![Sinric Pro install SinricPro library](https://help.sinric.pro/public/img/sinricpro_arduinoIDE-library-manager.png)
 

#### 2.2 Complete Code

```cpp
#ifndef _GASSENSOR_H_
#define _GASSENSOR_H_

#include <SinricProDevice.h>
#include <Capabilities/ModeController.h>
#include <Capabilities/PushNotification.h>

class GasSensor 
: public SinricProDevice
, public ModeController<GasSensor>
, public PushNotification<GasSensor> {
  friend class ModeController<GasSensor>;
  friend class PushNotification<GasSensor>;
public:
  GasSensor(const String &deviceId) : SinricProDevice(deviceId, "GasSensor") {};
};

#endif

```

 
Now you should be able to see the alcholol detected status and water level via Alexa, Sinric Pro App

Alexa, What's the alcholol (mode name) in Gas Sensor (device name)
 

![Sinric Pro Alexa gas sensor](https://help.sinric.pro/public/img/sinricpro-gas-sensor-alexa.jpg)
 
![Sinric Pro Portal gas sensor portal](https://help.sinric.pro/public/img/sinricpro-gas-sensor-device-portal.png)
 

### Troubleshooting
1. Google Home or SmartThings are not supproted.

2. Please refer to our [Troubleshooting](https://help.sinric.pro/pages/troubleshooting.html) page for more details.
 
> This document is open source. See a typo? Please create an [issue](https://github.com/sinricpro/help-docs)
