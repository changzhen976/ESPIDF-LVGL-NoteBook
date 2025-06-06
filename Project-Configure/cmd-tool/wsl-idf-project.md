# 使用wsl配置IDF开发环境

----

> Linux环境下IDF的编译速度明显优于Windows环境, 所以可以配置wsl-idf开发环境
> wsl开发环境配置过程见官网Linux环境搭建步骤

## VSCode连接wsl环境

> 略

## wsl共享Windows USB端口

> wsl无法共享宿主机的usb接口, 如果需要下载调式软件, 需要借助额外工具

### [usbipd](https://github.com/dorssel/usbipd-win)

**设置步骤:**

1. 列出USB设备
    - `usbipd list`
2. bind设备
    - `usbipd bind --busid=2-11`
3. attach设置
    - `usbipd attach --wsl --busid=2-11`