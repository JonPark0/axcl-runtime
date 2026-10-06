[English](README.md) | [中文](README_zh.md) | **한국어**

> 영어 [원문](README.md)을 번역한 문서입니다. 내용이 다르면 원문을 기준으로 합니다.

### 설명

​	Axera SDK 패키지에서 제공하는 IVPS(Image Video Process System) 유닛은 크롭, 스케일링, 회전, 스트리밍, CSC, OSD, 모자이크, 사각형 등의 기능을 제공하는 비디오 이미지 처리 서브시스템입니다.

​	이 모듈은 IVPS 유닛의 예제 코드로, 사용자가 IVPS 관련 인터페이스의 사용법을 빠르게 이해하고 익힐 수 있도록 돕습니다.

​	`axcl_sample_ivps` 바이너리는 /opt/bin 디렉터리에 있으며, IVPS 인터페이스 예제로 사용할 수 있습니다.

### 사용법
``` bash
Usage: /opt/bin/axcl_sample_ivps
        -d             (required) : device index from 0 to connected device num - 1
        -v             (required) : video frame input
        -g             (optional) : overlay input
        -s             (optional) : sp alpha input
        -n             (optional) : repeat number
        -r             (optional) : region config and update
        -l             (optional) : 0: no link 1. link ivps. 2: link venc. 3: link jenc
        --pipeline     (optional) : import ini file to config all the filters in one pipeline
        --pipeline_ext (optional) : import ini file to config all the filters in another pipeline
        --change       (optional) : import ini file to change parameters for one filter dynamicly
        --region       (optional) : import ini file to config region parameters
        --dewarp       (optional) : import ini file to config dewarp parameters, including LDC, perspective, fisheye, etc.
        --cmmcopy      (optional) : cmm copy API test
        --csc          (optional) : color space covert API test
        --fliprotation (optional) : flip and rotation API test
        --alphablend   (optional) : alpha blending API test
        --cropresize   (optional) : crop resize API test
        --osd          (optional) : draw osd API test
        --cover        (optional) : draw line/polygon API test
        -a             (optional) : all the sync API test

        --json         (optional) : axcl.json path

            -v  <PicPath>@<Format>@<Stride>x<Height>@<CropW>x<CropH>[+<CropX0>+<CropY0>]>
           e.g: -v /opt/bin/data/ivps/800x480car.nv12@3@800x480@600x400+100+50

           [-g] <PicPath>@<Format>@<Stride>x<Height>[+<DstX0>+<DstY0>*<Alpha>]>
           e.g: -g /opt/bin/data/ivps/rgb400x240.rgb24@161@400x240+100+50*150

           [-n] <repeat num>]
           [-r] <region num>]

        <PicPath>                     : source picture path
        <Format>                      : picture color format
                   3: NV12     YYYY... UVUVUV...
                   4: NV21     YYYY... VUVUVU...
                  10: NV16     YYYY... UVUVUV...
                  11: NV61     YYYY... VUVUVU...
                 161: RGB888   24bpp
                 165: BGR888   24bpp
                 160: RGB565   16bpp
                 197: ARGB4444 16bpp
                 203: RGBA4444 16bpp
                 199: ARGB8888 32bpp
                 201: RGBA8888 32bpp
                 198: ARGB1555 16bpp
                 202: RGBA5551 16bpp
                 200: ARGB8565 24bpp
                 204: RGBA5658 24bpp
                 205: ABGR4444 16bpp
                 211: BGRA4444 16bpp
                 207: ABGR8888 32bpp
                 209: BGRA8888 32bpp
                 206: ABGR1555 16bpp
                 210: BGRA5551 16bpp
                 208: ABGR8565 24bpp
                 212: BGRA5658 24bpp
                 224: BITMAP    1bpp
        <Stride>           (required) : picture stride (16 bytes aligned)
        <Stride>x<Height>  (required) : input frame stride and height (2 aligned)
        <CropW>x<CropH>    (required) : crop rect width & height (2 aligned)
        +<CropX0>+<CropY0> (optional) : crop rect coordinates
        +<DstX0>+<DstY0>   (optional) : output position coordinates
        <Alpha>            (optional) : ( (0, 255], 0: transparent; 255: opaque)

Example1:
        /opt/bin/axcl_sample_ivps -d 129 -v /opt/data/ivps/1920x1088.nv12@3@1920x1088@1920x1088  -n 1
```

> [!NOTE]
> - **-v**는 필수 항목이며, 입력 소스 이미지 경로와 프레임 정보를 지정합니다.
>   크롭 윈도우는 소스 이미지의 높이 범위 안에 있어야 합니다. 즉, CropX0 + CropW <= Stride, CropY0 + CropH <= Height여야 합니다.
>   크롭을 하지 않으면 CropW = Width, CropH = Height, CropX0 = 0, CropY0 = 0입니다.
> - **-n**은 소스 이미지를 지정한 횟수만큼 처리함을 나타냅니다. 파라미터가 -1이면 계속 반복 실행합니다.
>   IVPS의 proc 정보를 보려면 처리 횟수를 큰 값으로 설정하거나 계속 반복 실행하도록 해야 합니다.
>   IVPS proc 정보 확인 방법: cat proc/ax_proc/ivps.
> - **-r** 뒤의 입력 파라미터는 오버레이할 REGION 수이며, 현재 최대 4개입니다.
>   IVPS PIPELINE에서 REGION 오버레이는 비동기 동작이므로, 입력 소스 이미지에 실제로 오버레이되기까지 몇 프레임이 필요합니다.
>   따라서 REGION 기능을 확인하려면 -n 뒤의 파라미터를 더 크게 설정해야 하며, 값은 3.2보다 커야 합니다.

### 예제

1. 도움말 보기
   ``` bash
   axcl_sample_ivps -h
   ```

2. 소스 이미지(3840x2160 NV12 형식)를 한 번 처리
   ``` bash
   axcl_sample_ivps -v /opt/data/ivps/3840x2160.nv12@3@3840x2160@0x0+0+0 -d 0 -n 1
   ```

3. 소스 이미지(800x480 RGB 888 형식)를 크롭(X0=128 Y0=50 W=400 H=200)하여 세 번 처리
   ```bash
   axcl_sample_ivps -v /opt/data/ivps/800x480logo.rgb24@161@800x480@400x200+128+50 -d 0 -n 3
   ````

4. 소스 이미지(3840x2160 NV12 형식)에 REGION 3개를 오버레이하여 다섯 번 처리
   ```bash
   axcl_sample_ivps -v /opt/data/ivps/3840x2160.nv12@3@3840x2160@0x0+0+0 -d 0 -n 5 -r 3
   ````


실행에 성공하면 소스 이미지와 같은 디렉터리(/opt/data/ivps)에 다음 이미지가 생성되며, 도구로 열어 확인할 수 있습니다.

   - FlipMirrorRotate_chn0_480x800.fmt_a1
   - OSD_chn0_3840x2160.fmt_3
   - AlphaBlend_chn0_3840x2160.fmt_3
   - Rotate_chn0_1088x1920.fmt_3
   - CSC_chn0_3840x2160.fmt_3
   - CropResize_chn0_1280x720.fmt_3
   - PIPELINEoutput_grp1chn0_1920x1080.fmt_3
   - PIPELINEoutput_grp1chn1_2688x1520.fmt_a1
   - PIPELINEoutput_grp1chn2_768x1280.fmt_a1


> [!NOTE]
>
> - fmt_3: NV12 형식, fmt_a1: RGB 888 형식(a1은 16진수 0xa1을 의미)
> - 종료하려면 Ctrl + C를 누르세요.
> - 샘플 코드는 API 데모 용도로만 사용합니다.
>   실제 개발에서는 사용자가 구체적인 비즈니스 시나리오에 맞춰 파라미터를 설정해야 합니다.
> - 입력 이미지와 출력 이미지의 최대 해상도는 8192x8192입니다.
