# ISR
----

> GPIO端口用作外部中断

## 安装ISR服务

```c
gpio_install_isr_service(ESP_INTR_FLAG_DEFAULT);
```

## ISR初始化

```c
// zero-initialize the config structure.
gpio_config_t io_conf = {};

// interrupt of rising edge
io_conf.intr_type = GPIO_INTR_NEGEDGE;
io_conf.pin_bit_mask = 1ULL << GPIO_NUM_7 ;
// set as input mode
io_conf.mode = GPIO_MODE_INPUT;
// enable pull-up mode
io_conf.pull_up_en = 0;
gpio_config(&io_conf);
```

## 设置中断

### 添加中断

```c
gpio_isr_handler_add(GPIO_NUM_7, gpio_isr_handler, NULL);
```
将`gpio_isr_handler`中断处理函数链接到`GPIO_NUM_7`触发的中断, 当中断触发时, 调用该函数.

### 移除中断

```c
gpio_isr_handler_remove(GPIO_NUM_7);
```
移除该引脚设置的中断

## 编写中断处理函数

```c
static void IRAM_ATTR gpio_isr_handler(void *arg)
{
    // some code.
}
```
