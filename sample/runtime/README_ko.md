[English](README.md) | [中文](README_zh.md) | **한국어**

> 영어 [원문](README.md)을 번역한 문서입니다. 내용이 다르면 원문을 기준으로 합니다.

### 런타임 샘플
1. axclrtInit으로 axcl 런타임을 초기화합니다.
2. axclrtSetDevice로 EP를 활성화합니다.
3. axclrtCreateDevice로 메인 스레드의 컨텍스트를 생성합니다. (선택)
4. 스레드 컨텍스트를 생성하고 삭제합니다. (필수)
5. 메인 스레드의 컨텍스트를 삭제합니다.
6. axclrtResetDevice로 EP를 비활성화합니다
7. axclFinalize로 런타임 초기화를 해제합니다


### 사용법
```bash
usage: ./axcl_sample_runtime [options] ...
options:
  -d, --device    device index [-1, connected device num - 1], -1: traverse all devices (int [=-1])
      --json      axcl.json path (string [=./axcl.json])
  -?, --help      print this message
```

### 예제

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
