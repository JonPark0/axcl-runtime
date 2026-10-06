[English](README.md) | **中文** | [한국어](README_ko.md)

> 本文译自英文[原文](README.md)，如有出入以原文为准。

1）功能说明：
本模块是 SDK 包中提供的视频解码单元 ffmpeg api 示例代码。
旨在帮助客户快速理解和掌握视频 ffmpeg 解码相关接口的用法。

编译完成后，可执行文件 axcl_sample_ffmpeg_vdec 位于 /opt/bin/axcl 目录下，可用于验证视频解码功能。

-i : 输入码流文件。
-c：解码器名称。     h264_axdec 或 hevc_axdec
-y：是否将 YUV 帧保存到文件；0：不保存；1：保存。
-p：像素格式。               nv12、nv21，默认：nv12。
-r: 解码器缩放，仅支持缩小功能。       widthxheight
-v: 设置日志级别

2）使用示例：
示例 1：查看帮助信息
/opt/bin/axcl/axcl_sample_ffmpeg_vdec  -h

示例 2：解码 1080p h264，并将解码后的 yuv 保存到当前目录
/opt/bin/axcl/axcl_sample_ffmpeg_vdec -c h264_axdec -i /opt/data/vdec/ 1080p.h264 -y 1

示例 3：解码 1080p h265，并将解码后的 yuv 保存到当前目录
mount -t nfs -o nolock 10.126.12.109:/sw_nas/nfs_share /mnt
/opt/bin/axcl/axcl_sample_ffmpeg_vdec -c hevc_axdec -i /mnt/vdec/stream/cmodel_1080P_360frms_BJOpera.hevc -y 1

3）执行结果：
运行成功后，当前目录下应生成解码后的 yuv 数据，文件名为 file.yuv，用户可打开该文件查看实际效果。


