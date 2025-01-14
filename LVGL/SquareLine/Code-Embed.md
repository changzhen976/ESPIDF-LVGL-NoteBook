# Code-Embed
----

## 代码集成

### 仅UI代码生成

> 代码导出时会生成到指定文件夹内

1. `main`目录中新建`ui`文件夹, 将SquareLine生成的代码放入
2. 修改`./main/CMakeLists.txt`
    - 在顶部新增一行指令: `file(GLOB_RECURSE SRC_UI ${CMAKE_SOURCE_DIR} "./ui/*.c")`
    - `register`指定`INCLUDE_DIRS`添加`ui`: `idf_component_register(SRCS "main.c" ${SRC_UI} INCLUDE_DIRS "." "ui")`
    



> CMake [添加当前目录及其子目录下的所有c文件列表到lib_srcs变量中命令](https://cmake.org/cmake/help/latest/command/file.html#glob-recurse)
> `file(GLOB_RECURSE lib_srcs *.c)` 含义: 添加当前目录及其子目录下的所有c文件列表到lib_srcs变量中
> `GLOB` 指定文件夹内匹配的文件; `GLOB_RECURSE`: 指定文件夹及其子文件夹内所有匹配的文件
>
> main文件夹中`CMakeLists.txt` `SRCS`添加上面引入的`SRC_UI`, `INCLUDE_DIRS`路径中添加由ui文件夹定义的library `ui`

### 工程生成

> SquareLine可以导出工程直接编译烧录测试, 使用新建工程时指定的开发板所引用的例程生成工程
> 可通过[自定义开发板](./Custom-Board.md)快捷测试页面实际效果