# DHT22 Temperature Sensor Example

## Overview
This example demonstrates how to integrate a DHT11 or DHT22 temperature and humidity sensor with SinricPro. It reads temperature and humidity data from the sensor and sends `currentTemperature` events to the SinricPro server when these values change, or periodically. This is a foundational example for building smart environmental monitoring devices using common DHT sensors.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProTemperaturesensor`
*   **Capabilities Used**:
    *   [`TemperatureSensor`]: For sending `currentTemperature` events with temperature and humidity data.
*   **Hardware Integration**: Interfacing with a DHT11/DHT22 sensor.
*   **External Library Usage**: Utilizes the `DHT` library by Mark Ruys for sensor communication.
*   **Event-Driven Reporting**: Sending data only when values change significantly, combined with a rate-limiting mechanism.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, set up connection status callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   DHT11 or DHT22 temperature and humidity sensor.
*   **Wiring**: Connect the data pin of your DHT sensor to the `DHT_PIN` defined in the sketch (e.g., D5 on ESP8266, GPIO5 on ESP32). Ensure proper power supply (3.3V or 5V) and ground connections.

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Additionally, install the `DHT` library by Mark Ruys (search for "DHT sensor library by Mark Ruys" in the Arduino Library Manager).
2.  **Update Credentials**: Modify the following `#define` statements in the `DHT22.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `TEMP_SENSOR_ID`: The Device ID of your Temperature Sensor, created in the SinricPro portal (ensure it's of type `TEMPERATURE_SENSOR`).
3.  **Pin Configuration**: Define `DHT_PIN` to the GPIO pin where your DHT sensor's data line is connected.
4.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
5.  **Wiring**: Connect your DHT sensor to the `DHT_PIN` of your ESP board as described above.
6.  **Upload Sketch**: Upload the `DHT22.ino` sketch to your ESP8266/ESP32 board.
7.  **Monitor Serial**: Open the serial monitor to observe connection status, sensor readings, and event logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Global Objects and Defines
```cpp
#define WIFI_SSID         "YOUR-WIFI-SSID"    
#define WIFI_PASS         "YOUR-WIFI-PASSWORD"
#define APP_KEY           "YOUR-APP-KEY"      
#define APP_SECRET        "YOUR-APP-SECRET"   
#define TEMP_SENSOR_ID    "YOUR-DEVICE-ID"    
#define BAUD_RATE         115200                
#define EVENT_WAIT_TIME   60000               

#if defined(ESP8266)
       #define DHT_PIN    D5
#elif defined(ESP32) || defined(ARDUINO_ARCH_RP2040)
       #define DHT_PIN    5
#endif

DHT dht;

float temperature;
float humidity;
float lastTemperature;
float lastHumidity;
unsigned long lastEvent = (-EVENT_WAIT_TIME);
```
These define your Wi-Fi and SinricPro credentials, the sensor device ID, serial baud rate, the minimum time between sending events, and the GPIO pin for the DHT sensor. `dht` is the sensor object. Global variables store current and last known sensor values for change detection and `lastEvent` for rate limiting.

### `handleTemperaturesensor()`
This function is responsible for reading data from the DHT sensor and sending events to SinricPro. It implements a rate limit to prevent sending too many events and only sends data if the temperature or humidity values have changed significantly.
```cpp
void handleTemperaturesensor() {
  unsigned long actualMillis = millis();
  if (actualMillis - lastEvent < EVENT_WAIT_TIME) return; 

  temperature = dht.getTemperature();          
  humidity = dht.getHumidity();                

  if (isnan(temperature) || isnan(humidity)) { 
    Serial.printf("DHT reading failed!\r\n");  
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
  dht.setup(DHT_PIN); // Initialize DHT sensor
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
*   `DHT.h` (by Mark Ruys)

## Tags
`SinricPro`, `Example`, `Temperature Sensor`, `Humidity Sensor`, `DHT11`, `DHT22`, `Sensor`, `Smart Home`, `Environmental`, `ESP8266`, `ESP32`
