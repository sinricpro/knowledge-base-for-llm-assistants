# AirQualitySensor_GP2Y1014AU0F Example

## Overview
This example demonstrates how to integrate a Sharp GP2Y1014AU0F dust sensor with SinricPro to report air quality data. It specifically focuses on measuring PM2.5 particle concentrations and sending them periodically to the SinricPro server. This is a practical example for building a real-world air quality monitoring device.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProAirQualitySensor`
*   **Capabilities Used**:
    *   [`AirQualitySensor`]: For sending `airQuality` events with PM data.
*   **Hardware Integration**: Demonstrates interfacing with a specific analog dust sensor.
*   **External Library Usage**: Utilizes the `GP2YDustSensor` Arduino library.
*   **SinricPro Integration**: Shows how to initialize SinricPro, set up connection status callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.
*   **Periodic Event Dispatching**: Sends sensor data at regular intervals.

## Hardware Requirements
*   An ESP8266 (e.g., Wemos D1 Mini) or ESP32 development board.
*   Sharp GP2Y1014AU0F Dust Sensor.
*   **Wiring (Example for Wemos D1 Mini / ESP8266)**:
    *   Sharp LED Pin (typically pin 3 on sensor) to ESP Digital Pin D5.
    *   Sharp VO Pin (analog output) to ESP Analog Pin A0.
    *   Connect VCC and GND as per sensor datasheet (typically 5V).

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Additionally, install the `GP2YDustSensor` library (search in Arduino Library Manager or find on GitHub: `https://github.com/luciansabo/GP2YDustSensor`).
2.  **Update Credentials**: Modify the following `#define` statements in the `AirQualitySensor_gp2y1014au0f.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `DEVICE_ID`: The Device ID of your Air Quality Sensor, created in the SinricPro portal (ensure it's of type `AIR_QUALITY_SENSOR`).
3.  **Wiring**: Connect the Sharp GP2Y1014AU0F sensor to your ESP board according to the `SHARP_LED_PIN` and `SHARP_VO_PIN` definitions in the code.
4.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
5.  **Upload Sketch**: Upload the `AirQualitySensor_gp2y1014au0f.ino` sketch to your ESP8266/ESP32 board.
6.  **Monitor Serial**: Open the serial monitor to observe connection status, sensor readings, and event logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Defines and Pin Assignments
```cpp
#define WIFI_SSID         ""
#define WIFI_PASS         ""
#define APP_KEY           ""
#define APP_SECRET        ""
#define DEVICE_ID         ""
#define BAUD_RATE         115200

#define MIN (1000UL * 60 * 1) // Air quality sensor event dispatch time. Min is every 1 min.
unsigned long dispatchTime = millis() + MIN;

const uint8_t SHARP_LED_PIN = D5;   // Sharp Dust/particle sensor Led Pin
const uint8_t SHARP_VO_PIN = A0;    // Sharp Dust/particle analog out pin used for reading 

GP2YDustSensor dustSensor(GP2YDustSensorType::GP2Y1014AU0F, SHARP_LED_PIN, SHARP_VO_PIN);
```
These define your Wi-Fi and SinricPro credentials, the device ID, serial baud rate, the interval for sending air quality events, and the pins connected to the Sharp dust sensor. The `dustSensor` object is initialized with the sensor type and pin assignments.

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

### `setupDustSensor()`
Initializes the `GP2YDustSensor` object. Optional calibration lines are commented out.
```cpp
void setupDustSensor() {
  //dustSensor.setBaseline(0.4); // set no dust voltage according to your own experiments
  //dustSensor.setCalibrationFactor(1.1); // calibrate against precision instrument
  dustSensor.begin();
}
```

### `setup()` and `loop()`
Standard Arduino functions for initialization and continuous execution.
```cpp
void setup() {
  Serial.begin(BAUD_RATE); Serial.printf("\r\n\r\n");
  setupWiFi();
  setupSinricPro();
  setupDustSensor();  
}

void loop() {
  SinricPro.handle();

  if((long)(millis() - dispatchTime) >= 0) {
    Serial.print("Dust density: ");
    Serial.print(dustSensor.getDustDensity());
    Serial.print(" ug/m3; Running average: ");
    Serial.print(dustSensor.getRunningAverage());
    Serial.println(" ug/m3");

    SinricProAirQualitySensor &mySinricProAirQualitySensor = SinricPro[DEVICE_ID];
    
    int pm1=0;
    int pm2_5 = dustSensor.getRunningAverage();   
    int pm10=0;   
    
    bool success = mySinricProAirQualitySensor.sendAirQualityEvent(pm1, pm2_5, pm10, "PERIODIC_POLL");
    if(success) {
      Serial.println("Air Quality event sent! ..");
    } else {
      Serial.printf("Something went wrong...could not send Event to server!\r\n");
    }

    dispatchTime += MIN;

    Serial.println("Sending Air Quality event ..");
  }  
}
```
The `loop()` function continuously calls `SinricPro.handle()`. Every minute, it reads the dust density from the `dustSensor` (specifically the running average for PM2.5), prints it to serial, and then sends an `airQuality` event to SinricPro. Note that PM1 and PM10 values are hardcoded to 0 as this sensor primarily provides PM2.5 data.

## Dependencies
*   `Arduino.h`
*   `ESP8266WiFi.h` (for ESP8266) or `WiFi.h` (for ESP32/RP2040)
*   `SinricPro.h`
*   `SinricProAirQualitySensor.h`
*   `GP2YDustSensor.h`

## Tags
`SinricPro`, `Example`, `Air Quality Sensor`, `Dust Sensor`, `GP2Y1014AU0F`, `PM2.5`, `Sensor`, `Smart Home`, `Hardware Integration`, `ESP8266`, `ESP32`
