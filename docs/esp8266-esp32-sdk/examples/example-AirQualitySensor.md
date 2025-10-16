# AirQualitySensor Example

## Overview
This example demonstrates how to integrate an Air Quality Sensor with SinricPro. It showcases how to periodically report PM1, PM2.5, and PM10 particle concentrations to the SinricPro server using the `SinricProAirQualitySensor` device type. This provides a foundational framework for developers looking to build smart air quality monitoring solutions.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProAirQualitySensor`
*   **Capabilities Used**:
    *   [`AirQualitySensor`]: For sending `airQuality` events with PM data.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, set up connection status callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.
*   **Periodic Event Dispatching**: Shows how to send sensor data at regular intervals.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   (Implied) An air quality sensor capable of measuring PM1, PM2.5, and PM10 concentrations (e.g., PMS5003, SDS011). *Note: The example code uses placeholder values for PM data; actual sensor integration would be required for real-world use.*

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Update Credentials**: Modify the following `#define` statements in the `AirQualitySensor.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `DEVICE_ID`: The Device ID of your Air Quality Sensor, created in the SinricPro portal (ensure it's of type `AIR_QUALITY_SENSOR`).
3.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
4.  **Upload Sketch**: Upload the `AirQualitySensor.ino` sketch to your ESP8266/ESP32 board.
5.  **Monitor Serial**: Open the serial monitor to observe connection status and event logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Defines
```cpp
#define WIFI_SSID         ""
#define WIFI_PASS         ""
#define APP_KEY           ""
#define APP_SECRET        ""
#define DEVICE_ID         ""
#define BAUD_RATE         115200

#define MIN (1000UL * 60 * 1) // Air quality sensor event dispatch time. Min is every 1 min.
unsigned long dispatchTime = millis() + MIN;
```
These define your Wi-Fi and SinricPro credentials, the device ID, serial baud rate, and the interval for sending air quality events.

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
Initializes the SinricPro library and adds the Air Quality Sensor device. It also sets up callbacks for connection status.
```cpp
void setupSinricPro() {
  SinricProAirQualitySensor& mySinricProAirQualitySensor = SinricPro[DEVICE_ID];
  SinricPro.onConnected([](){ Serial.printf("Connected to SinricPro\r\n"); }); 
  SinricPro.onDisconnected([](){ Serial.printf("Disconnected from SinricPro\r\n"); });
  SinricPro.begin(APP_KEY, APP_SECRET);
}
```

### `setup()` and `loop()`
Standard Arduino functions for initialization and continuous execution.
```cpp
void setup() {
  Serial.begin(BAUD_RATE); Serial.printf("\r\n\r\n");
  setupWiFi();
  setupSinricPro();  
}

void loop() {
  SinricPro.handle();

  if((long)(millis() - dispatchTime) >= 0) {
    SinricProAirQualitySensor &mySinricProAirQualitySensor = SinricPro[DEVICE_ID]; // get sensor device
    
    int pm1 =0;
    int pm2_5 = 0;   
    int pm10=0;   
    
    bool success = mySinricProAirQualitySensor.sendAirQualityEvent(pm1, pm2_5, pm10, "PERIODIC_POLL");
    if(success) {
      Serial.println("Air Quality event sent! ..");
    } else {
      Serial.printf("Something went wrong...could not send Event to server!\r\n");
    }

    dispatchTime += MIN;
  }  
}
```
The `loop()` function continuously calls `SinricPro.handle()` to process events. Every minute, it attempts to send an air quality event using `sendAirQualityEvent`. *Note: The `pm1`, `pm2_5`, and `pm10` values are currently hardcoded to 0. In a real application, you would replace these with actual readings from your air quality sensor.*

## Dependencies
*   `Arduino.h`
*   `ESP8266WiFi.h` (for ESP8266) or `WiFi.h` (for ESP32/RP2040)
*   `SinricPro.h`
*   `SinricProAirQualitySensor.h`

## Tags
`SinricPro`, `Example`, `Air Quality Sensor`, `Sensor`, `PM1`, `PM2.5`, `PM10`, `Smart Home`, `ESP8266`, `ESP32`
