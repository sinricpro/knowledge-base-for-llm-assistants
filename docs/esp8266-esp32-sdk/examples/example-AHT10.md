# AHT10 Temperature Sensor Example

## Overview
This example demonstrates how to integrate an AHT10 or AHT20 temperature and humidity sensor with SinricPro. It reads temperature and humidity data from the sensor and sends `currentTemperature` events to the SinricPro server when these values change, or periodically. This is a foundational example for building smart environmental monitoring devices, allowing you to track indoor climate conditions.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProTemperaturesensor`
*   **Capabilities Used**:
    *   [`TemperatureSensor`]: For sending `currentTemperature` events with temperature and humidity data.
*   **Hardware Integration**: Interfacing with an AHT10/AHT20 sensor via I2C communication.
*   **External Library Usage**: Utilizes the `Adafruit_AHTX0` and `Adafruit Unified Sensor` libraries for sensor communication.
*   **Event-Driven Reporting**: Sending data only when values change significantly, combined with a rate-limiting mechanism.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, set up connection status callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   AHT10 or AHT20 temperature and humidity sensor.
*   **Wiring**: Connect the AHT10/AHT20 sensor to the I2C pins of your ESP board. Typically:
    *   SDA (Data) pin of AHT to ESP's SDA pin (e.g., D2/GPIO4 on ESP8266, GPIO21 on ESP32).
    *   SCL (Clock) pin of AHT to ESP's SCL pin (e.g., D1/GPIO5 on ESP8266, GPIO22 on ESP32).
    *   VCC and GND to appropriate power pins (3.3V or 5V, depending on sensor).

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Additionally, install the `Adafruit AHTX0` and `Adafruit Unified Sensor` libraries (available in the Arduino Library Manager).
2.  **Update Credentials**: Modify the following `#define` statements in the `AHT10.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `TEMP_SENSOR_ID`: The Device ID of your Temperature Sensor, created in the SinricPro portal (ensure it's of type `TEMPERATURE_SENSOR`).
3.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
4.  **Wiring**: Connect your AHT10/AHT20 sensor to the I2C pins of your ESP board as described above.
5.  **Upload Sketch**: Upload the `AHT10.ino` sketch to your ESP8266/ESP32 board.
6.  **Monitor Serial**: Open the serial monitor to observe connection status, sensor readings, and event logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Global Objects and Defines
```cpp
#define WIFI_SSID         ""
#define WIFI_PASS         ""
#define APP_KEY           ""    
#define APP_SECRET        ""    
#define TEMP_SENSOR_ID    ""    
#define BAUD_RATE         115200                
#define EVENT_WAIT_TIME   60000               // send event every 60 seconds

Adafruit_AHTX0 aht;

float humidity;
float temperature;
float lastTemperature;
float lastHumidity;
unsigned long lastEvent = (-EVENT_WAIT_TIME); 
```
These define your Wi-Fi and SinricPro credentials, the sensor device ID, serial baud rate, and the minimum time between sending events. `aht` is the sensor object. Global variables store current and last known sensor values for change detection and `lastEvent` for rate limiting.

### `handleTemperaturesensor()`
This function is responsible for reading data from the AHT sensor and sending events to SinricPro. It implements a rate limit to prevent sending too many events and only sends data if the temperature or humidity values have changed significantly.
```cpp
void handleTemperaturesensor() {
  unsigned long actualMillis = millis();
  if (actualMillis - lastEvent < EVENT_WAIT_TIME) return; 
  if (!aht.begin()) {
    Serial.printf("Sensor not initialized\r\n");
    return;
  }

  sensors_event_t hum, temp;
  aht.getEvent(&hum, &temp);
  float humidity = hum.relative_humidity;
  float temperature = temp.temperature; 

  if (isnan(temperature) || isnan(humidity)) { 
    Serial.printf("AHT reading failed!\r\n");  
    return;                                    
  }

  if (temperature == lastTemperature || humidity == lastHumidity) return; 

  SinricProTemperaturesensor &mySensor = SinricPro[TEMP_SENSOR_ID];  
  bool success = mySensor.sendTemperatureEvent(temperature, humidity); 
  if (success) {  
    Serial.printf("Temperature: %2.1f Celsius\tHumidity: %2.1f%%\r\n", temperature, humidity);
  } else {  
    Serial.printf("Something went wrong...could not send Event to server!\r\n");
  }

  lastTemperature = temperature;  
  lastHumidity = humidity;        
  lastEvent = actualMillis;       
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
  Serial.printf("connected!\r\n[WiFi]: IP-Address is %d.%d.%d.%d\r\n", localIP[0], localIP[1], localIP[2], localIP[3]);
}
```

### `setupSinricPro()`
Initializes the SinricPro library and adds the Temperature Sensor device. It also sets up callbacks for connection status.
```cpp
void setupSinricPro() {
  SinricProTemperaturesensor &mySensor = SinricPro[TEMP_SENSOR_ID];
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
  aht.begin(); // Initialize AHT sensor
  setupWiFi();
  setupSinricPro();
}

void loop() {
  SinricPro.handle();
  handleTemperaturesensor();
}
```

The `loop()` function continuously calls `SinricPro.handle()` to process SinricPro events and `handleTemperaturesensor()` to monitor and report sensor data.

## Dependencies
*   `Arduino.h`
*   `ESP8266WiFi.h` (for ESP8266) or `WiFi.h` (for ESP32/RP2040)
*   `SinricPro.h`
*   `SinricProTemperaturesensor.h`
*   `Adafruit_AHTX0.h`
*   `Adafruit_Sensor.h`

## Tags
`SinricPro`, `Example`, `Temperature Sensor`, `Humidity Sensor`, `AHT10`, `AHT20`, `Sensor`, `Smart Home`, `Environmental`, `ESP8266`, `ESP32`
