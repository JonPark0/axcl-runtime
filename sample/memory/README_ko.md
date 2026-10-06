[English](README.md) | [中文](README_zh.md) | **한국어**

> 영어 [원문](README.md)을 번역한 문서입니다. 내용이 다르면 원문을 기준으로 합니다.

### 호스트와 디바이스 간 memcpy 샘플

         HOST          |               DEVICE
      host_mem[0] -----------> dev_mem[0]
                                    |---------> dev_mem[1]
      host_mem[1] <----------------------------------|

1. 호스트 메모리 2개를 할당합니다: *host_mem[2]*
2. 디바이스 메모리 2개를 할당합니다: *dev_mem[2]*
3. AXCL_MEMCPY_HOST_TO_DEVICE로 host_mem[0]에서 dev_mem[0]으로 memcpy를 수행합니다
4. AXCL_MEMCPY_DEVICE_TO_DEVICE로 dev_mem[0]에서 dev_mem[1]로 memcpy를 수행합니다
5. AXCL_MEMCPY_DEVICE_TO_HOST로 dev_mem[1]에서 host_mem[0]으로 memcpy를 수행합니다
6. host_mem[0]과 host_mem[1]을 memcmp로 비교합니다

### 사용법
```bash
usage: ./axcl_sample_memory [options] ...
options:
  -d, --device    device index from 0 to connected device num - 1 (int [=0])
      --json      axcl.json path (string [=./axcl.json])
  -?, --help      print this message
```

### 예제

```bash
$ ./axcl_sample_memory  -d 0
[INFO ][                            main][  32]: ============== V2.26.1 sample started Feb 13 2025 11:09:59 ==============
[INFO ][                           setup][ 112]: json: ./axcl.json
[INFO ][                           setup][ 131]: device index: 0, bus number: 129
[INFO ][                            main][  51]: alloc host and device memory, size: 0x800000
[INFO ][                            main][  63]: memory [0]: host 0xffff967fb010, device 0x14926f000
[INFO ][                            main][  63]: memory [1]: host 0xffff95ffa010, device 0x149a6f000
[INFO ][                            main][  69]: memcpy from host memory[0] 0xffff967fb010 to device memory[0] 0x14926f000
[INFO ][                            main][  75]: memcpy device memory[0] 0x14926f000 to device memory[1] 0x149a6f000
[INFO ][                            main][  81]: memcpy device memory[1] 0x149a6f000 to host memory[0] 0xffff95ffa010
[INFO ][                            main][  88]: compare host memory[0] 0xffff967fb010 and host memory[1] 0xffff95ffa010 success
[INFO ][                         cleanup][ 146]: deactive device 129 and cleanup axcl
[INFO ][                            main][ 106]: ============== V2.26.1 sample exited Feb 13 2025 11:09:59 ==============
```
