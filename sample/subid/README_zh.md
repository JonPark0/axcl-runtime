[English](README.md) | **中文** | [한국어](README_ko.md)

> 本文译自英文[原文](README.md)，如有出入以原文为准。

### 简介
本指南介绍如何修改采用 NOR flash 存储的 PCIe EP 设备的 sub vendor ID 或 sub device ID。

:::{Caution}
- 请确保 HOST 端驱动已相应更新，否则设备将无法被 probe。
- PCIe sub id 只能在 NOR flash 上修改。
:::

### 用法
```bash
usage: ./axcl_change_subid [options] ...
options:
  -d, --device       device index from 0 to connected device num - 1 (unsigned int [=0])
      --subvendor    sub vendor id (decimal) (unsigned int [=8011])
      --subdevice    sub device id (decimal) (unsigned int [=1616])
      --json         axcl.json path (string [=./axcl.json])
  -?, --help         print this message
```

### 示例

```bash
# 将第 1 个已连接设备的 sub id 修改为 0x650
$ ./axcl_change_subid --subdevice=1616 -d 0
```
