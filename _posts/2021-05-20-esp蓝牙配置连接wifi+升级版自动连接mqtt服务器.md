---
title: esp蓝牙配置连接wifi+升级版自动连接mqtt服务器
tags: 
  - Esp32
  - Arduino
  - 物联网
---

# esp蓝牙配置连接wifi+升级版自动连接mqtt服务器

1.通过连接蓝牙再用手机app 去配置 wifi 
2.配置wifi 的格式 是json 格式

```json
{'ssid':'11111','password':'11111'}
```

```c
/*********************导入库*************************/
#include <BluetoothSerial.h>
#include <ArduinoJson.h>
#include <WiFi.h>
/**********************初始值************************/
#if !defined(CONFIG_BT_ENABLED) || !defined(CONFIG_BLUEDROID_ENABLED)
#error Bluetooth is not enabled! Please run `make menuconfig` to and enable it
#endif
static String data_app;
BluetoothSerial SerialBT;
//****************初始化联网*************************
bool init_wifi(){
    int count = 0;
    WiFi.begin();
    while(WiFi.status()!=WL_CONNECTED){
        Serial.print(".");
        delay(500);
        count++;
        if (count==20){
        return false;}}
    return true;
}
/**********************初始化*************************/
void setup() {
    Serial.begin(115200);
    WiFi.hostname("myESP32");
    SerialBT.begin("myESP32");
    Serial.println("现在可进行蓝牙配对!");
    if(init_wifi()){
        Serial.println();
        Serial.println("wifiok");
    }else{
        Serial.println();
        Serial.println("wifi连接失败请使用蓝牙配网");
    }
}
/**********************自循环************************/
void loop() {
    bluetooth();
}
/***********************蓝牙*************************/
void bluetooth() { //接收蓝牙数据
    if (Serial.available()) {               //用于调试
        SerialBT.write(Serial.read());
    }
    if (SerialBT.available()){
        data_app = "";
        data_app = SerialBT.readString();
        Serial.print("接收数据为：");        //用于调试
        Serial.println(data_app);           //用于调试
        if (data_app=="ip_mac"){
            String ip_mac = ipmac();
            SerialBT.println(ip_mac);
            Serial.println(ip_mac);
        }
        else if(data_app.indexOf("ssid")!=-1){
            StaticJsonDocument<200> doc;
            DeserializationError error = deserializeJson(doc, data_app);
            if (error) {
                Serial.println("数据格式错误");  //用于调试
                SerialBT.print("Incorrect format of incoming data");
            }
            else {
                String sid = doc["ssid"];
                String pwd = doc["password"];
                if (sid!="null"){
                    Serial.printf("SSID:%s\r\n",sid); //用于调试
                    if (pwd=="null"){
                        pwd = "";
                    }
                    Serial.printf("PAWD:%s\r\n",pwd); //用于调试
                    if(wifiPW(sid,pwd)){
                        SerialBT.println("True");
                    }else{
                        SerialBT.println("False");
                    }
                }
                else {
                    Serial.println("未识别到wifi数据"); //用于调试
                    SerialBT.print("Incorrect format of incoming data");
                }
            }
        }
        else{
            Serial.println("未接收到指令"); //用于调试
            SerialBT.print("Incorrect format of incoming data");
        }
    }
}
bool wifiPW(String sid,String pwd){//配网
    WiFi.begin(sid.c_str(), pwd.c_str());
    int count = 0;
    delay(1000);
    while (WiFi.status()!=WL_CONNECTED){
        Serial.print(".");
        delay(500);
        count++;
        if (count>20){
            Serial.println();
            Serial.println("配网失败");
            return false;}
    }
    Serial.println();
    Serial.println("配网成功");
    return true;
}
String ipmac(){//获取ip、mac地址
    if(WiFi.isConnected()){
        String ip = WiFi.localIP().toString();
        String mac = WiFi.macAddress();
        return "{'ip':'"+ip+"','mac':'"+mac+"'}";
    }else{
        return "Not connected to wifi";
    }
}
/****************************************************/
```
3.esp蓝牙配置wifi 连接 mqtt 配置
貌似   好像   大概   需要先用MQTT客户端先进行配置
![image-20231112222956183](https://cdn.jsdelivr.net/gh/YangSongL1n/img_bed/image-20231112222956183.png)
配置代码升级版

```c

#include <WiFi.h>
#include <BluetoothSerial.h>
#include <PubSubClient.h>
#include <ArduinoJson.h>

#if !defined(CONFIG_BT_ENABLED) || !defined(CONFIG_BLUEDROID_ENABLED)
#error Bluetooth is not enabled! Please run `make menuconfig` to and enable it
#endif
String ipmac(){//获取ip、mac地址
    if(WiFi.isConnected()){
        String ip = WiFi.localIP().toString();
        String mac = WiFi.macAddress();
        return "{'ip':'"+ip+"','mac':'"+mac+"'}";
    }else{
        return "Not connected to wifi";
    }
}
static String data_app;
BluetoothSerial SerialBT;

// MQTT Broker
const char *mqtt_broker = "broker.emqx.io";
const char *OutTopic = "test/esp32";
const char *InTopic = "test/pc";
const char *mqtt_username = "ESP32_test";
const char *mqtt_password = "14789632";
const int mqtt_port = 1883;
const int LED = 2;
WiFiClient espClient;
PubSubClient client(espClient);
void bluetooth() { //接收蓝牙数据
    if (Serial.available()) {               //用于调试
        SerialBT.write(Serial.read());
    }
    if (SerialBT.available()){
        data_app = "";
        data_app = SerialBT.readString();
        Serial.print("接收数据为：");        //用于调试
        Serial.println(data_app);           //用于调试
        if (data_app=="ip_mac"){
            String ip_mac = ipmac();
            SerialBT.println(ip_mac);
            Serial.println(ip_mac);
        }
        else if(data_app.indexOf("ssid")!=-1){
            StaticJsonDocument<200> doc;
            DeserializationError error = deserializeJson(doc, data_app);
            if (error) {
                Serial.println("数据格式错误");  //用于调试
                SerialBT.print("Incorrect format of incoming data");
            }
            else {
                String sid = doc["ssid"];
                String pwd = doc["password"];
                if (sid!="null"){
                    Serial.printf("SSID:%s\r\n",sid); //用于调试
                    if (pwd=="null"){
                        pwd = "";
                    }
                    Serial.printf("PAWD:%s\r\n",pwd); //用于调试
                    if(wifiPW(sid,pwd)){
                        SerialBT.println("True");
                    }else{
                        SerialBT.println("False");
                    }
                }
                else {
                    Serial.println("未识别到wifi数据"); //用于调试
                    SerialBT.print("Incorrect format of incoming data");
                }
            }
        }
        else{
            Serial.println("未接收到指令"); //用于调试
            SerialBT.print("Incorrect format of incoming data");
        }
    }
}
  bool wifiPW(String sid,String pwd){
    WiFi.begin(sid.c_str(), pwd.c_str());
    int count = 0;
    delay(1000);
    while (WiFi.status()!=WL_CONNECTED){
        Serial.print(".");
        delay(500);
        count++;
        if (count>20){
            Serial.println();
            Serial.println("配网失败");

            return false;}
    }
    Serial.println();
    Serial.println("配网成功");
    SerialBT.print("配网成功");
    //connecting to a mqtt broker
    client.setServer(mqtt_broker, mqtt_port);
    client.setCallback(callback);
    while (!client.connected()) {
     String client_id = "esp32-client-";
     client_id += String(WiFi.macAddress());
     Serial.printf("The client %s connects to the public mqtt broker\n", client_id.c_str());
     if (client.connect(client_id.c_str(), mqtt_username, mqtt_password)) {
         Serial.println("Public emqx mqtt broker connected");
     } else {
         Serial.print("failed with state ");
         Serial.print(client.state());
         delay(2000);
     }
 }
    // publish and subscribe
    client.publish(OutTopic, "我是LED");
    client.subscribe(InTopic);
}
  return true;
  }


  bool init_wifi(){
    int count = 0;
    WiFi.begin();
    while(WiFi.status()!=WL_CONNECTED){
        Serial.print(".");
        delay(500);
        count++;
        if (count==20){
        return false;}}
    return true;
}
  /**********************初始化*************************/
  void setup() {
    Serial.begin(115200);
    WiFi.hostname("myESP32");
    SerialBT.begin("myESP32");
    Serial.println("现在可进行蓝牙配对!");
    if(init_wifi()){
        Serial.println();
        Serial.println("wifiok");
    }else{
        Serial.println();
        Serial.println("wifi连接失败请使用蓝牙配网");
    }
}
  /**********************自循环************************/
  void loop() {
    client.loop();
    bluetooth();
}
  //觉得callback有点不需要小型被控制设备完全不需要看的执行动作就行了

  void callback(char *InTopic, byte *payload, unsigned int length) {
    Serial.print("Message arrived in topic: ");
    Serial.println(InTopic);
    Serial.print("Message:");
    for (int i = 0; i < length; i++) {
     Serial.print((char) payload[i]);
 }
    if ((char)payload[0] == '1') {     // 如果收到的信息以“1”为开始
     digitalWrite(LED, HIGH);  // 则点亮LED。
     client.publish(OutTopic, "我亮了");
 } else {                           
     digitalWrite(LED, LOW); // 否则熄灭LED。
     client.publish(OutTopic, "我灭了");
 }
    Serial.println();
    Serial.println("-----------------------");
}

```