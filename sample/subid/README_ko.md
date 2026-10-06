[English](README.md) | [中文](README_zh.md) | **한국어**

> 영어 [원문](README.md)을 번역한 문서입니다. 내용이 다르면 원문을 기준으로 합니다.

### 개요
이 가이드에서는 NOR 플래시 스토리지를 사용하는 PCIe EP 디바이스의 서브 벤더 ID 또는 서브 디바이스 ID를 변경하는 방법을 설명합니다.

:::{Caution}
- 호스트 드라이버도 그에 맞게 업데이트되었는지 확인하세요. 그렇지 않으면 디바이스를 프로브할 수 없습니다.
- PCIe 서브 ID는 NOR 플래시에서만 변경할 수 있습니다.
:::

### 사용법
```bash
usage: ./axcl_change_subid [options] ...
options:
  -d, --device       device index from 0 to connected device num - 1 (unsigned int [=0])
      --subvendor    sub vendor id (decimal) (unsigned int [=8011])
      --subdevice    sub device id (decimal) (unsigned int [=1616])
      --json         axcl.json path (string [=./axcl.json])
  -?, --help         print this message
```

### 예제

```bash
# 첫 번째로 연결된 디바이스의 서브 ID를 0x650으로 변경합니다
$ ./axcl_change_subid --subdevice=1616 -d 0
```
