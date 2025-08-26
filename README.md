# IoT Gas + Temperature Alert System 🚨

## 📌 Overview
This project uses **ESP32 + MQ2 Gas Sensor + DHT22 Temperature Sensor** to detect harmful gases and high temperature.  
Alerts are sent to **Telegram Bot** when:
- MQ2 gas level exceeds threshold  
- Temperature > 40°C  

## 🛠️ Components
- ESP32 DevKit
- MQ2 Gas Sensor
- DHT22 Temperature Sensor
- Buzzer
- Telegram Bot API

## 🚀 Features
- Real-time Gas & Temperature Monitoring
- Buzzer alarm (beep-beep-beep pattern)
- Telegram Notifications
- Serial Monitor Debugging

## ⚙️ Setup
1. Install Arduino IDE  
2. Install libraries:
   - `DHT sensor library`
   - `UniversalTelegramBot`
   - `ArduinoJson`
   - `WiFi.h`
3. Update WiFi SSID, Password & Telegram Bot Token in code  
4. Upload code to ESP32  

---
