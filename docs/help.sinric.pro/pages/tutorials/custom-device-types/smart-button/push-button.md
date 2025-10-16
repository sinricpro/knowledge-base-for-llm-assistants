---
title:  Sinric Pro Smart Button
layout: post
---

Want to control your DIY projects with just a tap on your phone? Sinric Pro's Smart Button feature is the perfect solution. Create a push buttons in the app that can trigger any action on your microcontroller – from switching on LEDs to controlling motors or sensors.
This guide will walk you through setting up Smart Button functionality on your ESP32, ESP8266, or Raspberry Pi Pico W. You'll learn how to handle all types of button interactions:

* Single press
* Double press
* Long press

### Prerequisites : 
1. ESP32, ESP8266 or Raspberry Pi Pico W x 1.

### Step 1 : Connect to Sinric Pro 
#### Step 1.1 : Create a custom device type for Smart Button.
Sinric Pro does not have a built-in device type for Push Button so we are going to create a custom device type for it.

**Note**: You can use the device template import feature mentioned below to skip creating the full template.

* [Login](https://portal.sinric.pro/devicetemplates/new) to your Sinric Pro account and goto **Device Templates**

* Click **Add Device Template**. Enter name and description as **SmartButton** and select **Smart Button** as device type

![Sinric Pro smart button template](https://help.sinric.pro/public/img/smart-button-set-template-name-desc-type.png) 

* Click **Capabilities** and drag a **Smart Button** capability.

![Sinric Pro custom device type for push button](https://help.sinric.pro/public/img/smart-button-set-template-drop-smart-button.png) 

Click on **Save** to save.

![Sinric Pro moisture saved template](https://help.sinric.pro/public/img/smart-button-saved-template.png)  

Now you can see the template we just created.

## Import an existing template?
If you are feeling lazy setup all the Modes and Range values, you can use the import feature.

![Sinric Pro capacitive soil moisture sensor import template](https://help.sinric.pro/public/img/capacitive-soil-moisture-sensor-import-template.png)

Paste this Template:

```c++
{
  "name": "SmartButton",
  "description": "SmartButton",
  "deviceTypeId": "672a3b2c96ec80395518f2fe",
  "capabilities": [
    {
      "id": "672a3fbd96ec8039551901eb"
    }
  ]
}

```

 

* [Login](http://portal.sinric.pro) to your Sinric Pro account, go to **Devices** menu on your left and click **Add Device** button (On top left).

* Enter the device name **SmartButton**, description **SmartButton** and select the device type as **SmartButton**.

![Sinric Pro create device alexa](https://help.sinric.pro/public/img/smart-button-save-device.png)

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
#ifndef _SMARTBUTTON_H_
#define _SMARTBUTTON_H_

#include <SinricProDevice.h>
#include <Capabilities/SmartButtonStateController.h>

class SmartButton 
: public SinricProDevice
, public SmartButtonStateController<SmartButton> {
  friend class SmartButtonStateController<SmartButton>;
public:
  SmartButton(const String &deviceId) : SinricProDevice(deviceId, "SmartButton") {};
};

#endif

```

![Sinric Pro smart button in portal](https://help.sinric.pro/public/img/smart-button-portal-dashboard.png)

![Sinric Pro smart button in app](https://help.sinric.pro/public/img/smart-button-app.png)

### Troubleshooting
1. Smart Buttons are only supported in SinricPro app. Alexa, Google Home or SmartThings are not supproted.

2. Please refer to our [Troubleshooting](https://help.sinric.pro/pages/troubleshooting.html) page for more details.
 
> This document is open source. See a typo? Please create an [issue](https://github.com/sinricpro/help-docs)
