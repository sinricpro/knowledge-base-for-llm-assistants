---
title: How to create Apple Shortcuts with SinricPro
layout: post
---

### Introduction
In this tutorial we are going to create a Apple Shortcut to turn on and off a relay connected to a ESP8266, ESP32 or Raspberry Pi using the Sinric Pro API. 

### Prerequisites : 
Please complete [Tutorial - Turn on and off a Relay](https://help.sinric.pro/pages/tutorials/switch/part-1) in order to learn how to connect your ESP or Pi to Sinric Pro.

#### Step 1: Create a new API Key in Sinric Pro
Login to [Sinric Portal](https://portal.sinric.pro), select **Credentials** and click on **New API Key** click **save** and **Copy** the newly created API Key

You can use the `curl` to send a test request to this API endpoint.

```curl
curl --location --request POST 'https://apple.sinric.pro/v1/shortcuts/actions' \
--header 'Content-Type: application/json' \
--data-raw '{ "api_key": "YOUR_API_KEY_FROM_ABOVE", "device_id": "YOUR_DEVICE_ID", "action": "setPowerState", "value": { "state" : "On"} }'

```

#### Step 2: Create Apple Shortcut
1. Create a new shortcut and add the Get Contents of URL action.
2. In the URL field, enter the URL: `https://apple.sinric.pro/v1/shortcuts/actions`
3. Tap on the Method field and select `POST`.
4. Tap on the Body field and add the body of your POST request.

- api_key: Your API key from above.
- device_id: Your device id from the Sinric Pro Portal.
- action: Action to perform. set to `setPowerState`. Check below for more examples.
- value: Enter `state` as key and `On` or `Off` value.

Tap on play button below to test the request!

Now you can control your ESP8266, ESP32 or Raspberry Pi via Apple Shortcut from home screen.

#### More examples
Power On or Off:

```json
{ "api_key": "YOUR_API_KEY_FROM_ABOVE", "device_id": "YOUR_DEVICE_ID", "action": "setPowerState", "value": { "state" : "On"} }

```

Garage door open/close:

```json
{ "api_key": "YOUR_API_KEY_FROM_ABOVE", "device_id": "YOUR_DEVICE_ID", "action": "setMode", "value": { "mode" : "Open"} }

```

Blinds open/close:

```json
{ "api_key": "YOUR_API_KEY_FROM_ABOVE", "device_id": "YOUR_DEVICE_ID", "action": "setRangeValue", "value": { "rangeValue" : 100} }

```

Change power level:

```json
    { "api_key": "YOUR_API_KEY_FROM_ABOVE", "device_id": "YOUR_DEVICE_ID", "action": "setPowerLevel", "value": { "powerLevel": 50 } }

```

Change brightness:

```json
    { "api_key": "YOUR_API_KEY_FROM_ABOVE", "device_id": "YOUR_DEVICE_ID", "action": "setBrightness", "value": { "brightness": 50 } }

```

Trigger doorbell:

```json
    { "api_key": "YOUR_API_KEY_FROM_ABOVE", "device_id": "YOUR_DEVICE_ID", "action": "DoorbellPress", "value": {  "state": "pressed" } }

```

Set target temperature:

```json
    { "api_key": "YOUR_API_KEY_FROM_ABOVE", "device_id": "YOUR_DEVICE_ID", "action": "targetTemperature", "value": {  "temperature": 18 } }

```

Change color:

```json
    { "api_key": "YOUR_API_KEY_FROM_ABOVE", "device_id": "YOUR_DEVICE_ID", "action": "setColor", "value": {  "color": { "b": 0, "g": 0, "r": 0 } } }

```

Change color temperature:

```json
    { "api_key": "YOUR_API_KEY_FROM_ABOVE", "device_id": "YOUR_DEVICE_ID", "action": "setColorTemperature", "value": {"colorTemperature":2700} }

```

More examples: [https://github.com/sinricpro/sample_messages](https://github.com/sinricpro/sample_messages)
