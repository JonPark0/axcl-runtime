[English](README.md) | [中文](README_zh.md) | **한국어**

> 영어 [원문](README.md)을 번역한 문서입니다. 내용이 다르면 원문을 기준으로 합니다.

### 설명

이 샘플 코드는 Aixin SDK 패키지에서 제공하는 IVE(Intelligent Video Analysis Engine) 모듈용으로, 고객이 IVE 관련 인터페이스를 빠르게 이해하고 올바르게 사용할 수 있도록 돕습니다.
`axcl_sample_ive`는 이 샘플 코드로 생성되어 opt/bin 디렉터리에 있으며, 해당 인터페이스의 사용 방법을 보여 줍니다.

### 사용법
```bash
Usage : ./axcl_sample_ive -c case_index [options]
        -d | --device_id: Device index from 0 to connected device num - 1, optional
        -c | --case_index:Calc case index, default:0
                0-DMA.
                1-DualPicCalc.
                2-HysEdge and CannyEdge.
                3-CCL.
                4-Erode and Dilate.
                5-Filter.
                6-Hist and EqualizeHist.
                7-Integ.
                8-MagAng.
                9-Sobel.
                10-GMM and GMM2.
                11-Thresh.
                12-16bit to 8bit.
                13-Multi Calc.
                14-Crop and Resize.
                15-CSC.
                16-CropResize2.
                17-MatMul.
        -e | --engine_choice:Choose engine id, default:0
                0-IVE; 1-TDP; 2-VGP; 3-VPP; 4-GDC; 5-DSP; 6-NPU; 7-CPU; 8-MAU.
                For Crop and Resize case, cropimage support IVE/VGP/VPP engine, cropresize and cropresize_split_yuv support VGP/VPP engine.
                For CSC case, support TDP/VGP/VPP engine.
                For CropResize2 case, support VGP/VPP engine.
                For MatMul case, support NPU/MAU engine.
        -m | --mode_choice:Choose test mode, default:0
                For DualPicCalc case, indicate dual pictures calculation task:
                  0-add; 1-sub; 2-and; 3-or; 4-xor; 5-mse.
                For HysEdge and CannyEdge case, indicate hys edge or canny edge calculation task:
                  0-hys edge; 1-canny edge.
                For Erode and Dilate case, indicate erode or dilate calculation task:
                  0-erode; 1-dilate.
                For Hist and EqualizeHist case, indicate hist or equalize hist calculation task:
                  0-hist; 1-equalize hist.
                For GMM and GMM2 case, indicate gmm or gmm2 calculation task:
                  0-gmm; 1-gmm2.
                For Crop and Resize case, indicate cropimage, cropresize, cropresize_split_yuv calculation task:
                  0-crop image; 1-crop_resize; 2-cropresize_split_yuv.
                For CropResize2 case, indicate crop_resize2 or cropresize2_split_yuv calculation task:
                  0-crop_resize2; 1-cropresize2_split_yuv.
        -t | --type_image:Image type index refer to IVE_IMAGE_TYPE_E(IVE engine) or AX_IMG_FORMAT_E(other engine)
                Note:
                  1. For all case, both input and output image types need to be specified in the same order as the specified input and output file order.
                  2. If no type is specified, i.e. a type value of -1 is passed in, then a legal type is specified, as qualified by the API documentation.
                  3. Multiple input and output image types, separated by spaces.
                  4. For One-dimensional data (such as AX_IVE_MEM_INFO_T type data), do not require a type to be specified.
        -i | --input_files:Input image files, if there are multiple inputs, separated by spaces.
        -o | --output_files:Output image files or dir, if there are multiple outputs, separated by spaces
                Note:for DMA, Crop Resize, blob of CCL case and CropResize2 case must be specified as directory.
        -w | --width:Image width of inputs, default:1280.
        -h | --height:Image height of inputs, default:720.
        -p | --param_list:Control parameters list or file(in json data format)
                Note:
                  5. Please refer to the json file in the '/opt/data/ive/' corresponding directory of each test case.
                  6. For MagAng, Multi Calc and CSC case, no need control parameters.
        -a | --align_need:Does the width/height/stride need to be aligned automatically, default:0.
                  0-no; 1-yes.
        -? | --help:Show usage help.
```

### 예제

> [!NOTE]
>
> - 샘플 코드는 API 데모용일 뿐이며, 실제로는 사용자 환경에 맞는 구체적인 설정 파라미터가 필요합니다.
> - 파라미터 제한 사항은 "42 - AX IVE API" 문서를 참고하세요.
> - 입력 및 출력 데이터를 담을 메모리는 사용자가 할당해야 합니다.
> - 입력 및 출력 이미지 데이터는 사용자가 지정해야 합니다.
> - CV마다 입력 이미지(또는 데이터)의 개수가 다를 수 있습니다.
> - 2차원 이미지의 데이터 타입은 명확하게 정의하거나 기본값으로 두어야 합니다.
> - 주요 파라미터는 Json 문자열 또는 Json 파일 형식입니다. /opt/data/ive/의 여러 디렉터리에 있는 .json 파일과 코드를 참고하세요.

1. 도움말 표시
   ```bash
   ./axcl_sample_ive -?
   ```

2. DMA 사용 예(소스 해상도: 1280 x 720, 입력/출력 타입: U8C1, Json 파일로 제어 파라미터 설정)
   ```bash
   ./axcl_sample_ive -c 0 -w 1280 -h 720 -i /opt/data/ive/common/1280x720_u8c1_gray.yuv -o /opt/data/ive/dma/ -t 0 0 -p /opt/data/ive/dma/dma.json
   ```

3. MagAndAng 사용 예(소스 해상도: 1280 x 720, 입력 파라미터(grad_h, grad_v)의 데이터 타입: U16C1, 출력 파라미터(ang_output)의 데이터 타입: U8C1)
   ```bash
   ./axcl_sample_ive -c 8 -w 1280 -h 720 -i /opt/data/ive/common/1280x720_u16c1_gray.yuv /opt/data/ive/common/1280x720_u16c1_gray_2.yuv -o /opt/data/ive/common/mag_output.bin /opt/data/ive/common/ang_output.bin -t 9 9 9 0
   ```

### Json 파일의 주요 파라미터
1. **dma.json**
   - `mode`, `x0`, `y0`, `h_seg`, `v_seg`, `elem_size`, `set_val`은 구조체 `AX_IVE_DMA_CTRL_T`에서 각각 대응하는 멤버인 `enMode`, `u16CrpX0`, `u16CrpY0`, `u8HorSegSize`, `u8VerSegRows`, `u8ElemSize`, `u64Val`의 값입니다.
   - `w_out`과 `h_out`은 각각 출력 이미지의 너비와 높이이며, DMA의 `AX_IVE_DMA_MODE_DIRECT_COPY` 모드에서만 사용합니다.
2. **dualpics.json**
   - `x`와 `y`는 ADD CV용 구조체 `AX_IVE_ADD_CTRL_T`의 `u1q7X`와 `u1q7Y` 값입니다.
   - `mode`는 Sub CV용 구조체 `AX_IVE_SUB_CTRL_T`의 `enMode` 값입니다.
   - `mse_coef`는 MSE CV용 구조체 `AX_IVE_MSE_CTRL_T`의 `u1q15MseCoef` 값입니다.
3. **ccl.json**
   - `mode`는 CCL CV용 구조체 `AX_IVE_CCL_CTRL_T`의 `enMode` 값입니다.
4. **ed.json**
   - `mask`는 Erode CV용 구조체 `AX_IVE_ERODE_CTRL_T` 또는 Dilate CV용 `AX_IVE_DILATE_CTRL_T`에 있는 `au8Mask[25]`의 모든 값입니다.
5. **filter.json**
   - `mask`는 Filter CV용 구조체 `AX_IVE_FILTER_CTRL_T`에 있는 `as6q10Mask[25]`의 모든 값입니다.
6. **hist.json**
   - `histeq_coef`는 EqualizeHist CV용 구조체 `AX_IVE_EQUALIZE_HIST_CTRL_T`의 `u0q20HistEqualCoef` 값입니다.
7. **integ.json**
   - `out_ctl`은 Integ CV용 구조체 `AX_IVE_INTEG_CTRL_T`의 `enOutCtrl` 값입니다.
8. **sobel.json**
   - `mask`는 Sobel CV용 구조체 `AX_IVE_SOBEL_CTRL_T`의 `as6q10Mask[25]` 값입니다.
9. **gmm.json**
   - `init_var`, `min_var`, `init_w`, `lr`, `bg_r`, `var_thr`, `thr`는 각각 GMM CV용 구조체 `AX_IVE_GMM_CTRL_T`의 `u14q4InitVar`, `u14q4MinVar`, `u1q10InitWeight`, `u1q7LearnRate`, `u1q7BgRatio`, `u4q4VarThr`, `u8Thr` 값입니다.
10. **gmm2.json:**
    - `init_var`, `min_var`, `max_var`, `lr`, `bg_r`, `var_thr`, `var_thr_chk`, `ct`, `thr`는 각각 GMM2 CV용 구조체 `AX_IVE_GMM2_CTRL_T`의 `u14q4InitVar`, `u14q4MinVar`, `u14q4MaxVar`, `u1q7LearnRate`, `u1q7BgRatio`, `u4q4VarThr`, `u4q4VarThrCheck`, `s1q7CT`, `u8Thr` 값입니다.
11. **thresh.json**
    - `mode`, `thr_l`, `thr_h`, `min_val`, `mid_val`, `max_val`은 각각 Thresh CV용 구조체 `AX_IVE_THRESH_CTRL_T`의 `enMode`, `u8LowThr`, `u8HighThr`, `u8MinVal`, `u8MidVal`, `u8MaxVal` 값입니다.
12. **16bit_8bit.json**
    - `mode`, `gain`, `bias`는 각각 16BitTo8Bit CV용 구조체 `AX_IVE_16BIT_TO_8BIT_CTRL_T`의 `enMode`, `s1q14Gain`, `s16Bias` 값입니다.
13. **crop_resize.json**
    - CropImage가 활성화되면 num은 구조체 `AX_IVE_CROP_IMAGE_CTRL_T`의 `u16Num` 값이고, boxs는 크롭 이미지의 배열 타입이며, 그 안의 `x`, `y`, `w`, `h`는 각각 구조체 `AX_IVE_RECT_U16_T`의 `u16X`, `u16Y`, `u16Width`, `u16Height` 값입니다.
    - CropResize 또는 CropResizeForSplitYUV 모드가 활성화되면 `num`은 구조체 `AX_IVE_CROP_RESIZE_CTRL_T`의 `u16Num` 값이고, `align0`, `align1`, `enAlign[1]`, `bcolor`, `w_out`, `h_out`은 각각 `enAlign[0]`, `enAlign[1]`, `u32BorderColor`, 출력 이미지의 `width`와 `height` 값입니다.
14. **crop_resize2.json**
    - `num`은 구조체 `AX_IVE_CROP_IMAGE_CTRL_T`의 `u16Num` 값입니다.
    - `res_out`은 출력 이미지의 너비와 높이 배열입니다.
    - **`src_boxs`는 소스 이미지의 크롭 영역 배열이고, `dst_boxs`는 리사이즈된 이미지의 영역 배열입니다.**
15. **matmul.json**
    - `mau_i`, `ddr_rdw`, `en_mul_res`, `en_topn_res`, `order`, `topn`은 각각 구조체 `AX_IVE_MAU_MATMUL_CTRL_T`의 `enMauId`, `s32DdrReadBandwidthLimit`, `bEnableMulRes`, `bEnableTopNRes`, `enOrder`, `s32TopN` 값입니다.
    - `type_in`은 구조체 `AX_IVE_MAU_MATMUL_INPUT_T`의 `stMatQ`와 `stMatB` 값입니다.
    - `type_mul_res`와 `type_topn_res`는 구조체 `AX_IVE_MAU_MATMUL_OUTPUT_T`의 `stMulRes`와 `sfTopNRes` 값입니다.
    - `q_shape`와 `b_shape`는 구조체 `AX_IVE_MAU_MATMUL_INPUT_T`의 `stMatQ`와 `stMatB`에 있는 `pShape` 값입니다.