---
title: ESP32-Wroom-32玩法
date: 2024-12-15 16:18:10
tags: [笔记, 嵌入式, esp32,Iot,物联网协议,esp32series]
categories: [硬件技术]	
---

# 硬件设计

基于esp32-wroom-32

## Project 1 设计一片esp32 uno













# 软件代码

全部代码基于`platformIO`



## 连接emqx分布式mqtt服务器



```cpp
#include <WiFiClientSecure.h>
#include <PubSubClient.h>
#include <time.h>

// WiFi credentials
uint LED = 16;        
const char *ssid = "regen";  // Replace with your WiFi name
const char *password = "regen's pwd"; // Replace with your WiFi password

// MQTT Broker settings
const int mqtt_port = 8883;  // MQTT port (TLS)
const char *mqtt_broker = "server ip or domain";  // EMQX broker endpoint
const char *mqtt_topic = "/esp38266/test"; // MQTT topic
const char *mqtt_username = "uasername";  // MQTT username for authentication
const char *mqtt_password = "passwd";  // MQTT password for authentication

const char *mqtt_start = "start";
const char *mqtt_stop = "stop";

// NTP Server settings
const char *ntp_server = "pool.ntp.org"; // Default NTP server
const long gmt_offset_sec = 0;           // GMT offset in seconds (adjust for your time zone)
const int daylight_offset_sec = 0;       // Daylight saving time offset in seconds

// WiFi and MQTT client initialization
WiFiClientSecure espClient;
PubSubClient mqtt_client(espClient);

// SSL certificate for MQTT broker
static const char ca_cert[] PROGMEM = R"EOF(
-----BEGIN CERTIFICATE-----

-----END CERTIFICATE-----
)EOF";

// Function declarations
void connectToWiFi();
void connectToMQTT();
void syncTime();
void mqttCallback(char *topic, byte *payload, unsigned int length);
bool judgeEqual(char *str1, const char *str2, int length);

void setup() {
    pinMode(LED, OUTPUT);
    Serial.begin(115200);
    connectToWiFi();
    syncTime();  // X.509 validation requires synchronization time
    mqtt_client.setServer(mqtt_broker, mqtt_port);
    mqtt_client.setCallback(mqttCallback);
    connectToMQTT();
}

void connectToWiFi() {
    WiFi.begin(ssid, password);
    while (WiFi.status() != WL_CONNECTED) {
        delay(1000);
        Serial.println("Connecting to WiFi...");
    }
    Serial.println("Connected to WiFi");
}

void syncTime() {
    configTime(gmt_offset_sec, daylight_offset_sec, ntp_server);
    Serial.print("Waiting for NTP time sync: ");
    while (time(nullptr) < 8 * 3600 * 2) {
        delay(1000);
        Serial.print(".");
    }
    Serial.println("Time synchronized");
    struct tm timeinfo;
    if (getLocalTime(&timeinfo)) {
        Serial.print("Current time: ");
        Serial.println(asctime(&timeinfo));
    } else {
        Serial.println("Failed to obtain local time");
    }
}

void connectToMQTT() {
    espClient.setCACert(ca_cert); // Load CA certificate
    while (!mqtt_client.connected()) {
        String client_id = "esp32-client-" + String(WiFi.macAddress());
        Serial.printf("Connecting to MQTT Broker as %s.....\n", client_id.c_str());
        if (mqtt_client.connect(client_id.c_str(), mqtt_username, mqtt_password)) {
            Serial.println("Connected to MQTT broker");
            mqtt_client.subscribe(mqtt_topic);
            // Publish message upon successful connection
            mqtt_client.publish(mqtt_topic, "Hi EMQX I'm ESP32 ^^");
        } else {
            Serial.print("Failed to connect to MQTT broker, rc=");
            Serial.println(mqtt_client.state());
            delay(5000);
        }
    }
}

void mqttCallback(char *topic, byte *payload, unsigned int length) {
    Serial.print("Message received on topic: ");
    Serial.print(topic);
    Serial.print("]: ");
    char judge[5];
    for (int i = 0; i < length; i++) {
        if (i >= 12) {
            judge[i - 12] = payload[i];
        }
        Serial.print((char)payload[i]);
    }
    if (judgeEqual(judge, mqtt_start, 5)) {
        Serial.print("Start signal received, turning LED on");
        digitalWrite(LED, HIGH); // Turn the LED on
    }
    if (judgeEqual(judge, mqtt_stop, 4)) {
        Serial.print("Stop signal received, turning LED off");
        digitalWrite(LED, LOW); // Turn the LED off
    }
    Serial.println();
}

bool judgeEqual(char *str1, const char *str2, int length) {
    for (int i = 0; i < length; i++) {
        if (str1[i] != str2[i]) return false;
    }
    return true;
}

void loop() {
    if (!mqtt_client.connected()) {
        connectToMQTT();
    }
    mqtt_client.loop();
}


```







```ini
; PlatformIO Project Configuration File
;
;   Build options: build flags, source filter
;   Upload options: custom upload port, speed and extra flags
;   Library options: dependencies, extra library storages
;   Advanced options: extra scripting
;
; Please visit documentation for the other options and examples
; https://docs.platformio.org/page/projectconf.html

[env:denky32]
platform = espressif32
board = esp32dev
framework = arduino

; Library dependencies
lib_deps = 
    PubSubClient
    arduino-libraries/NTPClient

monitor_speed = 115200 ; Set the serial monitor baud rate
upload_speed = 115200 ; Upload speed for flashing

build_flags =
    -D PIO_FRAMEWORK_ARDUINO_ESP32_BLE ; Optional, depending on usage

```

