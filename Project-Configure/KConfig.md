# KConfigure编译配置

----

## Menuconfig是什么

> [kconfig在线参考手册](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/kconfig-reference.html)
> 通过配置kconfig可以配置一系列编译选项, 其通过define一些常量, 在代码中可通过添加`#ifdf``#ifndf`等来控制代码的编译
> 比如可灵活切换开发模式和释放模式, 设置硬件参数或者是否启用一些模块, 可设置默认配置与配置依赖
> Menuconfig开通过命令行调用设置, VSCode中可通过插件UI配置


## IDF工程中如何配置

### 1 命令行窗口配置

在工程目录下通过命令行开启menuconfig设置

```shell
idf.py menuconfig
```

![cmd Memuconfig window](vx_images/424077040558017.png)

### 2 VSCode插件中配置

VSCode命令窗口中打开

```shell
>ESP-IDF:SDK Configuration Editor(Menuconfig)
```

插件中也可直接打开

![Open Menuconfig Page](vx_images/79514290782052.png)

### 3 生成配置文件保存路径

默认路径存在build文件夹下

`'project-path'/build/sdkconfig`

![config path](vx_images/413585765068847.png)

## 如何编写Menuconfig文件

### Kconfig.projbuild编写

> 将该文件保存在工程目录中, 位置没有强行规定, 一版工程主配置在main文件夹中
> 组件也可配置Kconfig文件放置与组件文件夹内

#### 举个例子

Located in Main folder, it config project property

```kconfig
menu "Example Product Configuration"

    config EXAMPLE_PRODUCT_NAME
        string "Product name"
        default "Not set"
        help
            Product name used in the example

    choice EXAMPLE_FUNC_IMPL
        prompt "Implementation of function 'func'"
        help
            Select one of the implementations of 'func' to be used in the app.
            This setting is used in component CMakeLists.txt.

        config EXAMPLE_FUNC_IMPL_DEV
            bool "Development (func_dev.c)"
        config EXAMPLE_FUNC_IMPL_PROD
            bool "Production (func_prod.c)"
    endchoice
endmenu
```


#### 格式

##### Menu

> 最外层包围, menu字符串将会显示在Menuconfig首页中, 进入该选项配置该文件中的设置

```
menu "menuString"  
    ...
endmenu
```  


![Memu config display](vx_images/280536399959583.png)

##### Option

```
config SOME_CFG_KEY_WORD    
    string "string key word" / Bool / some else    ' cfg data type

```
一旦设置, 该关键字将会在 `sdkconfig` 中添加`CONFIG_`前缀被定义为 `CONFIG_SOME_CFG_KEY_WORD`

![String Option Config Display](vx_images/234166785030276.png)


##### Choice

```
choice EXAMPLE_FUNC_IMPL
    prompt "Implementation of function 'func'"
    help
        Select one of the implementations of 'func' to be used in the app.
        This setting is used in component CMakeLists.txt.

    config EXAMPLE_FUNC_IMPL_DEV
        bool "Development (func_dev.c)"
    config EXAMPLE_FUNC_IMPL_PROD
        bool "Production (func_prod.c)"
endchoice
```

![choice display](vx_images/585057650547273.png)

![choice inside](vx_images/247316513418709.png)



### 输出的文件: `build/sdkconfig`

```
#
# Example Product Configuration
#
CONFIG_EXAMPLE_PRODUCT_NAME="Blinky Development Test01"
# CONFIG_EXAMPLE_FUNC_IMPL_DEV is not set
CONFIG_EXAMPLE_FUNC_IMPL_PROD=y
# end of Example Product Configuration

```

### 代码中实现

```c
#ifdef CONFIG_EXAMPLE_FUNC_IMPL_PROD
    const char *projName = CONFIG_EXAMPLE_PRODUCT_NAME
#elif
    const char *projName = "Dev Project"
#endif
```