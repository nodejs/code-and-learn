# 事前准备

## 安装环境

### MacOS

1. 从 App Store 安装 Xcode
2. 从命令行运行

  ```
  xcode-select --install
  ```
3. 推荐安装 ninja。如使用 homebrew：

   ```
   brew install ninja
   ```

### Ubuntu

```
apt-get update
apt-get install -y build-essential python ninja-build make g++
```

### 其他环境

UNIX: https://github.com/nodejs/node/blob/master/BUILDING.md#unix-prerequisites
Windows: https://github.com/nodejs/node/blob/master/BUILDING.md#prerequisites

## 构建与测试

全部环境：推荐先 clone Node.js core 的代码。因为体积较大，建议用 `--depth=1`：

```
git clone --depth=1 https://github.com/nodejs/node.git
```

推荐在参加前先编译（需耗时半个小时到一个小时）和运行一遍测试（会从 npm 下载一些渲染 API 文档需要的包）。MacOS 和 Linux：

```
cd node

# 如果有安装 ninja
python2 ./configure --ninja
BUILD_WITH=ninja make -j2 test

# 如果没有安装 ninja
python2 ./configure
make -j2 test
```

其中 -j2 的 2 可以替换成机器上的 CPU 逻辑核心数。
