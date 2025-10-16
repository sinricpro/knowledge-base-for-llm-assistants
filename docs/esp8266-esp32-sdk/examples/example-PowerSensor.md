# PowerSensor Example

## Overview
This example demonstrates how to integrate a power sensor with SinricPro. It simulates power measurements (voltage, current, power, apparent power) and sends them periodically to the SinricPro server. This is a foundational example for building smart energy monitoring devices, allowing you to track and visualize power consumption.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProPowerSensor`
*   **Capabilities Used**: 
    *   [`PowerSensor`]: For sending `powerUsage` events with power-related metrics.
*   **Periodic Data Reporting**: Implements a mechanism to send sensor data at regular, configurable intervals.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, set up connection status callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   (Implied) A power measurement sensor (e.g., PZEM-004T, ACS712, or a dedicated energy monitoring IC). *Note: The example uses simulated data; actual sensor integration would be required for real-world use.*

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Update Credentials**: Modify the following `#define` statements in the `PowerSensor.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `POWERSENSOR_ID`: The Device ID of your Power Sensor, created in the SinricPro portal (ensure it's of type `POWER_SENSOR`).
3.  **Reporting Interval**: Adjust `SAMPLE_EVERY_SEC` to change how often power data is sent (default is 60 seconds).
4.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
5.  **Upload Sketch**: Upload the `PowerSensor.ino` sketch to your ESP8266/ESP32 board.
6.  **Monitor Serial**: Open the serial monitor to observe connection status and event logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Defines
```cpp
#define WIFI_SSID         "YOUR-WIFI-SSID"    
#define WIFI_PASS         "YOUR-WIFI-PASSWORD"
#define APP_KEY           "YOUR-APP-KEY"      
#define APP_SECRET        "YOUR-APP-SECRET"   
#define POWERSENSOR_ID    "YOUR-DEVICE-ID"    
#define BAUD_RATE         115200                
#define SAMPLE_EVERY_SEC  60                  

bool powerState = false;

struct {
  float voltage;
  float current;
  float power;
  float apparentPower;
  float reactivePower;
  float factor;
} powerMeasure;
```
These define your Wi-Fi and SinricPro credentials, the power sensor device ID, serial baud rate, and the sampling interval. The `powerMeasure` struct holds the simulated power measurement data.

### `doPowerMeasure()`
This function simulates reading power data by assigning random values to `powerMeasure.voltage`, `powerMeasure.current`, and then calculating `powerMeasure.power` and `powerMeasure.apparentPower`. **In a real-world application, this function would be replaced with actual readings from your power measurement sensor.**
```cpp
void doPowerMeasure() {
  powerMeasure.voltage = random(2200,2300) / 10.0f;
  powerMeasure.current = random(1,20) / 10.0f;
  powerMeasure.power = powerMeasure.voltage * powerMeasure.current;
  powerMeasure.apparentPower = powerMeasure.power + (random(10,20)/10.0f);
}
```

### `sendPowerSensorData()`
This function is responsible for sending the power sensor data to SinricPro. It implements a rate limit using `lastEvent` and `SAMPLE_EVERY_SEC` to ensure data is sent only at the defined interval, preventing excessive data transmission.
```cpp
void sendPowerSensorData() {
  static unsigned long lastEvent = 0;
  unsigned long actualMillis = millis();
  if (actualMillis - lastEvent < (SAMPLE_EVERY_SEC * 1000)) return;
  lastEvent = actualMillis;

  doPowerMeasure();

  SinricProPowerSensor &myPowerSensor = SinricPro[POWERSENSOR_ID];
  bool success = myPowerSensor.sendPowerSensorEvent(powerMeasure.voltage, powerMeasure.current, powerMeasure.power, powerMeasure.apparentPower);
  if(!success) {
    Serial.printf("Something went wrong...could not send Event to server!\r\n");
  }
}
```

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
  IPAddress localIP = WiFi.localIP();
  Serial.printf("connected!\r\n[WiFi]: IP-Address is %s\r\n", WiFi.localIP().toString().c_str());
}
```

### `setupSinricPro()`
Initializes the SinricPro library and adds the Power Sensor device. It also sets up callbacks for connection status.
```cpp
void setupSinricPro() {
  SinricProPowerSensor &myPowerSensor = SinricPro[POWERSENSOR_ID];
  SinricPro.onConnected([](){ Serial.printf("Connected to SinricPro\r\n"); }); 
  SinricPro.onDisconnected([](){ Serial.printf("Disconnected from SinricPro\r\n"); });
  SinricPro.begin(APP_KEY, APP_SECRET);
}
```

### `setup()` and `loop()`
Standard Arduino functions for initialization and continuous execution.
```cpp
void setup() {
  Serial.begin(BAUD_RATE);
  setupWiFi();
  setupSinricPro();
}

void loop() {
  SinricPro.handle();
  sendPowerSensorData();
}
```
The `loop()` function continuously calls `SinricPro.handle()` to process SinricPro events and `sendPowerSensorData()` to periodically send power measurements.

## Dependencies
*   `Arduino.h`
*   `ESP8266WiFi.h` (for ESP8266) or `WiFi.h` (for ESP32/RP2040)
*   `SinricPro.h`
*   `SinricProPowerSensor.h`

## Tags
`SinricPro`, `Example`, `Power Sensor`, `Energy Monitoring`, `Sensor`, `Smart Home`, `ESP8266`, `ESP32`
