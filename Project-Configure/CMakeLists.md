# CMakeLists

IDF基于CMake链接编译

## 主文件夹

```cmake
cmake_minimum_required(VERSION 3.16)

include($ENV{IDF_PATH}/tools/cmake/project.cmake)

set(EXTRA_COMPONENT_DIRS "./block_setup")

project(TTGO-LVGL-example)
```

必要语句`cmake_minimum_required(VERSION 3.16)`​ `include($ENV{IDF_PATH}/tools/cmake/project.cmake)`​ `project(TTGO-LVGL-example)`​，这三句为最小定义，设置cmake最低版本，include cmake文件，定义功能名称

​`set(EXTRA_COMPONENT_DIRS "./block_setup")`​ `EXTRA_COMPONENT_DIRS`​为设置额外工程组件库路径，默认为空；

IDF默认包含组件路径`COMPONENT_DIRS`​为`./components`​，该文件夹下的组件不需要在主文件夹下CMake文件额外设置

## Main文件夹

```cmake
idf_component_register(SRCS "TTGO_LVGL_example_main.c"
                       INCLUDE_DIRS ".")
```

​`idf_component_register`​ IDF注册组件，idf下各子组件必要代码

## 组件文件夹

```cmake
idf_component_register(SRCS "lv_port_indev.c"
                    INCLUDE_DIRS "."
                    REQUIRES driver lvgl MultiButton)
```

​`REQUIRES`​设置依赖，如果组件内引用了其他组件功能，需要设置，例如调用GPIO需要包含`driver`​，设置wifi需要包含`esp_wifi`​，组件名为组件所在文件夹的名字，自定义组件也可调用，比如MultiButton组件在功能路径为`./components/MultiButton`​，若当前模块使用到了其中函数，则需要`REQUIRES MultiButton`​

‍
