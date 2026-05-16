# Smart Food Spoilage Detection System - IoT-Based Atmospheric Monitoring

A robust, IoT-powered monitoring system designed to detect and alert users of food spoilage in real-time. Utilizing advanced gas sensors (MQ-series), temperature, and humidity monitoring, the system analyzes atmospheric conditions within storage environments to provide early warning of bacterial activity and decay.

> **Quick Mental Model:** Sensors continuously monitor gas and humidity. The ESP32 processes this data locally and triggers an alert if spoilage thresholds are exceeded. You get a notification on your device via the IoT Cloud. That is it.
>
> ---
>
> ## Features
>
> - **Real-Time Atmospheric Monitoring**: Uses MQ sensors to detect ammonia, methane, and other gases associated with organic decomposition.
> - - **Dual-Threshold Detection Logic**: Combines gas concentration with humidity spikes to reduce false positives and ensure accurate spoilage detection.
>   - - **IoT Cloud Integration**: Seamless connectivity with Blynk/IoT platforms for remote monitoring and real-time push notifications.
>     - - **Energy-Efficient Operation**: Optimized power consumption for long-term battery-powered deployment in refrigerators or pantries.
>       - - **Local Alert System**: Includes on-board LED and buzzer indicators for immediate local notification of detected spoilage.
>         - - **Historical Data Logging**: Automatically logs atmospheric trends to the cloud, allowing for longitudinal shelf-life analysis.
>          
>           - ---
>
> ## Architecture
>
> ```
>  Sensors (MQ, DHT)
>    |
>    V
> [Local ESP32 Processing]
>    |
>    V
>    +- [Threshold Logic Check] ---> Buzzer/LED Alert (Local)
>    |
>    V
> [IoT Cloud / Blynk]
>    |
>    V
> User Notification (Mobile/Dashboard)
> ```
>
> ### Detection Decision Logic
> ```
> Read Sensors
>      |
>      V
> Gas level > Threshold? -- YES --> Humid Spike? -- YES --> TRIGGER_SPOILAGE_ALERT
>      |                           |
>      NO                          NO
>      |                           |
>      V                           V
> STABLE_STATE              POSSIBLE_FERMENTATION (Log Only)
> ```
>
> ---
>
> ## Quick Start
>
> ```cpp
> #include <ESP32.h>
> #include <BlynkSimpleEsp32.h>
>
> char auth[] = "YourAuthToken";
> const int GAS_PIN = 34;
>
> void setup() {
>     Serial.begin(115200);
>     Blynk.begin(auth, "SSID", "PASS");
> }
>
> void loop() {
>     int gasLevel = analogRead(GAS_PIN);
>     Blynk.virtualWrite(V1, gasLevel);
>
>     if (gasLevel > 400) {
>         Blynk.logEvent("food_spoilage_alert", "Warning: Spoilage detected!");
>     }
>     Blynk.run();
> }
> ```
>
> ---
>
> ## Hardware
>
> - **MCU:** ESP32 Dev Module
> - - **Sensors:** MQ-135 (Air Quality), DHT11 (Temp/Humidity)
>   - - **Alerts:** 5V Active Buzzer, Red/Green LEDs
>     - - **Power:** 5V USB or Li-Po Battery
>      
>       - ---
>
> ## Dependencies
>
> - Blynk Library
> - - DHT Sensor Library
>   - - Adafruit Unified Sensor Library
>    
>     - ---
>
> ## License
>
> MIT License
> 
