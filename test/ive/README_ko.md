[English](README.md) | [中文](README_zh.md) | **한국어**

> 영어 [원문](README.md)을 번역한 문서입니다. 내용이 다르면 원문을 기준으로 합니다.


1）기능 설명:
- ut_ive는 데모용 IVE 애플리케이션입니다.

파라미터 설명은 다음과 같습니다:
--c | --case_index: 기본값: 0
    0-DMA
    1-DualPicCalc (Add, Sub, And, Or, Xor, Mse)
    2-HysEdge/CannyEdge
    3-CCL
    4-Erode/Dilate
    5-Filter 5x5
    6-Hist/EqualizeHist
    7-Integ
    8-MagAng
    9-Sobel 5x5 Sobel 유사 그래디언트 계산
    10-GMM GMM/GMM2
    11-Thresh
    12-16BitTo8Bit
    13-DMA-Sub-Hist
    14-Crop Resize (CropImage, CropResize, CropResizeForSplitYUV)
    15-CSC
    16-단일 스레드, 다중 CV 테스트 케이스
    17-다중 스레드, 다중 CV 테스트 케이스
-e | --engine_choice:
    0-IVE; 1-TDP; 2-VGP; 3-VPP; 4-GDC; 5-DSP; 6-NPU; 7-CPU; 8-MAU.
    CropImage: IVE/VGP/VPP 하드웨어 엔진 중 하나를 사용할 수 있습니다.
    CropResize/CropResizeForSplitYUV: VGP/VPP 하드웨어 엔진 중 하나를 사용할 수 있습니다.
    CSC: TDP/VGP/VPP 엔진 중 하나를 사용할 수 있습니다.
    CropResize2/CropResize2ForSplitYUV: VGP 또는 VPP 엔진을 사용할 수 있습니다.
-m | --mode_choice: 기본값: 0
    DualPicCalc에서는 아래 옵션으로 다음 CV 중 하나를 활성화합니다:
        0-add; 1-sub; 2-and; 3-or; 4-xor; 5-mse.
    HysEdge/CannyEdge에서는 다음 값으로 HysEdge 또는 CannyEdge를 활성화합니다:
        0-hys edge; 1-canny edge.
    Erode/Dilate에서는 아래 옵션으로 Erode 또는 Dilate를 활성화합니다:
        0-erode; 1-dilate.
    Hist/EqualizeHist에서는 아래 옵션으로 Hist 또는 EqualizeHist를 활성화합니다:
        0-hist; 1-equalize hist.
    GMM에서는 아래 옵션으로 GMM 또는 GMM2를 활성화합니다:
        0-gmm; 1-gmm2.
    Crop Resize에서는 아래 옵션으로 CropImage, CropResize 또는 CropResizeForSplitYUV를 활성화합니다:
        0-crop image; 1-crop_resize; 2-cropresize_split_yuv.
    CropResize2에서는 아래 옵션으로 CropResize2 또는 CropResize2ForSplitYUV를 활성화합니다:
        0-crop_resize2; 1-cropresize2_split_yuv.
-t | --type_image: AX_IVE_IMAGE_TYPE_E 및 AX_IMG_FORMAT_E 열거형의 이미지 타입 인덱스(엔진이 IVE이면
        AX_IVE_IMAGE_TYPE_E를, 그 외에는 AX_IMG_FORMAT_E를 참고하세요)
    참고:
        1. 모든 테스트에서 입력 및 출력 이미지 타입은 지정한 입력 및 출력 파일 순서대로 지정해야 합니다.
        2. 타입을 지정하지 않으면, 즉 전달한 타입 값이 -1이면 API 문서에 따라 유효한 타입이 지정됩니다.
        3. 여러 입력/출력 이미지 타입은 공백으로 구분합니다.
        4. 1차원 데이터 타입(예: AX_IVE_MEM_INFO_T 타입)은 타입을 지정할 필요가 없습니다.
-i | --input_files: 입력 이미지(또는 데이터) 파일. 입력이 여러 개이면 공백으로 구분합니다.
-o | --output_files: 출력 이미지(또는 데이터) 파일 또는 디렉터리. 출력이 여러 개이면 공백으로 구분합니다.
    참고:
        1. 자세한 내용은 각 테스트 케이스에 해당하는 /opt/data/eve/ 디렉터리의 JSON 파일을 참고하세요.
        2. DMA, Crop Resize, CCL의 blob, Crop Resize2의 경우 출력 디렉터리를 지정해야 합니다.
-w | --width: 기본값: 1280
-h | --height: 기본값: 720
-p | --param_list: JSON 형식의 제어 파라미터 목록 또는 파일
    참고: MagAng, Multi Calc, CSC에는 필요하지 않습니다.
-a | --align_need: 너비, 높이, 스트라이드 정렬 사용 여부, 기본값: 0
    0-아니요; 1-예.
-? | --help: 사용법 도움말을 표시합니다.


2）샘플 데모:

예제 1: 도움말 표시
./sample_ive -?

예제 2: DMA 사용 예(소스 해상도: 1280 x 720, 입력/출력 타입: U8C1, Json 파일로 제어 파라미터 설정)
./sample_ive -c 0 -w 1280 -h 720 -i /opt/data/ive/common/1280x720_u8c1_gray.yuv -o /opt/data/ive/dma/ -t 0 0 -p /opt/data/ive/dma/dma.json

예제 3: MagAndAng 사용 예(소스 해상도: 1280 x 720, 입력 파라미터(grad_h, grad_v)의 데이터 타입: U16C1, 출력 파라미터
                (ang_output)의 데이터 타입: U8C1)
./sample_ive. -c 8 -w 1280 -h 720 -i /opt/data/ive/common/1280x720_u16c1_gray.yuv /opt/data/ive/common/1280x720_u16c1_gray_2.yuv -o /opt/data/ive/common/mag_output.bin /opt/data/ive/common/ang_output.bin -t 9 9 9 0

3) 결과:
실행에 성공하면 출력 디렉터리에 예상한 이미지 또는 데이터 파일이 생성됩니다.


4）참고:

    a) 샘플 코드는 API 데모용일 뿐이며, 실제로는 사용자 환경에 맞는 구체적인 설정 파라미터가 필요합니다.
    b) 파라미터 제한 사항은 "42 - AX IVE API" 문서를 참고하세요.
    c) 입력 및 출력 데이터를 담을 메모리는 사용자가 할당해야 합니다.
    d) 입력 및 출력 이미지 데이터는 사용자가 지정해야 합니다.
    e) CV마다 입력 이미지(또는 데이터)의 개수가 다를 수 있습니다.
    f) 2차원 이미지의 데이터 타입은 명확하게 정의하거나 기본값으로 두어야 합니다.
    h) 주요 파라미터는 Json 문자열 또는 Json 파일 형식입니다. /opt/data/ive/의 여러 디렉터리에 있는 .json 파일과 코드를 참고하세요.


5）Json 파일의 주요 파라미터:

    (1) dma.json에서:
        mode, x0, y0, h_seg, v_seg, elem_size, set_val은 구조체 AX_IVE_DMA_CTRL_T에서 각각 대응하는 멤버인
        enMode, u16CrpX0, u16CrpY0, u8HorSegSize, u8VerSegRows, u8ElemSize, u64Val의 값입니다.
        w_out과 h_out은 각각 출력 이미지의 너비와 높이이며, DMA의 AX_IVE_DMA_MODE_DIRECT_COPY 모드에서만 사용합니다.

    (2) dualpics.json에서:
        x와 y는 ADD CV용 구조체 AX_IVE_ADD_CTRL_T의 u1q7X와 u1q7Y 값입니다.
        mode는 Sub CV용 구조체 AX_IVE_SUB_CTRL_T의 enMode 값입니다.
        mse_coef는 MSE CV용 구조체 AX_IVE_MSE_CTRL_T의 u1q15MseCoef 값입니다.

    (3) ccl.json에서:
        mode는 CCL CV용 구조체 AX_IVE_CCL_CTRL_T의 enMode 값입니다.

    (4) ed.json에서:
        mask는 Erode CV용 구조체 AX_IVE_ERODE_CTRL_T 또는 Dilate CV용 AX_IVE_DILATE_CTRL_T에 있는 au8Mask[25]의 모든 값입니다.

    (5) filter.json에서:
        mask는 Filter CV용 구조체 AX_IVE_FILTER_CTRL_T에 있는 as6q10Mask[25]의 모든 값입니다.

    (6) hist.jsom에서:
        histeq_coef는 EqualizeHist CV용 구조체 AX_IVE_EQUALIZE_HIST_CTRL_T의 u0q20HistEqualCoef 값입니다.

    (7) integ.json에서:
        out_ctl은 Integ CV용 구조체 AX_IVE_INTEG_CTRL_T의 enOutCtrl 값입니다.

    (8) sobel.json에서:
        mask는 Sobel CV용 구조체 AX_IVE_SOBEL_CTRL_T의 as6q10Mask[25] 값입니다.

    (9) gmm.json에서:
        init_var, min_var, init_w, lr, bg_r, var_thr, thr는 각각 GMM CV용 구조체 AX_IVE_GMM_CTRL_T의 u14q4InitVar, u14q4MinVar, u1q10InitWeight, u1q7LearnRate, u1q7BgRatio, u4q4VarThr, u8Thr 값입니다.
        gmm2.json에서:
        init_var, min_var, max_var, lr, bg_r, var_thr, var_thr_chk, ct, thr는 각각 GMM2 CV용 구조체 AX_IVE_GMM2_CTRL_T의 u14q4InitVar, u14q4MinVar, u14q4MaxVar, u1q7LearnRate, u1q7BgRatio, u4q4VarThr, u4q4VarThrCheck, s1q7CT, u8Thr 값입니다.

    (10) thresh.json에서:
        mode, thr_l, thr_h, min_val, mid_val, max_val은 각각 Thresh CV용 구조체 AX_IVE_THRESH_CTRL_T의 enMode, u8LowThr, u8HighThr, u8MinVal, u8MidVal, u8MaxVal 값입니다.

    (11) 16bit_8bit.json에서:
        mode, gain, bias는 각각 16BitTo8Bit CV용 구조체 AX_IVE_16BIT_TO_8BIT_CTRL_T의 enMode, s1q14Gain, s16Bias 값입니다.

    (12) crop_resize.json에서:
        CropImage가 활성화되면 num은 구조체 AX_IVE_CROP_IMAGE_CTRL_T의 u16Num 값이고, boxs는 크롭 이미지의 배열 타입이며, 그 안의 x, y, w, h는 각각 구조체 AX_IVE_RECT_U16_T의 u16X, u16Y, u16Width, u16Height 값입니다.
        CropResize 또는 CropResizeForSplitYUV 모드가 활성화되면 num은 구조체 AX_IVE_CROP_RESIZE_CTRL_T의 u16Num 값이고, align0, align1, enAlign[1], bcolor, w_out, h_out은 각각 enAlign[0], enAlign[1], u32BorderColor, 출력 이미지의 width와 height 값입니다.

    (13) crop_resize2.json에서:
        num은 구조체 AX_IVE_CROP_IMAGE_CTRL_T의 u16Num 값입니다. res_out은 출력 이미지의 너비와 높이 배열입니다.
        src_boxs는 소스 이미지의 크롭 영역 배열이고, dst_boxs는 리사이즈된 이미지의 영역 배열입니다.

    (14) matmul.json에서:
        mau_i, ddr_rdw, en_mul_res, en_topn_res, order, topn은 각각 구조체 AX_IVE_MAU_MATMUL_CTRL_T의 enMauId, s32DdrReadBandwidthLimit, bEnableMulRes, bEnableTopNRes, enOrder, s32TopN 값입니다. type_in은 구조체 AX_IVE_MAU_MATMUL_INPUT_T의 stMatQ와 stMatB 값입니다. type_mul_res와 type_topn_res는 구조체 AX_IVE_MAU_MATMUL_OUTPUT_T의 stMulRes와 sfTopNRes 값입니다. q_shape와 b_shape는 구조체 AX_IVE_MAU_MATMUL_INPUT_T의 stMatQ와 stMatB에 있는 pShape 값입니다.