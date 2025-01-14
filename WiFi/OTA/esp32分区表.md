# esp32 分区表

> [官网分区表描述](https://docs.espressif.com/projects/esp-idf/zh_CN/stable/esp32/api-guides/partition-tables.html#id2)

# 分区表描述

esp32 Flash默认偏移地址为`0x8000`​

分区表的长度为 0xC00 字节，最多可以保存 95 条分区表条目。MD5 校验和附加在分区表之后，用于在运行时验证分区表的完整性。分区表占据了整个 flash 扇区，大小为 0x1000 (4 KB)。因此，它后面的任何分区至少需要位于 `0x8000`​ + `0x1000`​ 处。

# 分区表设置

分区表中的每个条目都包括以下几个部分：Name（标签）、Type（app、data 等）、SubType 以及在 flash 中的偏移量（分区的加载地址）

menuconfig中可以设置[分区表类型](https://docs.espressif.com/projects/esp-idf/zh_CN/stable/esp32/api-reference/kconfig.html#config-partition-table-type)：`PARTITION_TABLE_TYPE`​

## 内置分区表

### 单工厂程序分区，无OTA分区

```csv
# ESP-IDF Partition Table
# Name,   Type, SubType, Offset,  Size,   Flags
nvs,      data, nvs,     0x9000,  0x6000,
phy_init, data, phy,     0xf000,  0x1000,
factory,  app,  factory, 0x10000, 1M,
```

### 单工厂程序分区，2个OTA分区

```csv
# ESP-IDF Partition Table
# Name,   Type, SubType, Offset,  Size, Flags
nvs,      data, nvs,     0x9000,  0x4000,
otadata,  data, ota,     0xd000,  0x2000,
phy_init, data, phy,     0xf000,  0x1000,
factory,  app,  factory, 0x10000,  1M,
ota_0,    app,  ota_0,   0x110000, 1M,
ota_1,    app,  ota_1,   0x210000, 1M,
```

## 自定义分区表

```csv
# Name,   Type, SubType,  Offset,   Size,  Flags
nvs,      data, nvs,      0x9000,  0x4000
otadata,  data, ota,      0xd000,  0x2000
phy_init, data, phy,      0xf000,  0x1000
factory,  app,  factory,  0x10000,  1M
ota_0,    app,  ota_0,    ,         1M
ota_1,    app,  ota_1,    ,         1M
nvs_key,  data, nvs_keys, ,        0x1000
```

* 字段之间的空格会被忽略，任何以 `#`​ 开头的行（注释）也会被忽略。
* CSV 文件中的每个非注释行均为一个分区定义。
* 每个分区的 `Offset`​ 字段可以为空，`gen_esp32part.py`​ 工具会从分区表位置的后面开始自动计算并填充该分区的偏移地址，同时确保每个分区的偏移地址正确对齐。

如果在`menuconfig`​中选取了`Custom partition table CSV`​，该自定义csv文件需放入工程文件并设置路径

‍
