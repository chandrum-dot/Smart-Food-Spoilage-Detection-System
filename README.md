<div align="center">

# Smart Food Spoilage Detection System
*An enterprise-grade, IoT-powered environment monitoring framework built for ESP32.*

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Platform: ESP32](https://img.shields.io/badge/Platform-ESP32-orange.svg)]()
[![IoT: Blynk](https://img.shields.io/badge/IoT-Blynk-brightgreen.svg)]()

</div>

<br />

Quick Mental Model: The hardware continuously samples the localized chemical environment. The internal engine processes raw analog data into calibrated parts-per-million (PPM) and pH scales, applies threshold-based anomaly detection, and dispatches critical alerts over the cloud via Blynk IoT. You react to the dashboard. That's it.

---

## DOCS Table of Contents

- - - SETUP Integration and Setup
    - - CONFIGURATION Configuration and Hardware
     
      - ---

      ## FEATURES Features

      - Multi-Vector Chemical Profiling: Integrates MQ4 (Methane) and MQ6 (LPG/Butane) sensors to detect precise off-gassing signatures.
     
      - - Dynamic Acidity Tracking: Utilizes a dedicated analog pH probe to monitor localized acidity changes.
       
        - - Asynchronous Cloud Telemetry: Operates on a non-blocking RTOS-style loop, pushing structured telemetry payloads.
         
          - - Active Self-Healing and Calibration: Includes built-in environmental baselining on boot.
           
            - - Fail-Safe Alerting Engine: Automatically triggers high-priority push notifications and local buzzer alarms.
             
              - ## ARCHITECTURE Architecture
             
              - ```text
                  [Physical Env]            [Core Processing Engine]              [IoT Telemetry]
                         |                                   |                                  |
                    Off-Gassing --------+                    |                                  |
                                        |            +-------V-------+                  +-------V-------+
                  MQ4 Sensor (CH4) -----+----------->|  ADC Core 1   |                  |  Blynk Cloud  |
                                        |            |  (Filtering)  |                  |  (Dashboard)  |
                  MQ6 Sensor (LPG) -----+----------->|               |--- WiFi/TCP ---->|               |
                                        |            +---------------+                  |  - Real-time  |
                  pH Probe -------------+            |  Logic Core 0 |                  |  - History    |
                                                     | (Thresholds)  |                  |  - Push Notifs|
                                                     +-------+-------+                  +---------------+
                ```

                ## SETUP Integration and Setup

                ### Mode 1 - Direct Cloud Integration (Recommended)
                Handles all network overhead, maintaining a persistent TCP connection to the Blynk servers.

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
                  engine.processTasks();
                }
                ```

                ## CONFIGURATION Configuration and Hardware

                | Parameter | Type | Default | Description |
                |-----------|------|---------|-------------|
                | mq4_threshold | int | 400 | PPM limit for Methane before triggering alert. |
                | mq6_threshold | int | 350 | PPM limit for LPG/Butane off-gassing. |
                | ph_acidic_limit | float | 5.5 | Lower bound pH indicating dangerous fermentation. |
                | ph_alkaline_limit | float | 8.5 | Upper bound pH indicating protein breakdown. |

                Deployment Best Practices:
                
