---
title: esp8266连接服务器（基于mosquitto的mqtt server）
date: 2024-02-09 00:39:50
tags: [笔记, 嵌入式, esp8266, mosquitto,  mqtt, linux, IoT,物联网] 
---

* Time：2024-02-09 00:39:50

* 还挺兴奋的，这个项目算是我自己搭建云服务器的最后一步。

# idea

​	起因是想要自己搭建云服务器，同时还想要利用手上已经有的esp8266开发板实现对该服务器的远程控制（开关机）。

​	本来打算整一个备用电源给服务器供电，奈何这台服务器是普通PC，即使配置已经很低（与现代PC比较为拉胯，但是比大多数出售的服务器例如某云2核4g更加）但功耗的保守估计已经超过70w。

​	于是便打算使用远程开关降低平均功耗。同时进行物联网方面知识的扫盲与学习。

# 过程

## 1.前期准备

​	首先，在已经搭建好的mqtt服务器上创建了用户`user`和密码`passwd`，同时处于安全性考虑禁止匿名访问。[配置mqtt服务器的过程](https://regenm.gitee.io/2024/02/09/MQTT服务器搭建（mosquitto）/))

​	创建好测试主题（topic）`test`后进行测试。多平台测试收发message没问题。

## 2.中期打BOSS

​	接下来就是攻克esp8266这边的问题了。

* 问题一：esp8266开发环境

​	由于这块esp8266开发板是我在高中时期玩的，那时候的电脑早已坏掉，这台电脑的arduinoIDE 还未配置esp8266的环境。

​	本来打算使用platformIO但是太慢了，在下载框架以及支持的时候解决了ide的问题。。。

> 需要配置：
>
> * 添加支持esp8266开发板 
>
> 方法一：ide设置里的 Additional boards manager URLs添加`http://arduino.esp8266.com/stable/package_esp8266com_index.json`
>
> 方法二：手动添加：下载esp8266_package_installer直接离线下载
>
> 
>
> * 增加库：
>   * `<PubSubClient.h>`
>   * `ESP8266WiFi.h`

* 问题二：esp8266连接不成功

```
MQTT Server Connect Failed. Client State:-2
MQTT Server Connect Failed. Client State:-2
MQTT Server Connect Failed. Client State:-2
MQTT Server Connect Failed. Client State:-2
MQTT Server Connect Failed. Client State:-2
MQTT Server Connect Failed. Client State:-2
```

​		其实主要是平台的问题，8266，以及PubSubClient.h库中的案例程序和大多数网上教程都是使用的别人的mqtt服务器，他们往往不需要提供账号密码等等。例如

```cpp
/*
 Basic MQTT example

 This sketch demonstrates the basic capabilities of the library.
 It connects to an MQTT server then:
  - publishes "hello world" to the topic "outTopic"
  - subscribes to the topic "inTopic", printing out any messages
    it receives. NB - it assumes the received payloads are strings not binary

 It will reconnect to the server if the connection is lost using a blocking
 reconnect function. See the 'mqtt_reconnect_nonblocking' example for how to
 achieve the same result without blocking the main loop.
 
*/

#include <SPI.h>
#include <Ethernet.h>
#include <PubSubClient.h>

// Update these with values suitable for your network.
byte mac[]    = {  0xDE, 0xED, 0xBA, 0xFE, 0xFE, 0xED };
IPAddress ip(172, 16, 0, 100);
IPAddress server(172, 16, 0, 2);

void callback(char* topic, byte* payload, unsigned int length) {
  Serial.print("Message arrived [");
  Serial.print(topic);
  Serial.print("] ");
  for (int i=0;i<length;i++) {
    Serial.print((char)payload[i]);
  }
  Serial.println();
}

EthernetClient ethClient;
PubSubClient client(ethClient);

void reconnect() {
  // Loop until we're reconnected
  while (!client.connected()) {
    Serial.print("Attempting MQTT connection...");
    // Attempt to connect
      /*----------------------------区别（报错位置）---------------------------*/
    if (client.connect("arduinoClient")) {
      /*----------------------------区别（报错位置）---------------------------*/
      Serial.println("connected");
      // Once connected, publish an announcement...
      client.publish("outTopic","hello world");
      // ... and resubscribe
      client.subscribe("inTopic");
    } else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 5 seconds");
      // Wait 5 seconds before retrying
      delay(5000);
    }
  }
}

void setup()
{
  Serial.begin(57600);

  client.setServer(server, 1883);
  client.setCallback(callback);

  Ethernet.begin(mac, ip);
  // Allow the hardware to sort itself out
  delay(1500);
}

void loop()
{
  if (!client.connected()) {
    reconnect();
  }
  client.loop();
}

```

​	以及太极创客的代码

```cpp
/**********************************************************************
项目名称/Project          : 零基础入门学用物联网
程序名称/Program name     : publish_ranye_url
团队/Team                : 太极创客团队 / Taichi-Maker (www.taichi-maker.com)
作者/Author              : CYNO朔
日期/Date（YYYYMMDD）     : 20200813
程序目的/Purpose          : 
本程序旨在演示如何使用PubSubClient库使用ESP8266向MQTT服务器发布信息。
-----------------------------------------------------------------------
本示例程序为太极创客团队制作的《零基础入门学用物联网》中示例程序。
该教程为对物联网开发感兴趣的朋友所设计和制作。如需了解更多该教程的信息，请参考以下网页：
http://www.taichi-maker.com/homepage/esp8266-nodemcu-iot/iot-c/esp8266-nodemcu-web-client/http-request/
***********************************************************************/
#include <ESP8266WiFi.h>
#include <PubSubClient.h>
#include <Ticker.h>

// 设置wifi接入信息(请根据您的WiFi信息进行修改)
const char* ssid = "lkw";
const char* password = "01234567890";
const char* mqttServer = "192.168.43.61";

// 如以上MQTT服务器无法正常连接，请前往以下页面寻找解决方案
// http://www.taichi-maker.com/public-mqtt-broker/

Ticker ticker;
WiFiClient wifiClient;
PubSubClient mqttClient(wifiClient);

int count;    // Ticker计数用变量

void setup() {
  Serial.begin(9600);
  
  //设置ESP8266工作模式为无线终端模式
  WiFi.mode(WIFI_STA);
  
  // 连接WiFi
  connectWifi();
  
  // 设置MQTT服务器和端口号
  mqttClient.setServer(mqttServer, 1883);

  // 连接MQTT服务器
  connectMQTTServer();

  // Ticker定时对象
  ticker.attach(1, tickerCount);  
}

void loop() { 
  if (mqttClient.connected()) { // 如果开发板成功连接服务器
    // 每隔3秒钟发布一次信息
    if (count >= 3){
      pubMQTTmsg();
      count = 0;
    }    
    // 保持心跳
    mqttClient.loop();
  } else {                  // 如果开发板未能成功连接服务器
    connectMQTTServer();    // 则尝试连接服务器
  }
}

void tickerCount(){
  count++;
}
/*----------------------------区别（报错位置）---------------------------*/
void connectMQTTServer(){
  // 根据ESP8266的MAC地址生成客户端ID（避免与其它ESP8266的客户端ID重名）
  String clientId = "esp8266-" + WiFi.macAddress();

  // 连接MQTT服务器
  if (mqttClient.connect(clientId.c_str())) { 
/*----------------------------区别（报错位置）---------------------------*/
    Serial.println("MQTT Server Connected.");
    Serial.println("Server Address: ");
    Serial.println(mqttServer);
    Serial.println("ClientId:");
    Serial.println(clientId);
  } else {
    Serial.print("MQTT Server Connect Failed. Client State:");
    Serial.println(mqttClient.state());
    delay(3000);
  }   
}

// 发布信息
void pubMQTTmsg(){
  static int value; // 客户端发布信息用数字

  String topicString = "lkwMQTTtopic";
  
  char publishTopic[topicString.length() + 1];  
  strcpy(publishTopic, topicString.c_str());

  // 建立发布信息。信息内容以Hello World为起始，后面添加发布次数。
  String messageString = "Hello World " + String(value++); 
  char publishMsg[messageString.length() + 1];   
  strcpy(publishMsg, messageString.c_str());
  
  // 实现ESP8266向主题发布信息
  if(mqttClient.publish(publishTopic, publishMsg)){
    Serial.println("Publish Topic:");Serial.println(publishTopic);
    Serial.println("Publish message:");Serial.println(publishMsg);    
  } else {
    Serial.println("Message Publish Failed."); 
  }
}

// ESP8266连接wifi
void connectWifi(){

  WiFi.begin(ssid, password);
 
  //等待WiFi连接,成功连接后输出成功信息
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.print(".");
  }
  Serial.println("");
  Serial.println("WiFi Connected!");  
  Serial.println(""); 
}

```

解决方法：查看`PubSubClient`的源代码

幸亏结构挺简单，很容易就找到了想要的代码即`PubSubClient的connect方法`

* 在`PubSubClient.h`

![PubSubClientH](/images/esp8266_mqtt/PubSubClientH.png)

```cpp
 boolean connect(const char* id);
   boolean connect(const char* id, const char* user, const char* pass);
   boolean connect(const char* id, const char* willTopic, uint8_t willQos, boolean willRetain, const char* willMessage);
   boolean connect(const char* id, const char* user, const char* pass, const char* willTopic, uint8_t willQos, boolean willRetain, const char* willMessage);
   boolean connect(const char* id, const char* user, const char* pass, const char* willTopic, uint8_t willQos, boolean willRetain, const char* willMessage, boolean cleanSession);
   void disconnect();
```

在`PubSubClient.cpp`

![PubSubClientCPP](/images/esp8266_mqtt/PubSubClientCPP.png)

```cpp

boolean PubSubClient::connect(const char *id) {
    return connect(id,NULL,NULL,0,0,0,0,1);
}

boolean PubSubClient::connect(const char *id, const char *user, const char *pass) {
    return connect(id,user,pass,0,0,0,0,1);
}

boolean PubSubClient::connect(const char *id, const char* willTopic, uint8_t willQos, boolean willRetain, const char* willMessage) {
    return connect(id,NULL,NULL,willTopic,willQos,willRetain,willMessage,1);
}

boolean PubSubClient::connect(const char *id, const char *user, const char *pass, const char* willTopic, uint8_t willQos, boolean willRetain, const char* willMessage) {
    return connect(id,user,pass,willTopic,willQos,willRetain,willMessage,1);
}

boolean PubSubClient::connect(const char *id, const char *user, const char *pass, const char* willTopic, uint8_t willQos, boolean willRetain, const char* willMessage, boolean cleanSession) {
    if (!connected()) {
        int result = 0;


        if(_client->connected()) {
            result = 1;
        } else {
            
            /******************省略*****************/
```

​	于是就得出 connect 重载函数本体只有一个，即最后一个，我可以修改输入的参数实现自己想要的功能。

```cpp
boolean PubSubClient::connect(const char *id, const char *user, const char *pass) {
    return connect(id,user,pass,0,0,0,0,1);
}
```

## 3.结尾

先附上自己的代码，修改了连接模板代码 部分。

```cpp
#include <ESP8266WiFi.h>
#include <PubSubClient.h>
#include <Ticker.h>

// 设置wifi接入信息(请根据您的WiFi信息进行修改)
const char* ssid = "MERCURY_F80E";
const char* password = "********";
const char* mqttServer = "regenm.gitee.io";

Ticker ticker;
WiFiClient wifiClient;
PubSubClient mqttClient(wifiClient);

int count;  // Ticker计数用变量

void setup() {
  Serial.begin(9600);

  //设置ESP8266工作模式为无线终端模式
  WiFi.mode(WIFI_STA);

  // 连接WiFi
  connectWifi();

  // 设置MQTT服务器和端口号
  mqttClient.setServer(mqttServer, 1883);

  // 连接MQTT服务器
  connectMQTTServer();

  // Ticker定时对象
  ticker.attach(1, tickerCount);
}

void loop() {
  if (mqttClient.connected()) {  // 如果开发板成功连接服务器
    // 每隔3秒钟发布一次信息
    if (count >= 3) {
      pubMQTTmsg();
      count = 0;
    }
    // 保持心跳
    mqttClient.loop();
  } else {                // 如果开发板未能成功连接服务器
    connectMQTTServer();  // 则尝试连接服务器
  }
}

void tickerCount() {
  count++;
}

void connectMQTTServer() {
  // 根据ESP8266的MAC地址生成客户端ID（避免与其它ESP8266的客户端ID重名）
  String clientId = "regen";
  String userRegen = "user";
  String passwdRegen = "passwd";
  // 连接MQTT服务器 
    
  /* --------------------connect(id,user,pass,0,0,0,0,1)-------------------- */
  if (mqttClient.connect(clientId.c_str(),userRegen.c_str() ,passwdRegen.c_str() )) {
    Serial.println("MQTT Server Connected.");
    Serial.println("Server Address: ");
    Serial.println(mqttServer);
    Serial.println("ClientId:");
    Serial.println(clientId);
  } else {
    Serial.print("MQTT Server Connect Failed. Client State:");
    Serial.println(mqttClient.state());
    delay(3000);
  }
}

// 发布信息
void pubMQTTmsg() {
  static int value;  // 客户端发布信息用数字

  String topicString = "test";

  char publishTopic[topicString.length() + 1];
  strcpy(publishTopic, topicString.c_str());

  // 建立发布信息。信息内容以Hello World为起始，后面添加发布次数。
  String messageString = "Hello World " + String(value++);
  char publishMsg[messageString.length() + 1];
  strcpy(publishMsg, messageString.c_str());

  // 实现ESP8266向主题发布信息
  if (mqttClient.publish(publishTopic, publishMsg)) {
    Serial.println("Publish Topic:");
    Serial.println(publishTopic);
    Serial.println("Publish message:");
    Serial.println(publishMsg);
  } else {
    Serial.println("Message Publish Failed.");
  }
}

// ESP8266连接wifi
void connectWifi() {

  WiFi.begin(ssid, password);

  //等待WiFi连接,成功连接后输出成功信息
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.print(".");
  }
  Serial.println("");
  Serial.println("WiFi Connected!");
  Serial.println("");
}
```



​	经过测试后成功，附上截图：

![success](/images/esp8266_mqtt/success1.png)

![success](/images/esp8266_mqtt/success2.jpg)

![success](/images/esp8266_mqtt/success3.png)
