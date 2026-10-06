[English](README.md) | **中文** | [한국어](README_ko.md)

> 本文译自英文[原文](README.md)，如有出入以原文为准。

### runtime 示例
1. 通过 axclrtInit 初始化 axcl runtime。
2. 通过 axclrtSetDevice 激活 EP。
3. 通过 axclrtCreateDevice 为主线程创建 context。（可选）
4. 创建并销毁线程 context。（必须）
5. 销毁主线程的 context。
6. 通过 axclrtResetDevice 去激活 EP
7. 通过 axclFinalize 去初始化 runtime


### 用法
```bash
usage: ./axcl_sample_runtime [options] ...
options:
  -d, --device    device index [-1, connected device num - 1], -1: traverse all devices (int [=-1])
      --json      axcl.json path (string [=./axcl.json])
  -?, --help      print this message
```

### 示例

```bash
$ ./axcl_sample_runtime
[INFO ][                            main][  22]: ============== V3.0.0 sample started Mar 12 2025 16:21:21 ==============
[INFO ][                      operator()][ 104]: malloc 1048576 bytes memory from device 13 success, addr = 0x14926f000
[INFO ][                      operator()][ 104]: malloc 1048576 bytes memory from device 11 success, addr = 0x14926f000
[INFO ][                      operator()][ 104]: malloc 1048576 bytes memory from device 14 success, addr = 0x14926f000
[INFO ][                      operator()][ 104]: malloc 1048576 bytes memory from device  5 success, addr = 0x14926f000
[INFO ][                      operator()][ 104]: malloc 1048576 bytes memory from device  3 success, addr = 0x14926f000
[INFO ][                      operator()][ 104]: malloc 1048576 bytes memory from device 10 success, addr = 0x14926f000
[INFO ][                      operator()][ 104]: malloc 1048576 bytes memory from device  4 success, addr = 0x14926f000
[INFO ][                      operator()][ 104]: malloc 1048576 bytes memory from device  9 success, addr = 0x14926f000
[INFO ][                            main][ 127]: ============== V3.0.0 sample exited Mar 12 2025 16:21:21 ==============
```
