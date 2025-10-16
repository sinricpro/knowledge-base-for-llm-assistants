# Health Example

## Overview
This example demonstrates how to implement a comprehensive health reporting mechanism for your SinricPro device. It showcases the use of the `SinricPro.onReportHealth` callback to provide detailed diagnostic information about the ESP board's status (e.g., uptime, free heap, WiFi signal strength, reset cause) to the SinricPro server. This allows for remote monitoring of your device's health and performance.

## Key Concepts/Capabilities Demonstrated
*   **SinricPro Callback**: `SinricPro.onReportHealth` for custom health reporting.
*   **Health Diagnostics**: Utilizing the `HealthDiagnostics` class to gather system-level information.
*   **System Monitoring**: Reporting metrics like uptime, free heap memory, WiFi RSSI, and reset reasons.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro and set up connection status callbacks.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   (No specific external hardware required beyond the ESP board itself).

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. You will also need `ArduinoJson` (available via Arduino Library Manager).
2.  **Update Credentials**: Modify the following `#define` statements in the `Health.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `SWITCH_ID`: The Device ID of any device (e.g., a Switch) from SinricPro. This example uses a `SinricProSwitch` as a placeholder, but the health reporting functionality is generic and works for any device type.
3.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
4.  **Upload Sketch**: Upload the `Health.ino` sketch to your ESP8266/ESP32 board.
5.  **Monitor Serial**: Open the serial monitor to observe connection status and health report logs (debug output is enabled by default).

## Code Walkthrough

### Defines
```cpp
#define WIFI_SSID "YOUR-WIFI-SSID"
#define WIFI_PASS "YOUR-WIFI-PASSWORD"
#define APP_KEY "YOUR-APP-KEY"        
#define APP_SECRET "YOUR-APP-SECRET"  
#define SWITCH_ID "YOUR-DEVICE-ID"    
#define BAUD_RATE   115200             

#define ENABLE_DEBUG // Uncomment to enable serial debug output
```
These define your Wi-Fi and SinricPro credentials, a placeholder device ID, serial baud rate, and enable debug output.

### `HealthDiagnostics` Instance
```cpp
HealthDiagnostics healthDiagnostics; // (HealthDiagnostics.h here: https://github.com/sinricpro/esp8266-esp32-sdk/tree/master/examples/Health)
```
An instance of the `HealthDiagnostics` class is created. This object is responsible for gathering the various system metrics.

### `setupWiFi()`
Connects the ESP board to your specified Wi-Fi network.
```cpp
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
  Serial.printf("connected!\r\n[WiFi]: IP-Address is %s\r\n", WiFi.localIP().toString().c_str());
}
```

### `setupSinricPro()`
Initializes the SinricPro library and sets up the `onReportHealth` callback.
```cpp
void setupSinricPro() {
   SinricProSwitch& mySwitch = SinricPro[SWITCH_ID]; // Placeholder device

  SinricPro.onConnected([]() {
    Serial.printf("Connected to SinricPro\r\n");
  });
  
  SinricPro.onDisconnected([]() {
    Serial.printf("Disconnected from SinricPro\r\n");
  });
  
  // Register the onReportHealth callback
  SinricPro.onReportHealth([&](String &healthReport) {
    return healthDiagnostics.reportHealth(healthReport);
  });  
  
  SinricPro.begin(APP_KEY, APP_SECRET);
}
```
The `onReportHealth` callback is registered with a lambda function that calls `healthDiagnostics.reportHealth()`. This means whenever the SinricPro server requests a health report, the `reportHealth` method of our `healthDiagnostics` object will be executed, populating the `healthReport` string with diagnostic data.

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
The `loop()` function continuously calls `SinricPro.handle()` to process SinricPro events and maintain the connection. The `onReportHealth` callback is triggered asynchronously by the SinricPro library when the server requests a health report.

## Dependencies
*   `Arduino.h`
*   `ArduinoJson.h`
*   `ESP8266WiFi.h` (for ESP8266) or `WiFi.h` (for ESP32)
*   `SinricPro.h`
*   `SinricProSwitch.h`
*   `HealthDiagnostics.h` (here: https://github.com/sinricpro/esp8266-esp32-sdk/tree/master/examples/Health)

## Tags
`SinricPro`, `Example`, `Health`, `Diagnostics`, `System Monitoring`, `ESP8266`, `ESP32`, `Arduino`
