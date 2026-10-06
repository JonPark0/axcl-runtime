[English](README.md) | **中文** | [한국어](README_ko.md)

> 本文译自英文[原文](README.md)，如有出入以原文为准。

### 说明

​	Axera SDK 包中提供的 IVPS（图像视频处理系统）单元是一个视频图像处理子系统，提供裁剪、缩放、旋转、流式处理、CSC、OSD、马赛克、四边形等功能。

​	本模块为 IVPS 单元的示例代码，便于用户快速理解并掌握 IVPS 相关接口的使用方法。

​	`axcl_sample_ivps` 可执行文件位于 /opt/bin 目录下，可用于 IVPS 接口示例。

### 用法
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
> - **-v** 为必选项，后接输入源图像路径及帧信息。
>   裁剪窗口应位于源图像的高度范围内，即 CropX0 + CropW <= Stride，CropY0 + CropH <= Height。
>   若不进行裁剪，则 CropW = Width，CropH = Height，CropX0 = 0，CropY0 = 0。
> - **-n** 表示对源图像处理指定的次数。若该参数为 -1，则一直循环执行。
>   如需查看 IVPS 的 proc 信息，需要将处理次数设置为较大的值或一直循环执行。
>   IVPS proc 信息的查看方法：cat proc/ax_proc/ivps。
> - **-r** 后的输入参数为叠加的 REGION 数量，目前最大为 4。
>   REGION 在 IVPS PIPELINE 上的叠加为异步操作，需要经过若干帧之后才会真正叠加到输入源图像上。
>   因此，如需验证 REGION 功能，需要将 -n 后的参数设置得大一些，该值应大于 3.2。

### 示例

1. 查看帮助信息
   ``` bash
   axcl_sample_ivps -h
   ```

2. 对源图像（3840x2160 NV12 格式）处理一次
   ``` bash
   axcl_sample_ivps -v /opt/data/ivps/3840x2160.nv12@3@3840x2160@0x0+0+0 -d 0 -n 1
   ```

3. 对源图像（800x480 RGB 888 格式）进行裁剪（X0=128 Y0=50 W=400 H=200）处理，共三次
   ```bash
   axcl_sample_ivps -v /opt/data/ivps/800x480logo.rgb24@161@800x480@400x200+128+50 -d 0 -n 3
   ````

4. 对源图像（3840x2160 NV12 格式）处理五次，并叠加三个 REGION
   ```bash
   axcl_sample_ivps -v /opt/data/ivps/3840x2160.nv12@3@3840x2160@0x0+0+0 -d 0 -n 5 -r 3
   ````


运行成功后，将在源图像所在目录（/opt/data/ivps）下生成以下图像，可通过工具打开查看。

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
> - fmt_3：表示 NV12 格式；fmt_a1：表示 RGB 888 格式（a1 表示十六进制 0xa1）
> - 按 Ctrl + C 退出。
> - 示例代码仅用于 API 演示。
>   实际开发中，用户需要结合具体业务场景配置参数。
> - 输入图像和输出图像的最大分辨率为 8192x8192。
