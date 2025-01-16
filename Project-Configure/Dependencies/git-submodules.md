# git-submodules
----

## 添加

```shell
git submodule add https://github.com/lvgl/lv_demos.git components/lv_demos

git submodule add -b release/v8.0 https://github.com/lvgl/lv_demos.git components/lv_demos
# 指定版本
```
> 工程文件夹中自动生成`.gitmodules`文件

## 代码下载

初次添加或初次克隆时可能不会自动下载相关代码, 需运行
```shell
git submodule init
git submodule update
# 或
git submodule update --init --recursive

```

## 其他常用命令

### 列出所有子模块

```shell
git submodule
```

### 更新所有子模块

```shell
git submodule update --recursive --remote
```
> `--recursive`:   递归地更新所有子模块 包含子模块的子模块
> `--remote`:        从子模块的远程仓库拉取最新的更改

> 更新子模块后, 需要提交最新更改, 远程仓库才能同步

### 检查子模块状态

```shell
git submodule status
```

## 删除(比较复杂)

### 原始方法

1. 删除项目目录下`.gitmodules`文件中相关子组件条目    `vi .gitmodules`
2. 删除配置项中子模块相关条目    `vi .git/config`
3. 删除暂存区数据    `git rm --cached path_to_submodule（末尾不加路径符）`
4. 删除子模块    `rm --rf .git/modules/path_to_submodule（末尾不加路径符）`
5. 提交修改    `git commit -m "Remove submodules"`
6. 删除当前工作区文件    `rm -rf path_to_submodule`

### `deinit`命令

1. 使用`deinit`命令移除submodules
    - `git submodule deinit path/to/submodule`
    - 该命令会编辑`.submodules`文件, 并将子模块从`.git/config`中移除, 并删除子模块目录中的文件
2. 移除子模块文件夹远程同步
    - `git rm path/to/submodule`

