# Configure

----

## What is Menuconfig

> [kconfig-reference Online](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/kconfig-reference.html)


### Where to Config

#### 1 In cmd window

```shell
idf.py menuconfig
```

![cmd Memuconfig window](vx_images/424077040558017.png)

#### 2 In VS Code extention

```shell
>ESP-IDF:SDK Configuration Editor(Menuconfig)
```
![Open Menuconfig Page](vx_images/79514290782052.png)

#### 3 config saved path

`'project-path'/build/sdkconfig`

![config path](vx_images/413585765068847.png)

## What is Menuconfig



## How to Write

### Kconfig.projbuild

#### An example

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


#### Format

##### Menu

```
menu "menuString"  
    ...
endmenu
```  

It will display in SdkConfig Editor

![Memu config display](vx_images/280536399959583.png)

##### Option

```
config SOME_CFG_KEY_WORD    
    string "string key word" / Bool / some else    ' cfg data type

```
Once Configed, a key word will be seved in `sdkconfig` as `CONFIG_SOME_CFG_KEY_WORD`

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



### build/sdkconfig

```
#
# Example Product Configuration
#
CONFIG_EXAMPLE_PRODUCT_NAME="Blinky Development Test01"
# CONFIG_EXAMPLE_FUNC_IMPL_DEV is not set
CONFIG_EXAMPLE_FUNC_IMPL_PROD=y
# end of Example Product Configuration

```