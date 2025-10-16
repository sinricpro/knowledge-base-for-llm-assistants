---
title: How to use device templates (custom device types) in SinricPro
layout: post
---

## What are SinricPro Device Templates?

Device Templates in SinricPro are blueprints for creating your own custom device types. They allow you to define a new device by selecting and combining different "capabilities" that match the features of your physical IoT device.

---

## Available Capabilities

You can build a custom device template by combining the capabilities listed below.

| Capability | Description | Usage Limit |
| :--- | :--- | :--- |
| **Power** | Turn a device on or off. | Single Use |
| **Brightness** | Control the brightness level of a device like a light bulb. | Single Use |
| **Channel** | Change or increment channels on an entertainment device. | Single Use |
| **Color** | Change the color of a light. | Single Use |
| **Color Temperature** | Adjust the color temperature for tunable white lights. | Single Use |
| **Contact Sensor** | Detect if a contact sensor is open or closed. | Single Use |
| **Doorbell** | Trigger a doorbell press event. | Single Use |
| **Equalizer** | Adjust equalizer bands and sound modes. | Single Use |
| **Input Control** | Change the input source (e.g., HDMI 1, HDMI 2) on a device. | Single Use |
| **Lock** | Lock or unlock a lockable device. | Single Use |
| **Media Control** | Control playback (play, pause, stop, next, previous). | Single Use |
| **Mode** | Control specific mode settings (e.g., fan rotation, wash cycles,). | **Multiple Use** |
| **Motion Sensor** | Detect physical movement in an area. | Single Use |
| **Open/Close** | Control devices that open and close, like blinds or garage doors. | Single Use |
| **Percentage** | Control a setting that can be expressed as a percentage. | Single Use |
| **Power Level** | Control the power level of a device. | Single Use |
| **Push Notification** | Send a push notification to the Sinric Pro mobile app. | Single Use |
| **Range** | Control a setting within a numeric range (e.g., fan speed 1-5). | **Multiple Use** |
| **Setting** | Change a specific setting on the device. | **Multiple Use** |
| **Smart Button** | A simple push-button trigger from the Sinric Pro app. | Single Use |
| **Start/Stop** | Control devices that can start, stop, and optionally pause. | Single Use |
| **Temperature Sensor** | Reports the current temperature from a sensor. | Single Use |
| **Thermostat** | Control a thermostat to maintain a target temperature. | Single Use |
| **Toggle** | Control a toggle setting (e.g., enable/disable a feature). It can also be used as Power alternative | **Multiple Use** |
| **Volume** | Control the volume of a device. | Single Use |

---

## How are capability limits determined?

The number of capabilities you can add to a custom device template is determined by your total number of device licenses. Each license grants you one capability slot.

*   **Default (Free Tier):** 3 device licenses = 3 capabilities.
*   **Example 1:** If you purchase 1 additional device license, you will have a total of 4 licenses, allowing for 4 capabilities.
*   **Example 2:** If you purchase 2 additional device licenses, you will have a total of 5 licenses, allowing for 5 capabilities.

To increase the number of available capabilities, you can purchase more device licenses from your Sinric Pro portal.

---

## How to create a custom device type?

This guide demonstrates how to create a custom **Washing Machine** device type, define wash modes (*Hot, Warm, Cold*), and generate the necessary code for ESP32/ESP8266 to control it with Alexa and Google Home.

### Step 1: Create the Device Template

1.  **Login** to your [Sinric Pro account](http://portal.sinric.pro).
2.  Navigate to the **Device Templates** menu.
3.  Click **Add Device Template**.
4.  Fill in the details:
    *   **Template Name:** `Washing Machine`
    *   **Description:** `A custom template for a washing machine.`
    *   **Device Type:** Select the type that best matches your hardware (e.g., `OTHER`).
    > **Note:** Alexa supports all device types, but Google Home has limited compatibility. If your desired device type is not listed, contact support.
5.  Go to the **Capabilities** tab.
6.  Drag and drop the **Power** capability to allow turning the machine on and off.
7.  Drag and drop the **Mode** capability to set the wash temperature.
8.  Click the **Configure** button for the **Mode** capability and define the modes:
    *   **InstanceId:** Leave the default value.
    *   **Locale:** `English (US)`
    *   **Mode name:** `Wash Temperature`
    *   **Modes:** Add `Hot`, `Warm`, and `Cold`.
9.  Click **Save** to save the modes, then click **Save** again to save the template.

### Step 2: Create a Device from the Template

1.  Go to the **Devices** menu and click **Add**.
2.  Fill in the device details:
    *   **Device Name:** `Washing Machine`
    *   **Description:** `My washing machine in the basement.`
    *   **Device Type:** Select **Washing Machine** from under "Your Device Templates".
3.  Click **Next** through the following steps and then **Save**.

### Step 3: Generate the Code

1.  From the device list, find your "Washing Machine" device and click on **Code Generator**.
2.  Sinric Pro will generate the code for an ESP8266/ESP32 project.
3.  Download the project ZIP file. Ensure you are using the latest Sinric Pro SDK (v2.9.0 or newer) to avoid compilation errors.

![Sinric Pro create a device and generate code from template](https://help.sinric.pro/public/img/create-device-template-and-generate-code.gif)

After setting up your hardware, you can discover the "Washing Machine" device in your Alexa or Google Home app.

---
### Additional Tutorials
*   [Soil Moisture Sensor (HW-390)](https://help.sinric.pro/pages/tutorials/custom-device-types/capacitive-soil-moisture-sensor/HW-390.html)
*   [Water Level Indicator (HC-SR04)](https://help.sinric.pro/pages/tutorials/custom-device-types/ultrasonic-sensor/HC-SR04.html)
*   [Water Leak/Flood Sensor](https://help.sinric.pro/pages/tutorials/custom-device-types/water-sensor/flood-leak-rain-sensor.html)
*   [Air Quality Sensor (MQ135)](https://help.sinric.pro/pages/tutorials/air-quality-sensors/mq135.html)
*   [Gas Sensor (MQ-3)](https://help.sinric.pro/pages/tutorials/custom-device-types/alcohol-sensor/MQ-3.html)

> This document is open source. See a typo? Please create an [issue](https://github.com/sinricpro/help-docs).
