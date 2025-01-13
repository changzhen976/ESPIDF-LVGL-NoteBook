# IDF基础命令行

# 命令行编译流程

## 命令行加载IDF相关路径

```cmd
. %IDF_PATH%/export.bat
```

> 注意前面的`.`​

‍

## 设置目标芯片

```cmd
idf.py set-target esp32
```

## 配置工程相关参数

```cmd
idf.py menuconfig
```

> menuconfig相关见[Menuconfig](../KConfig.md)

## build编译

```cmd
idf.py build
```

## 设置刷写端口并刷写

```cmd
idf.py -p PORT flash
```

## 监控

```cmd
idf.py -p PORT monitor
```
