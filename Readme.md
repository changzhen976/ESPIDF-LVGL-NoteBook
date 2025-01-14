# Readme
----

本仓库用于记录`ESP-IDF`学习中遇到的问题及解决方法
主要分为几大方面

- [工程配置](./Project-Configure/Readme.md): 用于IDF工程配置, 添加组件依赖, 解决编译中遇到的问题等
    - [IDF命令行工具](./Project-Configure/cmd-tool/Readme.md)
    - [CMakeLists配置](./Project-Configure/CMakeLists.md)
    - [Menuconfig配置](./Project-Configure/KConfig.md)
- [GPIO配置](./GPIO/Readme.md): IO口的常用配置方法
    - [普通输入输出](./Normal_IO.md): 常规IO口设置
    - [外部中断](./ISR.md): IO口外部中断
    - [编码器正交计数](./Pcnt.md): 解析正交编码器计数
- [WiFi配置](./WiFi/Readme.md): WiFi初始化,连接AP, 以及基于WiFi的其他协议 如MQTT HTTP 等
    - [WiFi连接](./WiFi-Init.md): WiFi连接
    - [MQTT](./MQTT.md): MQTT客户端配置
    - [OTA](./OTA/Readme.md): OTA设置
        - [OTA准备工作](./OTA相关准备工作.md): 例程代码, 用于发布软件的服务器 等
        - [OTA例程代码解析](./OTA例程代码解析.md)
        - [分区表设置](./esp32分区表.md): ESP32分区表的使用与自定义
        - [软件版本设置](./软件版本设定.md)
- [LVGL配置](./LVGL/Readme.md): LVGL学习笔记
- [BUS总线配置](./BUS/Readme.md): 常用总线配置 SPI UART IIC CAN 等