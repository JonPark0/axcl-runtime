# AXCL 새 툴체인 연동 가이드

[English](README_en.md) | [中文](README.md) | **한국어**

> 중국어 원문 [README.md](README.md)를 번역한 문서입니다. 내용이 다르면 원문을 기준으로 합니다.

이 문서는 AXCL의 기존 빌드 시스템에 새 호스트 툴체인을 연동하는 방법을 설명합니다. 예시 시나리오는 OpenWrt arm64 gcc입니다.

> **팁**
> 새 툴체인을 추가하기 전에 먼저 기존 `host=x86` 빌드를 한 번 끝까지 실행하여 정상 동작하는 기준 결과를 남겨 두는 것을 권장합니다. 그러면 이후 비교가 더 쉬워집니다.

## 기존 빌드 시스템과 x86_64 예시

### 참고 빌드 옵션

| host 매개변수 | 설명 | 출력 디렉터리 |
| --- | --- | --- |
| `x86` | x86_64 | `out/axcl_linux_x86` |
| `arm64` | aarch64 | `out/axcl_linux_arm64` |

### 기존 빌드 명령

| host | 빌드 명령 | 용도 |
| --- | --- | --- |
| `x86` | `cd build && make host=x86 clean all install -j128` | x86_64 기준 빌드 |
| `arm64` | `cd build && make host=arm64 clean all install -j128` | 기존 arm64 빌드 |

### x86_64 빌드 명령

```bash
cd build && make host=x86 clean all install -j128
```

이 단계의 목적은 간단합니다. 먼저 현재 빌드 과정이 처음부터 끝까지 정상적으로 동작하는지 확인한 다음 새 툴체인 추가를 시작합니다.

### x86_64 출력 디렉터리

```text
out/axcl_linux_x86/
├── bin/      # 실행 파일
├── lib/      # 라이브러리 파일
├── include/  # 헤더 파일
├── ko/       # 커널 모듈
└── json/     # 설정 파일
```

보충 설명:
- x86_64의 주 출력 디렉터리는 `out/axcl_linux_x86`입니다.
- 현재 x86_64 빌드에서 함께 생성되는 Python wheel의 출력 디렉터리는 `out/python`입니다.

## 빌드 시스템 주요 흐름

| 단계 | 파일 | 역할 |
| --- | --- | --- |
| 최상위 진입점 | `build/Makefile` | `host` 매개변수를 받아 `clean`, `all`, `install` 실행 |
| 호스트 분기 | `build/config.mak` | `host`를 해석하여 `HOST`를 설정하고 해당하는 `*_config.mak` 파일을 로드 |
| 사용자 공간 규칙 | `build/rules.mak` | `HOST`에 따라 해당하는 `*_rules.mak` 파일로 전달 |
| 커널 공간 규칙 | `build/krules.mak` | `HOST`에 따라 해당하는 `*_krules.mak` 파일로 전달 |
| 호스트 설정 디렉터리 | `build/projects/` | 각 호스트의 `config`, `rules`, `krules` 파일 보관 |
| 3rdparty 경로 선택 | `logger/Makefile`, `protocol/proto/static.mak`, `protocol/package/Makefile`, `test/*/Makefile` 등 | 이 파일들에는 `$(ARCH)`에 따라 `3rdparty` 디렉터리를 직접 선택하는 경로가 많음 |

여기서는 다음 두 변수를 구분해서 이해해야 합니다.
- `HOST`는 전체 빌드 대상을 구분하는 데 사용되며, 출력 디렉터리 이름도 결정합니다.
- `ARCH`는 기반 아키텍처를 나타내며, 많은 3rdparty 헤더 파일 및 라이브러리 경로 선택이 여전히 이 변수에 의존합니다.

OpenWrt arm64 시나리오의 경우 첫 버전에서는 다음 구성을 사용하는 것을 권장합니다.

| 변수 | 권장 값 | 설명 |
| --- | --- | --- |
| `HOST` | `openwrt_arm64` | 새 툴체인의 진입점과 출력 디렉터리를 구분하는 데 사용 |
| `ARCH` | `arm64` |  |

## 3rdparty 처리 방식

서드파티 컴포넌트는 먼저 독립적으로 빌드한 다음, 설치 결과를 아키텍처별로 `3rdparty/` 디렉터리에 넣습니다. AXCL 메인 빌드 단계에서는 주로 이렇게 미리 만들어진 산출물을 사용합니다.

특수한 경우는 `ffmpeg`뿐입니다. 저장소에 이미 소스 디렉터리가 포함되어 있으며, 실제 빌드는 `3rdparty/ffmpeg/build.sh` 스크립트로 별도로 수행합니다.

### 주의해야 할 3rdparty 컴포넌트

| 컴포넌트 | 현재 방식 | 현재 버전 | 다운로드 주소 | 연동 참고 사항 |
| --- | --- | --- | --- | --- |
| `ffmpeg` | 저장소 내 소스 + 독립 빌드 스크립트 | `n7.1` | `3rdparty/ffmpeg/FFmpeg-n7.1/` | OpenWrt 연동 시 `3rdparty/ffmpeg/build.sh`의 configure 인자를 중점적으로 확인해야 함 |
| `googletest` | 사전 빌드된 설치 결과 | `1.15.0` | `https://github.com/google/googletest/releases/tag/v1.15.0` | 기존 `arm64` 산출물을 재사용할 수 없으면 다시 사전 빌드하고 새로운 디렉터리 선택 로직을 추가해야 함 |
| `protobuf` | 사전 빌드된 설치 결과 | `3.20.3` | `https://github.com/protocolbuffers/protobuf/releases/tag/v3.20.3` | 기존 `arm64` 산출물을 재사용할 수 없으면 다시 사전 빌드하고 새로운 디렉터리 선택 로직을 추가해야 함 |
| `spdlog` | 사전 빌드된 설치 결과 | `1.14.1` | `https://github.com/gabime/spdlog/releases/tag/v1.14.1` | 기존 `arm64` 산출물을 재사용할 수 없으면 다시 사전 빌드하고 새로운 디렉터리 선택 로직을 추가해야 함 |

## OpenWrt arm64 툴체인을 예로 든 새 툴체인 추가

### 수정 및 추가해야 할 파일

| 유형 | 파일 또는 디렉터리 | 필수 여부 | 설명 |
| --- | --- | --- | --- |
| 수정 | `build/config.mak` | 예 | `openwrt_arm64` 분기를 추가하여 최상위 make가 새 호스트를 인식하도록 함 |
| 신규 | `build/projects/axcl_linux_openwrt_arm64_config.mak` | 예 | OpenWrt 툴체인 변수 정의 |
| 신규 | `build/projects/axcl_linux_openwrt_arm64_rules.mak` | 예 | 사용자 공간 규칙 파일. 첫 버전에서는 arm64 템플릿을 그대로 재사용 가능 |
| 신규 | `build/projects/axcl_linux_openwrt_arm64_krules.mak` | 예 | 커널 공간 규칙 파일. 첫 버전에서는 arm64 템플릿을 그대로 재사용 가능 |
| 수정 | `3rdparty` | 예 | 사전 빌드 및 경로 수정 |

### 템플릿 출처

| 새 파일 | 권장 템플릿 |
| --- | --- |
| `axcl_linux_openwrt_arm64_config.mak` | `build/projects/axcl_linux_arm64_config.mak` |
| `axcl_linux_openwrt_arm64_rules.mak` | `build/projects/axcl_linux_arm64_rules.mak` |
| `axcl_linux_openwrt_arm64_krules.mak` | `build/projects/axcl_linux_arm64_krules.mak` |

### 연동 절차

| 단계 | 작업 | 설명 |
| --- | --- | --- |
| 1 | `cd build && make host=x86 clean all install -j128` 실행 | 먼저 정상 동작하는 x86_64 기준 결과를 확보 |
| 2 | `build/config.mak`에 `openwrt_arm64` 분기 추가 | `make host=openwrt_arm64`에서 새 호스트를 인식할 수 있도록 함 |
| 3 | arm64 템플릿을 복사하여 `axcl_linux_openwrt_arm64_config.mak`, `rules.mak`, `krules.mak` 추가 | 새 호스트를 위한 전체 진입점 구성 |
| 4 | 새 `*_config.mak`에서 OpenWrt 툴체인 접두사를 설정하고 `ARCH=arm64` 유지 | 우선 툴체인 차이를 설정 파일 안에서만 처리 |
| 5 | 3rdparty | 사전 빌드 및 경로 수정 |
| 9 | `cd build && make host=openwrt_arm64 clean all install -j128` 실행 | 새 호스트 연동이 완료되었는지 검증 |
| 10 | `out/axcl_linux_openwrt_arm64` 아래의 `bin`, `lib`, `include`, `ko`, `json` 확인 | 빌드 결과가 예상과 일치하는지 확인 |

### OpenWrt config 파일 주요 변수

| 변수 | 역할 | OpenWrt arm64 예시에서의 처리 방식 |
| --- | --- | --- |
| `CROSS` | 툴체인 접두사 | OpenWrt arm64 musl gcc 12.3.0에 해당하는 접두사로 변경 |
| `CC` | C 컴파일러 | 일반적으로 `$(CROSS)gcc`에서 파생 |
| `CPP` | C++ 컴파일러 | 일반적으로 `$(CROSS)g++`에서 파생 |
| `LD` | 링커 | 일반적으로 `$(CROSS)ld`에서 파생 |
| `AR` | 아카이브 도구 | 일반적으로 `$(CROSS)ar`에서 파생 |
| `STRIP` | strip 도구 | 일반적으로 `$(CROSS)strip`에서 파생 |
| `OBJCOPY` | objcopy 도구 | 일반적으로 `$(CROSS)objcopy`에서 파생 |
| `ARCH` | 아키텍처 식별자 | `arm64`로 유지 |

### 빌드 검증

```bash
cd build && make host=openwrt_arm64 clean all install -j128
```

### 결과 확인

| 확인 항목 | 예상 결과 |
| --- | --- |
| 출력 루트 디렉터리 | `out/axcl_linux_openwrt_arm64` 생성 |
| `bin` 디렉터리 | 실행 파일 출력 존재 |
| `lib` 디렉터리 | 라이브러리 파일 출력 존재 |
| `include` 디렉터리 | 헤더 파일 출력 존재 |
| `ko` 디렉터리 | 현재 흐름에 드라이버 빌드가 포함된 경우 모듈 출력이 있어야 함 |
| `json` 디렉터리 | 현재 흐름에 설정 설치가 포함된 경우 JSON 설정 출력이 있어야 함 |
