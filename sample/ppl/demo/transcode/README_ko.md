[English](README.md) | [中文](README_zh.md) | **한국어**

> 영어 [원문](README.md)을 번역한 문서입니다. 내용이 다르면 원문을 기준으로 합니다.

### transcode 샘플 (PPL: VDEC - IVPS - VENC)
1. .mp4 또는 .h264/h265 스트림 파일을 로드합니다
2. ffmpeg로 nalu를 디먹싱합니다
3. nalu 프레임을 VDEC에 전송합니다
4. 리사이즈하는 경우 VDEC이 디코딩된 nv12를 IVPS에 전송합니다
5. IVPS가 nv12를 VENC에 전송합니다
6. VENC가 인코딩한 nalu 프레임을 호스트로 전송합니다.


### 모듈 배치
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

### transcode ppl 속성
```bash
        attribute name                       R/W    attribute value type
 *  axcl.ppl.transcode.vdec.grp             [R  ]       int32_t                            allocated by ax_vdec.ko
 *  axcl.ppl.transcode.ivps.grp             [R  ]       int32_t                            allocated by ax_ivps.ko
 *  axcl.ppl.transcode.venc.chn             [R  ]       int32_t                            allocated by ax_venc.ko
 *
 *  the following attributes take effect BEFORE the axcl_ppl_create function is called:
 *  axcl.ppl.transcode.vdec.blk.cnt         [R/W]       uint32_t          8                depend on stream DPB size and decode mode
 *  axcl.ppl.transcode.vdec.out.depth       [R/W]       uint32_t          4                out fifo depth
 *  axcl.ppl.transcode.ivps.in.depth        [R/W]       uint32_t          4                in fifo depth
 *  axcl.ppl.transcode.ivps.out.depth       [R  ]       uint32_t          0                out fifo depth
 *  axcl.ppl.transcode.ivps.blk.cnt         [R/W]       uint32_t          4
 *  axcl.ppl.transcode.ivps.engine          [R/W]       uint32_t   AX_IVPS_ENGINE_VPP      AX_IVPS_ENGINE_VPP|AX_IVPS_ENGINE_VGP|AX_IVPS_ENGINE_TDP
 *  axcl.ppl.transcode.venc.in.depth        [R/W]       uint32_t          4                in fifo depth
 *  axcl.ppl.transcode.venc.out.depth       [R/W]       uint32_t          4                out fifo depth

NOTE:
 The value of "axcl.ppl.transcode.vdec.blk.cnt" depends on input stream.
 Usually set to dpb + 1
```
### 사용법
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
> 위 오류가 발생하면 ffmpeg 라이브러리를 LD_LIBRARY_PATH에 설정하세요.
> x86_x64 OS의 경우:  *export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/lib/axcl/ffmpeg*

### 예제

```bash
# 입력 1080P@30fps 264를 1080P@30fps 265로 트랜스코딩하고, /tmp/axcl/transcode.dump.pidxxx 파일에 저장합니다.
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

**launch_transcode.sh**는 axcl_sample_transcode를 여러 개(최대 16개) 실행하고 LD_LIBRARY_PATH를 자동으로 설정할 수 있습니다.

```bash
Usage:
launch_transcode.sh 16 -i bangkok_30952_1920x1080_30fps_gop60_4Mbps.mp4  -d 0 --dump /tmp/axcl/transcode.265
```

> [!NOTE]
>
> 첫 번째 인수는 반드시 *axcl_sample_transcode* 프로세스 수여야 합니다. 범위: [1, 16]

