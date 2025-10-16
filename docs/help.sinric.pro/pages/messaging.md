---
title: Messaging
layout: post
---

**Overview**

The documentation covers the Sinric Pro WebSocket messaging protocol. All messages sent over the WebSocket protocol is in JSON format and they are signed using HMAC key  to guarante the authenticity of the request. Sample message definitions are available [here](https://github.com/sinricpro/sample_messages)

We recommend using one of the SDKs we have built since they properly handle authentication, connection, reconnection and many more feature for messaging layer. We have libraries written for `Arduino`, `ESP8266`, `ESP32`, `RaspberryPI`, and `PC`

-   [ESP8266/ESP32 SDK](https://github.com/sinricpro/esp8266-esp32-sdk) - [examples](https://github.com/sinricpro/esp8266-esp32-sdk/tree/master/examples)
-   [Python SDK](https://github.com/sinricpro/python-sdk)  - [examples](https://github.com/sinricpro/python-examples)

### Messaging
There are 3 types of messages in Sinric Pro platform. They are

 1. Request
 2. Respond
 3. Event

## Request
An act of doing something in will generate a **request** type message in the system. Eg: Alexa, turn on [device name] will generate a request message like below  

```json   
{
    "header": {
        "payloadVersion": 2,
        "signatureVersion": 1
    },
    "payload": {
        "action": "setPowerState",
        "clientId": "alexa-skill",
        "createdAt": 1567852244,
        "deviceAttributes": [],
        "deviceId": "5d737888aea17c30a056d759",
        "replyToken": "6ec1f778-e92f-487c-9818-bdbe3438f30e",
        "type": "request",
        "value": {
            "state": "On"
        }
    },
    "signature": {
        "HMAC": "jjrxa5b7fzXXml2+PxlfUYIUgLdHg33Cr2yWVuzlr/s="
    }
}

```

 
[Complete Message](https://github.com/sinricpro/sample_messages/blob/master/01_PowerState/01_setPowerState/01_Request.json)

## Response
 When you receive such a request in your IOT module, you must respond to it by sending a **response** type message. The correct response to above request should be

```json
{
    "header": {
        "payloadVersion": 2,
        "signatureVersion": 1
    },
    "payload": {
        "action": "setPowerState",
        "clientId": "alexa-skill",
        "createdAt": 1567860300,
        "deviceId": "5d737888aea17c30a056d759",
        "message": "OK",
        "replyToken": "6ec1f778-e92f-487c-9818-bdbe3438f30e",
        "success": true,
        "type": "response",
        "value": {
            "state": "On"
        }
    },
    "signature": {
        "HMAC": "xxxxxxxx"
    }
}

```

[Complete Message](https://github.com/sinricpro/sample_messages/blob/master/01_PowerState/01_setPowerState/02_Response.json)

**Notes:**

* **replyToken** in the request is being used to identify the message. It must be used when you create a "response" type message
* **createdAt** timestamp is the Unix time in seconds
* If you are using the SDK, the responses will be handled by the SDK internally

Upon receiving this message in the server, the server will update the interested parties about the status of the **request** .

## Event
Let's imagine you want to turn on the device by pushing a button or change the brightness level using a nob. Now you are interacting with the device physically and making changes to it. To notify the server about the changes you make, you can send an **event**  message to the server. Eg: Amazon Alexa cloud

```json   
{
    "header": {
        "payloadVersion": 2,
        "signatureVersion": 1
    },
    "payload": {
        "action": "setPowerState",
        "cause": {
            "type": "PHYSICAL_INTERACTION"
        },
        "createdAt": 1567852244,
        "deviceId": "5d737888aea17c30a056d759",
        "replyToken": "6ec1f778-e92f-487c-9818-bdbe3438f30e",
        "type": "event",
        "value": {
            "state": "On"
        }
    },
    "signature": {
        "HMAC": "xxxxx"
    }
}
 ```

  
 

1.  The user change the device state physically. Eg: push a button to turn on the switch. 
2.  Your IOT module creates an [event](https://github.com/sinricpro/sample_messages/blob/master/01_PowerState/01_setPowerState/03_Event.json) message and send it to Sinric Pro IOT Platform. (In this case your IOT module sends setPowerState event)
3.  The Sinric Pro IOT platform update the device status and update Alexa service

Sinric Pro IOT platform will report any changes you do via events, app or API to Alexa as well. 

### Example
1. The Amazon Alexa sends a message to the Sinric Pro IOT Platform. eg: Turn on [device name]
2. The Sinric Pro IoT platform creates a [request](https://github.com/sinricpro/sample_messages/blob/master/01_PowerState/01_setPowerState/01_Request.json) type message with appropriate action (in this case setPowerState) and send it to your IoT hardware module.
3. Your IoT hardware module [responds](https://github.com/sinricpro/sample_messages/blob/master/01_PowerState/01_setPowerState/02_Response.json) back to the Sinric Pro IOT Platform with success or failed status. 

   **If you fail to respond with in 8 seconds / or device is offline, then the request will timeout and Alexa will say "Device is unresponsive".**

4. The Sinric Pro IoT platform updates the device status according to your response and updates Alexa service.
