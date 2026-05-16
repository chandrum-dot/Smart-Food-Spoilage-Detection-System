# Smart Food Spoilage Detection System

An enterprise-grade, IoT-powered environment monitoring framework built for ESP32. This system provides real-time, high-precision chemical analysis of food storage environments, leveraging multi-gas and pH telemetry to algorithmically determine spoilage levels before they become apparent to the human senses.

Quick Mental Model: The hardware continuously samples the localized chemical environment. The internal engine processes raw analog data into calibrated parts-per-million (PPM) and pH scales, applies threshold-based anomaly detection, and dispatches critical alerts over the cloud via Blynk IoT. You react to the dashboard. That's it.

## Features

- **Multi-Vector Chemical Profiling:** Integrates MQ4 (Methane) and MQ6 (LPG/Butane) sensors to detect the precise off-gassing signatures of bacterial decomposition in complex carbohydrates and proteins.
- **Dynamic Acidity Tracking:** Utilizes a dedicated analog pH probe to monitor localized acidity changes, the primary leading indicator of spoilage in dairy, meat, and fermented goods.
- **Asynchronous Cloud Telemetry:** Operates on a non-blocking RTOS-style loop, pushing structured telemetry payloads to a Blynk cloud dashboard without interrupting sensor sampling rates.
- **Active Self-Healing & Calibration:** Includes built-in environmental baselining on boot to account for ambient room conditions, preventing false positives from background chemical noise.
- **Fail-Safe Alerting Engine:** Automatically triggers high-priority push notifications and local buzzer alarms when critical spoilage thresholds are breached.

## Architecture

  [Physical Environment]            [Core Processing Engine]              [IoT Telemetry]
           |                                   |                                  |
               Off-Gassing --------+                    |                                  |
                                       |            +-------v-------+                  +-------v-------+
                                         MQ4 Sensor (CH4) -----+----------->|  ADC Core 1   |                  |  Blynk Cloud  |
                                                                 |            |  (Filtering)  |                  |  (Dashboard)  |
                                                                   MQ6 Sensor (LPG) -----+----------->|               |--- WiFi/TCP ---->|               |
                                                                                           |            +---------------+                  |  - Real-time  |
                                                                                             pH Probe -------------+            |  Logic Core 0 |                  |  - History    |
                                                                                                                                  | (Thresholds)  |                  |  - Push Notifs|
                                                                                                                                                                       +-------+-------+                  +---------------+
                                                                                                                                                                       
                                                                                                                                                                       ## Integration & Setup
                                                                                                                                                                       
                                                                                                                                                                       ### Mode 1 -- Direct Cloud Integration (Recommended)
                                                                                                                                                                       This mode handles all network overhead, maintaining a persistent TCP connection to the Blynk servers while managing sensor reads asynchronously.
                                                                                                                                                                       
                                                                                                                                                                       ```cpp
                                                                                                                                                                       #include <WiFi.h>
                                                                                                                                                                       #include <BlynkSimpleEsp32.h>
                                                                                                                                                                       #include "SpoilageEngine.h"
                                                                                                                                                                       
                                                                                                                                                                       SpoilageEngine engine;
                                                                                                                                                                       
                                                                                                                                                                       void setup() {
                                                                                                                                                                         Serial.begin(115200);
                                                                                                                                                                           engine.setCredentials("TMPL_ID", "AUTH", "SSID", "PASS");
                                                                                                                                                                             engine.attachMQ4(34);
                                                                                                                                                                               engine.attachMQ6(35);
                                                                                                                                                                                 engine.attachPH(32);
                                                                                                                                                                                   engine.init();
                                                                                                                                                                                   }
                                                                                                                                                                                   
                                                                                                                                                                                   void loop() {
                                                                                                                                                                                   
                                                                                                                                                                                   }
                                                                                                                                                                                   ```
                                                                                                                                                                                   
                                                                                                                                                                                   ## Config Reference
                                                                                                                                                                                   - mq4_threshold = 400 PPM
                                                                                                                                                                                   - mq6_threshold = 350 PPM
                                                                                                                                                                                   - ph_acidic_limit = 5.5
                                                                                                                                                                                   - ph_alkaline_limit = 8.5
                                                                                                                                                                                   
                                                                                                                                                                                   Deployment Best Practices:
                                                                                                                                                                                   - Burn-in Time: Gas sensors (MQ series) require a minimum 24-48 hour "burn-in" period.
                                                                                                                                                                                   - Power: The internal heaters of MQ sensors draw significant current (~150mA each). A standalone 5V 2A power adapter is strongly recommended.
                                                                                                                                                                                   
                                                                                                                                                                                   ## Hardware Specifications
                                                                                                                                                                                   - MCU: ESP32 Dev Module (38-pin)
                                                                                                                                                                                   - Sensors: MQ4, MQ6, Analog pH Probe
                                                                                                                                                                                   - Power Architecture: 5V 2A Adapter -> Common GND for all components.
                                                                                                                                                                                   
                                                                                                                                                                                   ## License
                                                                                                                                                                                   MIT License
