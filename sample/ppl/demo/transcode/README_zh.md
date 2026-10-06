[English](README.md) | **中文** | [한국어](README_ko.md)

> 本文译自英文[原文](README.md)，如有出入以原文为准。

### transcode 示例（PPL：VDEC - IVPS - VENC）
1. 加载 .mp4 或 .h264/h265 码流文件
2. 通过 ffmpeg 解封装出 nalu
3. 将 nalu 帧送入 VDEC
4. VDEC 将解码后的 nv12 发送给 IVPS（需要缩放时）
5. IVPS 将 nv12 发送给 VENC
6. 将 VENC 编码后的 nalu 帧发送到主机。


### 模块部署
```bash
|-----------------------------|
|          sample             |
|-----------------------------|
|      libaxcl_ppl.so         |
|-----------------------------|
|      libaxcl_lite.so        |
|-----------------------------|
|         axcl sdk            |
|-----------------------------|
|         pcie driver         |
|-----------------------------|
```

### transcode ppl 属性
```bash
        属性名称                             R/W    属性值类型
 *  axcl.ppl.transcode.vdec.grp             [R  ]       int32_t                            由 ax_vdec.ko 分配
 *  axcl.ppl.transcode.ivps.grp             [R  ]       int32_t                            由 ax_ivps.ko 分配
 *  axcl.ppl.transcode.venc.chn             [R  ]       int32_t                            由 ax_venc.ko 分配
 *
 *  以下属性须在调用 axcl_ppl_create 函数之前设置才会生效：
 *  axcl.ppl.transcode.vdec.blk.cnt         [R/W]       uint32_t          8                取决于码流的 DPB 大小和解码模式
 *  axcl.ppl.transcode.vdec.out.depth       [R/W]       uint32_t          4                输出 fifo 深度
 *  axcl.ppl.transcode.ivps.in.depth        [R/W]       uint32_t          4                输入 fifo 深度
 *  axcl.ppl.transcode.ivps.out.depth       [R  ]       uint32_t          0                输出 fifo 深度
 *  axcl.ppl.transcode.ivps.blk.cnt         [R/W]       uint32_t          4
 *  axcl.ppl.transcode.ivps.engine          [R/W]       uint32_t   AX_IVPS_ENGINE_VPP      AX_IVPS_ENGINE_VPP|AX_IVPS_ENGINE_VGP|AX_IVPS_ENGINE_TDP
 *  axcl.ppl.transcode.venc.in.depth        [R/W]       uint32_t          4                输入 fifo 深度
 *  axcl.ppl.transcode.venc.out.depth       [R/W]       uint32_t          4                输出 fifo 深度

注意：
 "axcl.ppl.transcode.vdec.blk.cnt" 的值取决于输入码流。
 通常设置为 dpb + 1
```
### 用法
```bash
usage: ./axcl_sample_transcode --url=string [options] ...
options:
  -i, --url       mp4|.264|.265 file path (string)
  -d, --device    device index from 0 to connected device num - 1 (unsigned int [=0])
  -w, --width     output width, 0 means same as input (unsigned int [=0])
  -h, --height    output height, 0 means same as input (unsigned int [=0])
      --codec     encoded codec: [h264 | h265] (default: h265) (string [=h265])
      --json      axcl.json path (string [=./axcl.json])
      --loop      1: loop demux for local file  0: no loop(default) (int [=0])
      --dump      dump file path (string [=])
      --hwclk     decoder hw clk, 0: 624M, 1: 500M, 2: 400M(default) (unsigned int [=2])
      --ut        unittest
  -?, --help      print this message
```

> [!NOTE]
>
> ./axcl_sample_transcode: error while loading shared libraries: libavcodec.so.58: cannot open shared object file: No such file or directory
> 如果出现上述错误，请将 ffmpeg 库路径配置到 LD_LIBRARY_PATH 中。
> 对于 x86_x64 系统：*export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/lib/axcl/ffmpeg*

### 示例

```bash
# 将输入的 1080P@30fps 264 转码为 1080P@30fps 265，保存到 /tmp/axcl/transcode.dump.pidxxx 文件。
$ ./axcl_sample_transcode -i bangkok_30952_1920x1080_30fps_gop60_4Mbps.mp4 -d 0 --dump /tmp/axcl/transcode.265
[INFO ][                            main][  66]: ============== V2.26.1 sample started Feb 13 2025 16:37:03 pid 798 ==============
[WARN ][                            main][  91]: if enable dump, disable loop automatically
[INFO ][                            main][ 130]: pid: 798, device index: 0, bus number: 129
[INFO ][             ffmpeg_init_demuxer][ 438]: [798] url: bangkok_30952_1920x1080_30fps_gop60_4Mbps.mp4
[INFO ][             ffmpeg_init_demuxer][ 501]: [798] url bangkok_30952_1920x1080_30fps_gop60_4Mbps.mp4: codec 96, 1920x1080, fps 30
[INFO ][         ffmpeg_set_demuxer_attr][ 570]: [798] set ffmpeg.demux.file.frc to 1
[INFO ][         ffmpeg_set_demuxer_attr][ 573]: [798] set ffmpeg.demux.file.loop to 0
[INFO ][                            main][ 194]: pid 798: [vdec 00] - [ivps -1] - [venc 00]
[INFO ][                            main][ 212]: pid 798: VDEC attr ==> blk cnt: 8, fifo depth: out 4
[INFO ][                            main][ 213]: pid 798: IVPS attr ==> blk cnt: 5, fifo depth: in 4, out 0, engine 1
[INFO ][                            main][ 215]: pid 798: VENC attr ==> fifo depth: in 4, out 4
[INFO ][          ffmpeg_dispatch_thread][ 188]: [798] +++
[INFO ][             ffmpeg_demux_thread][ 294]: [798] +++
[INFO ][             ffmpeg_demux_thread][ 327]: [798] reach eof
[INFO ][             ffmpeg_demux_thread][ 434]: [798] demuxed    total 470 frames ---
[INFO ][          ffmpeg_dispatch_thread][ 271]: [798] dispatched total 470 frames ---
[INFO ][                            main][ 246]: ffmpeg (pid 798) demux eof
[INFO ][                            main][ 282]: total transcoded frames: 470
[INFO ][                            main][ 283]: ============== V2.26.1 sample exited Feb 13 2025 16:37:03 pid 798 ==============
```

### launch_transcode.sh

**launch_transcode.sh** 支持启动多个（最多 16 个）axcl_sample_transcode，并自动配置 LD_LIBRARY_PATH。

```bash
Usage:
launch_transcode.sh 16 -i bangkok_30952_1920x1080_30fps_gop60_4Mbps.mp4  -d 0 --dump /tmp/axcl/transcode.265
```

> [!NOTE]
>
> 第 1 个参数必须是 *axcl_sample_transcode* 进程的数量。范围：[1, 16]

