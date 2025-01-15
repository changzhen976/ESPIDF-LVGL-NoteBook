# 硬件输入设备接入
----

## 设备类型

### 按键-Button

模拟屏幕上固定点位的点击
**不建议使用, 局限性很大**

### 键盘-Keypad

### 编码器-Encoder

## `lv_port_indev_init()` 代码解析

### 硬件初始化

初始化硬件输入, 配置IO口上下拉, 是否使用中断等

### 注册设备

```c
static lv_indev_drv_t indev_drv;
/*Register a keypad input device*/
lv_indev_drv_init(&indev_drv);
indev_drv.type = LV_INDEV_TYPE_KEYPAD;
indev_drv.read_cb = keypad_read;
indev_keypad = lv_indev_drv_register(&indev_drv);
```
- `type`: 用于确认设备类型
- `read_cb`: 获取键值的回调函数

### 回调函数读取键值
 
```c
static void keypad_read(lv_indev_drv_t *indev_drv, lv_indev_data_t *data)

/** Data structure passed to an input driver to fill*/
typedef struct {
    lv_point_t point; /**< For LV_INDEV_TYPE_POINTER the currently pressed point*/
    uint32_t key;     /**< For LV_INDEV_TYPE_KEYPAD the currently pressed key*/
    uint32_t btn_id;  /**< For LV_INDEV_TYPE_BUTTON the currently pressed button*/
    int16_t enc_diff; /**< For LV_INDEV_TYPE_ENCODER number of steps since the previous read*/

    lv_indev_state_t state; /**< LV_INDEV_STATE_REL or LV_INDEV_STATE_PR*/
    bool continue_reading;  /**< If set to true, the read callback is invoked again*/
} lv_indev_data_t;
```
> 回调函数返回键值, 不同设备对应`lv_indev_data_t`中的不同元素

## UI代码中的使用
