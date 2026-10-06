[English](README.md) | [中文](README_zh.md) | **한국어**

> 영어 [원문](README.md)을 번역한 문서입니다. 내용이 다르면 원문을 기준으로 합니다.

1）기능 설명:
이 모듈은 SDK 패키지에 포함된 비디오 디코딩 및 필터 유닛의 ffmpeg API 샘플 코드로 제공됩니다.
고객이 비디오 ffmpeg 디코딩 관련 인터페이스의 사용법을 빠르게 이해하고 익힐 수 있도록 설계되었습니다.

컴파일 후 실행 파일 axcl_sample_ffmpeg_vdec_ivps는 /opt/bin/axcl 디렉터리에 있으며, 비디오 디코딩 기능을 검증하는 데 사용할 수 있습니다.

-i : 입력 스트림 파일.
-c: 디코더 이름.     h264_axdec 또는 hevc_axdec
-y: YUV 프레임을 파일로 저장할지 여부; 0: 저장하지 않음; 1: 저장.
-r: 디코더 리사이즈, 축소 기능만 지원.       widthxheight

-f: yuv 리사이즈 및 포맷 전환용 필터.       ax_scale=width:height
-v: 로깅 레벨 설정

2）사용 예제:
예제 1: 도움말 정보 보기
/opt/bin/axcl/axcl_sample_ffmpeg_vdec_ivps  -h

예제 2: 1080p h264를 디코딩하고, 필터링된 yuv를 현재 디렉터리에 저장
/opt/bin/axcl/axcl_sample_ffmpeg_vdec_ivps -c h264_axdec -i 1080p.h264 -f "ax_scale=1280:720,hwdownload,format=nv12" -y 1

예제 3: 1080p h265를 디코딩하고, 필터링된 yuv를 현재 디렉터리에 저장
/opt/bin/axcl/axcl_sample_ffmpeg_vdec_ivps -c hevc_axdec -i 1080p.hevc -f "ax_scale=1280:720,hwdownload,format=nv12" -y 1

3）실행 결과:
실행에 성공하면 현재 디렉터리에 out.yuv라는 이름으로 필터링된 yuv 데이터가 생성되어야 하며, 사용자는 이 파일을 열어 실제 효과를 확인할 수 있습니다.

