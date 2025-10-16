---
title: Wally Tutorial
layout: post
---

In this section we’ll walk through creating a product we would like to sell in the future called **Wally**.  It's a 2 Gang 1/2 Way Wireless Wi-Fi Mini Smart Switch Module.

Please note that **only Espressif ESP32, ESP32C3, ESP32S3 are supported**

![Sinric Pro sinricpro wally](https://help.sinric.pro/public/img/sinricpro-wally.png)

#### Setup Product Profile
1. Login to your [Business Account](https://biz-portal.sinric.pro/)

2. Goto *Products* menu and click *Add Product*.

![Sinric Pro sinricpro business acccount add product wally](https://help.sinric.pro/public/img/sinricpro-biz-acccount-add-product.png)

And enter details about your product.

![Sinric Pro sinricpro business acccount enter product details](https://help.sinric.pro/public/img/sinricpro-biz-acccount-enter-product-details.png)

Then goto *Features* tab.

![Sinric Pro sinricpro business acccount enter product features](https://help.sinric.pro/public/img/sinricpro-biz-acccount-enter-product-features.png)

**Device Type** : Sinric Pro device type. Could be a Switch, Fan ect. Same as Sinric Pro portal. 

**Identifier** : We use identifier to refer to this device in the Arduino sketch. 

**Default Device Name** : You can set a default device name for the device. This is the default name shown during the device setup.

**Default Device Description** : You can set a default device description for the device. This is the default name shown during the device setup.

**Display Index** : Order which will be shown durnig the device setup.

![Sinric Pro sinricpro business acccount enter product details](https://help.sinric.pro/public/img/sinricpro-biz-acccount-setup-default-values-example.png)

Click *Save* and click on **Download** to download the product provisioing code.

![Sinric Pro sinricpro business acccount code download](https://help.sinric.pro/public/img/sinricpro-biz-acccount-setup-code-download.png)

The downloaded example comes pre-loaded with essential features including easy setup, multi-WiFi support, OTA updates, and health checks, allowing you to focus on bringing your unique IoT ideas to life without needing to implement these fundamental functionalities from scratch.

Search for **sinricprobusinesssdk** in the Arduino Library Manager to install the SinricPro Business SDK. 

![Sinric Pro Business SDK arduino IDE](https://help.sinric.pro/public/img/sinricpro-business-sdk.png)

This would also install *ArduinoJson (>=7.0.3), WebSockets (>=2.6.1), NimBLE-Arduino (>=1.4.2), SinricPro (>=3.2.1)* as well. Customize the GPIO mappings to fit your specific device configuration, compile and flash the firmware to your hardware.

Next, please change the Arduino IDE Tools -> Partition Scheme -> Minimal SPIFF (1.9MB APP ...). 

![Sinric Pro sinricpro wally arduino IDE tools settings](https://help.sinric.pro/public/img/arduino-ide-settings.png)

Now we are ready to flash the code.

![Sinric Pro sinricpro business acccount code download](https://help.sinric.pro/public/img/sinricpro-biz-acccount-setup-code-arduino-ide.png)

You can find the complete example code here:

[https://github.com/sinricpro/esp32-business-sdk/tree/master/examples/Wally](https://github.com/sinricpro/esp32-business-sdk/tree/master/examples/Wally)

#### Now you can setup product via Sinric Pro app
1. Download the Sinric Pro app from Google Play or AppStore.

2. Create an account and login.

3. Click *Add* and complete the setup process. 

Here's the product provisioning demo:

[Video: https://help.sinric.pro/public/video/prov.mp4](https://help.sinric.pro/public/video/prov.mp4)
