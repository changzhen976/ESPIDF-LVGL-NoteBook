# 正交编码器解析
----

> 编码器正交计数

## 新建pcunt_unit

新建unit handle

```c
static pcnt_unit_handle_t pcnt_unit = NULL;
```

新建配置

```c
pcnt_unit_config_t unit_config = {
        .high_limit = EXAMPLE_PCNT_HIGH_LIMIT,
        .low_limit = EXAMPLE_PCNT_LOW_LIMIT,
    };
```

将配置写入handle

```c
ESP_ERROR_CHECK(pcnt_new_unit(&unit_config, &pcnt_unit));
```

## 配置滤波参数

```c
pcnt_glitch_filter_config_t filter_config = {
    .max_glitch_ns = 1000,
};
ESP_ERROR_CHECK(pcnt_unit_set_glitch_filter(pcnt_unit, &filter_config));
```

## 新建频道并配置计数action

```c
ESP_LOGI("Encoder Init", "install pcnt channels");
pcnt_chan_config_t chan_a_config = {
    .edge_gpio_num = ENCODER_A_GPIO,
    .level_gpio_num = ENCODER_B_GPIO,
};
pcnt_channel_handle_t pcnt_chan_a = NULL;
ESP_ERROR_CHECK(pcnt_new_channel(pcnt_unit, &chan_a_config, &pcnt_chan_a));
pcnt_chan_config_t chan_b_config = {
    .edge_gpio_num = ENCODER_B_GPIO,
    .level_gpio_num = ENCODER_A_GPIO,
};
pcnt_channel_handle_t pcnt_chan_b = NULL;
ESP_ERROR_CHECK(pcnt_new_channel(pcnt_unit, &chan_b_config, &pcnt_chan_b));

ESP_LOGI("Encoder Init", "set edge and level actions for pcnt channels");
ESP_ERROR_CHECK(pcnt_channel_set_edge_action(pcnt_chan_a, PCNT_CHANNEL_EDGE_ACTION_DECREASE, PCNT_CHANNEL_EDGE_ACTION_INCREASE));
ESP_ERROR_CHECK(pcnt_channel_set_level_action(pcnt_chan_a, PCNT_CHANNEL_LEVEL_ACTION_KEEP, PCNT_CHANNEL_LEVEL_ACTION_INVERSE));
ESP_ERROR_CHECK(pcnt_channel_set_edge_action(pcnt_chan_b, PCNT_CHANNEL_EDGE_ACTION_INCREASE, PCNT_CHANNEL_EDGE_ACTION_DECREASE));
ESP_ERROR_CHECK(pcnt_channel_set_level_action(pcnt_chan_b, PCNT_CHANNEL_LEVEL_ACTION_KEEP, PCNT_CHANNEL_LEVEL_ACTION_INVERSE));

```
分别配置`A``B`项channel, `edge_gpio_num`, `level_gpio_num`相反, 并设置edge level action

## 设置观测点

```c
int watch_points[] = {EXAMPLE_PCNT_LOW_LIMIT, -50, 0, 50, EXAMPLE_PCNT_HIGH_LIMIT};
for (size_t i = 0; i < sizeof(watch_points) / sizeof(watch_points[0]); i++) {
    // 设置观测点, 当编码器值到达该值时调用观测点回调函数
    ESP_ERROR_CHECK(pcnt_unit_add_watch_point(pcnt_unit, watch_points[i]));
}
pcnt_event_callbacks_t cbs = {
    // 设置观测点回调函数
    .on_reach = example_pcnt_on_reach,
};
// 新建一个队列, 用于回调函数发送数据(使用队列可减少中断处理时间, 后续任务由其他任务执行)
queue = xQueueCreate(10, sizeof(int));
// 注册观测点回调事件, 传入队列
ESP_ERROR_CHECK(pcnt_unit_register_event_callbacks(pcnt_unit, &cbs, queue));
```

配置观测点

```c
static bool example_pcnt_on_reach(pcnt_unit_handle_t unit, const pcnt_watch_event_data_t *edata, void *user_ctx)
{
    BaseType_t high_task_wakeup;
    // 获取队列
    QueueHandle_t queue = (QueueHandle_t)user_ctx;
    // send event data to queue, from this interrupt callback
    xQueueSendFromISR(queue, &(edata->watch_point_value), &high_task_wakeup);
    return (high_task_wakeup == pdTRUE);
}
```

编写观测点触发函数


```c
static int event_count = 0;

if (xQueueReceive(queue, &event_count, 0)) {
        ESP_LOGI("TAG", "Watch point event, count: %d", event_count);
    }
```

其他阻塞轮询函数处理队列信息

## 使能Pcnt

```c
ESP_LOGI("Encoder Init", "enable pcnt unit");
ESP_ERROR_CHECK(pcnt_unit_enable(pcnt_unit));
ESP_LOGI("Encoder Init", "clear pcnt unit");
ESP_ERROR_CHECK(pcnt_unit_clear_count(pcnt_unit));
ESP_LOGI("Encoder Init", "start pcnt unit");
ESP_ERROR_CHECK(pcnt_unit_start(pcnt_unit));
```

使能, 清空计数值, 开始计数

## 获取计数值

```c
static int pulse_count = 0;

ESP_ERROR_CHECK(pcnt_unit_get_count(pcnt_unit, &pulse_count));
```

