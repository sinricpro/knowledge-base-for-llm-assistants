# SetFixedIPAddress Example

## Overview
This example demonstrates how to remotely configure a fixed (static) IP address, gateway, subnet mask, and DNS servers for your SinricPro device. It utilizes the `SinricPro.onSetSetting` callback to receive network configuration details from the SinricPro platform, allowing for dynamic network setup without needing to reflash the device. This is useful for devices that require a stable IP address on your local network.

## Key Concepts/Capabilities Demonstrated
*   **Remote Configuration**: Handling `onSetSetting` callback to receive and process network configuration details.
*   **JSON Parsing**: Extracting network parameters from a JSON payload using `ArduinoJson`.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   (No specific external hardware required beyond the ESP board itself).

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. You will also need the `ArduinoJson` library (available via Arduino Library Manager).
2.  **Update Credentials**: Modify the following `#define` statements in the `SetFixedIPAddress.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `SWITCH_ID`: The Device ID of any device (e.g., a Switch) from SinricPro. This example uses a `SinricProSwitch` as a placeholder, but the network configuration is generic.
3.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
4.  **Upload Sketch**: Upload the `SetFixedIPAddress.ino` sketch to your ESP8266/ESP32 board.
5.  **Monitor Serial**: Open the serial monitor to observe connection status and logs.
6.  **Remote Configuration**: In the SinricPro portal, navigate to your device and use the `setSetting` action.
    *   Set the `id` field to `pro.sinric::set.fixed.ip.address`.
    *   Set the `value` field to a JSON string containing your network details. For example:
        ```json
        {"localIP": "192.168.1.100", "gateway": "192.168.1.1", "subnet": "255.255.255.0", "dns1": "8.8.8.8", "dns2": "8.8.4.4"}
        ```
        You can omit `dns1` and `dns2` if not needed.

## Code Walkthrough

### Defines
```cpp
#define WIFI_SSID ""
#define WIFI_PASS ""
#define APP_KEY ""     
#define APP_SECRET ""  
#define SWITCH_ID ""   
#define BAUD_RATE 115200  

#define SET_FIXED_IP_ADDRESS "pro.sinric::set.fixed.ip.address"
```
These define your Wi-Fi and SinricPro credentials, a placeholder device ID, serial baud rate, and the unique identifier for the fixed IP address setting command.

### `onSetModuleSetting(const String &id, const String &value)`
This callback function is registered with `SinricPro.onSetSetting()` and is responsible for processing incoming requests to set a fixed IP address. It parses the JSON payload to extract the network parameters and prints them to the serial monitor.

**Important Note**: This example primarily demonstrates *receiving* the fixed IP address configuration. To *apply* the static IP to your ESP's Wi-Fi stack, you would need to add `WiFi.config()` and potentially `WiFi.reconnect()` or `ESP.restart()` within this function after parsing the values.
```cpp
bool onSetModuleSetting(const String &id, const String &value) {
  JsonDocument doc;
  DeserializationError error = deserializeJson(doc, value);

  if (error) {
    Serial.print(F("onSetModuleSetting::deserializeJson() failed: "));
    Serial.println(error.f_str());
    return false;
  }

  if (id == SET_FIXED_IP_ADDRESS) {
    String localIP = doc["localIP"];
    String gateway = doc["gateway"];
    String subnet = doc["subnet"];
    String dns1 = doc["dns1"] | "";
    String dns2 = doc["dns2"] | "";

    Serial.printf("localIP:%s, gateway:%s, subnet:%s, dns1:%s, dns2:%s   \r\n", localIP.c_str(), gateway.c_str(), subnet.c_str(), dns1.c_str(), dns2.c_str());
    // TODO: Add WiFi.config() here to apply the static IP
    // WiFi.config(IPAddress(localIP), IPAddress(gateway), IPAddress(subnet), IPAddress(dns1), IPAddress(dns2));
    // ESP.restart(); // Or WiFi.reconnect();
    return true;
  } else {
    return false;
  }
}
```

### `setupWiFi()`
Connects the ESP board to your specified Wi-Fi network using DHCP initially.
```cpp
void setupWiFi() {
  Serial.printf("\r\n[Wifi]: Connecting");
#if defined(ESP8266)
  WiFi.setSleepMode(WIFI_NONE_SLEEP);
  WiFi.setAutoReconnect(true);
#elif defined(ESP32) || defined(ARDUINO_ARCH_RP2040)
  WiFi.setSleep(false);
  WiFi.setAutoReconnect(true);
#endif
  WiFi.begin(WIFI_SSID, WIFI_PASS);
  while (WiFi.status() != WL_CONNECTED) {
    Serial.printf(".");
    delay(250);
  }
  Serial.printf("connected!\r\n[WiFi]: IP-Address is %s\r\n", WiFi.localIP().toString().c_str());
}
```

### `setupSinricPro()`
Initializes the SinricPro library and registers the `onSetModuleSetting` callback to enable remote network configuration. It also sets up standard connection status callbacks.
```cpp
void setupSinricPro() {
  SinricProSwitch &mySwitch = SinricPro[SWITCH_ID];
  SinricPro.onConnected([]() {
    Serial.printf("Connected to SinricPro\r\n");
  });
  SinricPro.onDisconnected([]() {
    Serial.printf("Disconnected from SinricPro\r\n");
  });
  SinricPro.onSetSetting(onSetModuleSetting);
  SinricPro.begin(APP_KEY, APP_SECRET);
}
```

### `setup()` and `loop()`
Standard Arduino functions for initialization and continuous execution.
```cpp
void setup() {
  Serial.begin(BAUD_RATE);
  Serial.printf("\r\n\r\n");
  setupWiFi();
  setupSinricPro();
}

void loop() {
  SinricPro.handle();
}
```

The `loop()` function continuously calls `SinricPro.handle()` to process SinricPro events and maintain the connection.

## Dependencies
*   `Arduino.h`
*   `ESP8266WiFi.h` (for ESP8266) or `WiFi.h` (for ESP32/RP2040)
*   `SinricPro.h`
*   `SinricProSwitch.h`
*   `ArduinoJson.h`

## Tags
`SinricPro`, `Example`, `WiFi`, `Static IP`, `Fixed IP`, `Remote Configuration`, `Settings`, `ESP8266`, `ESP32`
