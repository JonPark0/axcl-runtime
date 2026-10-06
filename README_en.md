# AXCL New Toolchain Integration Guide

**English** | [中文](README.md) | [한국어](README_ko.md)

> Translated from the Chinese [README.md](README.md). If the two differ, the Chinese original is authoritative.

This document explains how to integrate a new host toolchain into the existing AXCL build system. The example scenario is OpenWrt arm64 gcc.

> **Tip**
> Before adding a new toolchain, it is recommended to first run a complete build with the existing `host=x86` and keep a working baseline result. This makes later comparisons more straightforward.

## Existing Build System and x86_64 Example

### Reference Build Options

| host parameter | Description | Output directory |
| --- | --- | --- |
| `x86` | x86_64 | `out/axcl_linux_x86` |
| `arm64` | aarch64 | `out/axcl_linux_arm64` |

### Existing Build Commands

| host | Build command | Purpose |
| --- | --- | --- |
| `x86` | `cd build && make host=x86 clean all install -j128` | x86_64 baseline build |
| `arm64` | `cd build && make host=arm64 clean all install -j128` | Existing arm64 build |

### x86_64 Build Command

```bash
cd build && make host=x86 clean all install -j128
```

The purpose of this step is simple: first confirm that the current build pipeline works end to end, then start adding the new toolchain.

### x86_64 Output Directory

```text
out/axcl_linux_x86/
├── bin/      # Executables
├── lib/      # Libraries
├── include/  # Header files
├── ko/       # Kernel modules
└── json/     # Configuration files
```

Additional notes:
- `out/axcl_linux_x86` is the main x86_64 output directory.
- `out/python` is the output directory for the Python wheel that the current x86_64 build also generates.

## Main Build System Flow

| Stage | File | Purpose |
| --- | --- | --- |
| Top-level entry | `build/Makefile` | Receives the `host` parameter and runs `clean`, `all` and `install` |
| host dispatch | `build/config.mak` | Parses `host`, sets `HOST` and loads the corresponding `*_config.mak` |
| User-space rules | `build/rules.mak` | Forwards to the corresponding `*_rules.mak` based on `HOST` |
| Kernel-space rules | `build/krules.mak` | Forwards to the corresponding `*_krules.mak` based on `HOST` |
| host configuration directory | `build/projects/` | Holds the `config`, `rules` and `krules` files for each host |
| 3rdparty path selection | `logger/Makefile`, `protocol/proto/static.mak`, `protocol/package/Makefile`, `test/*/Makefile`, etc. | Many paths in these files select the `3rdparty` directory directly by `$(ARCH)` |

Two variables here need to be considered separately:
- `HOST` distinguishes the complete build target and also determines the output directory name.
- `ARCH` describes the underlying architecture; many 3rdparty header and library path selections still depend on it.

For the OpenWrt arm64 scenario, the following configuration is recommended for the first version:

| Variable | Recommended value | Description |
| --- | --- | --- |
| `HOST` | `openwrt_arm64` | Distinguishes the new toolchain's entry point and output directory |
| `ARCH` | `arm64` |  |

## 3rdparty Handling

Third-party components are first built independently, and their install output is then placed in the `3rdparty/` directory by architecture. The main AXCL build stage mainly consumes these prebuilt artifacts.

Only `ffmpeg` is a special case: the repository already contains its source directory, and the actual build is done separately by `3rdparty/ffmpeg/build.sh`.

### 3rdparty Components to Watch

| Component | Current method | Current version | Download URL | Integration notes |
| --- | --- | --- | --- | --- |
| `ffmpeg` | In-tree source + standalone build script | `n7.1` | `3rdparty/ffmpeg/FFmpeg-n7.1/` | When integrating OpenWrt, check the configure arguments in `3rdparty/ffmpeg/build.sh` carefully |
| `googletest` | Prebuilt install output | `1.15.0` | `https://github.com/google/googletest/releases/tag/v1.15.0` | If the existing `arm64` artifacts cannot be reused, prebuild it again and add new directory selection logic |
| `protobuf` | Prebuilt install output | `3.20.3` | `https://github.com/protocolbuffers/protobuf/releases/tag/v3.20.3` | If the existing `arm64` artifacts cannot be reused, prebuild it again and add new directory selection logic |
| `spdlog` | Prebuilt install output | `1.14.1` | `https://github.com/gabime/spdlog/releases/tag/v1.14.1` | If the existing `arm64` artifacts cannot be reused, prebuild it again and add new directory selection logic |

## Adding a New Toolchain, Using the OpenWrt arm64 Toolchain as an Example

### Files to Modify and Add

| Type | File or directory | Required | Description |
| --- | --- | --- | --- |
| Modify | `build/config.mak` | Yes | Add an `openwrt_arm64` branch so that the top-level make recognizes the new host |
| Add | `build/projects/axcl_linux_openwrt_arm64_config.mak` | Yes | Defines the OpenWrt toolchain variables |
| Add | `build/projects/axcl_linux_openwrt_arm64_rules.mak` | Yes | User-space rules file; the first version can reuse the arm64 template as is |
| Add | `build/projects/axcl_linux_openwrt_arm64_krules.mak` | Yes | Kernel-space rules file; the first version can reuse the arm64 template as is |
| Modify | `3rdparty` | Yes | Prebuild and modify paths |

### Template Sources

| New file | Recommended template |
| --- | --- |
| `axcl_linux_openwrt_arm64_config.mak` | `build/projects/axcl_linux_arm64_config.mak` |
| `axcl_linux_openwrt_arm64_rules.mak` | `build/projects/axcl_linux_arm64_rules.mak` |
| `axcl_linux_openwrt_arm64_krules.mak` | `build/projects/axcl_linux_arm64_krules.mak` |

### Integration Steps

| Step | Action | Description |
| --- | --- | --- |
| 1 | Run `cd build && make host=x86 clean all install -j128` | First obtain a working x86_64 baseline result |
| 2 | Add an `openwrt_arm64` branch to `build/config.mak` | Lets `make host=openwrt_arm64` recognize the new host |
| 3 | Copy the arm64 templates and add `axcl_linux_openwrt_arm64_config.mak`, `rules.mak` and `krules.mak` | Sets up a complete entry point for the new host |
| 4 | In the new `*_config.mak`, set the OpenWrt toolchain prefix and keep `ARCH=arm64` | Confine the toolchain differences to the configuration file first |
| 5 | 3rdparty | Prebuilding and path changes |
| 9 | Run `cd build && make host=openwrt_arm64 clean all install -j128` | Verify that the new host is fully integrated |
| 10 | Check `bin`, `lib`, `include`, `ko` and `json` under `out/axcl_linux_openwrt_arm64` | Confirm that the build results are as expected |

### Key Variables in the OpenWrt config File

| Variable | Purpose | Handling in the OpenWrt arm64 example |
| --- | --- | --- |
| `CROSS` | Toolchain prefix | Change to the prefix for OpenWrt arm64 musl gcc 12.3.0 |
| `CC` | C compiler | Usually derived from `$(CROSS)gcc` |
| `CPP` | C++ compiler | Usually derived from `$(CROSS)g++` |
| `LD` | Linker | Usually derived from `$(CROSS)ld` |
| `AR` | Archiver | Usually derived from `$(CROSS)ar` |
| `STRIP` | strip tool | Usually derived from `$(CROSS)strip` |
| `OBJCOPY` | objcopy tool | Usually derived from `$(CROSS)objcopy` |
| `ARCH` | Architecture identifier | Keep as `arm64` |

### Build Verification

```bash
cd build && make host=openwrt_arm64 clean all install -j128
```

### Result Check

| Check item | Expected result |
| --- | --- |
| Output root directory | `out/axcl_linux_openwrt_arm64` is generated |
| `bin` directory | Executable output is present |
| `lib` directory | Library output is present |
| `include` directory | Header file output is present |
| `ko` directory | If the current flow includes the driver build, module output should be present |
| `json` directory | If the current flow includes configuration installation, JSON configuration output should be present |
