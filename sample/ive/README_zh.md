[English](README.md) | **中文** | [한국어](README_ko.md)

> 本文译自英文[原文](README.md)，如有出入以原文为准。

### 说明

此处的示例代码针对爱芯 SDK 包中提供的 IVE（智能视频分析引擎）模块，便于客户快速理解并正确使用 IVE 相关接口。
`axcl_sample_ive` 由本示例代码编译生成，位于 opt/bin 目录下，用于演示其用法。

### 用法
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

### 示例

> [!NOTE]
>
> - 示例代码仅用于 API 演示，实际使用时需根据用户场景配置具体参数。
> - 参数限制请参考名为《42 - AX IVE API》的文档。
> - 存放输入和输出数据的内存须由用户分配。
> - 输入和输出的图像数据须由用户指定。
> - 不同 CV 的输入图像（或数据）数量可能不同。
> - 二维图像的数据类型须明确指定，或使用默认值。
> - 这些关键参数以 Json 字符串或 Json 文件形式提供，请参考 /opt/data/ive/ 下相关目录中的 .json 文件和代码。

1. 显示帮助信息
   ```bash
   ./axcl_sample_ive -?
   ```

2. DMA 用法（源分辨率：1280 x 720，输入/输出类型：U8C1，使用 Json 文件配置控制参数）
   ```bash
   ./axcl_sample_ive -c 0 -w 1280 -h 720 -i /opt/data/ive/common/1280x720_u8c1_gray.yuv -o /opt/data/ive/dma/ -t 0 0 -p /opt/data/ive/dma/dma.json
   ```

3. MagAndAng 用法（源分辨率：1280 x 720，输入参数（grad_h、grad_v）的数据类型：U16C1，输出参数（ang_output）的数据类型：U8C1）
   ```bash
   ./axcl_sample_ive -c 8 -w 1280 -h 720 -i /opt/data/ive/common/1280x720_u16c1_gray.yuv /opt/data/ive/common/1280x720_u16c1_gray_2.yuv -o /opt/data/ive/common/mag_output.bin /opt/data/ive/common/ang_output.bin -t 9 9 9 0
   ```

### Json 文件中的关键参数
1. **dma.json**
   - `mode`、`x0`、`y0`、`h_seg`、`v_seg`、`elem_size` 和 `set_val` 分别为结构体 `AX_IVE_DMA_CTRL_T` 中对应成员的值，即 `enMode`、`u16CrpX0`、`u16CrpY0`、`u8HorSegSize`、`u8VerSegRows`、`u8ElemSize`、`u64Val`。
   - `w_out` 和 `h_out` 分别为输出图像的宽和高，仅用于 DMA 的 `AX_IVE_DMA_MODE_DIRECT_COPY` 模式。
2. **dualpics.json**
   - `x` 和 `y` 为结构体 `AX_IVE_ADD_CTRL_T` 中 `u1q7X` 和 `u1q7Y` 的值，用于 ADD CV。
   - `mode` 为结构体 `AX_IVE_SUB_CTRL_T` 中 `enMode` 的值，用于 Sub CV。
   - `mse_coef` 为结构体 `AX_IVE_MSE_CTRL_T` 中 `u1q15MseCoef` 的值，用于 MSE CV。
3. **ccl.json**
   - `mode` 为结构体 `AX_IVE_CCL_CTRL_T` 中 `enMode` 的值，用于 CCL CV。
4. **ed.json**
   - `mask` 为结构体 `AX_IVE_ERODE_CTRL_T`（用于 Erode CV）或 `AX_IVE_DILATE_CTRL_T`（用于 Dilate CV）中 `au8Mask[25]` 的全部值。
5. **filter.json**
   - `mask` 为结构体 `AX_IVE_FILTER_CTRL_T` 中 `as6q10Mask[25]` 的全部值，用于 Filter CV。
6. **hist.json**
   - `histeq_coef` 为结构体 `AX_IVE_EQUALIZE_HIST_CTRL_T` 中 `u0q20HistEqualCoef` 的值，用于 EqualizeHist CV。
7. **integ.json**
   - `out_ctl` 为结构体 `AX_IVE_INTEG_CTRL_T` 中 `enOutCtrl` 的值，用于 Integ CV。
8. **sobel.json**
   - `mask` 为结构体 `AX_IVE_SOBEL_CTRL_T` 中 `as6q10Mask[25]` 的值，用于 Sobel CV。
9. **gmm.json**
   - `init_var`、`min_var`、`init_w`、`lr`、`bg_r`、`var_thr` 和 `thr` 分别为结构体 `AX_IVE_GMM_CTRL_T` 中 `u14q4InitVar`、`u14q4MinVar`、`u1q10InitWeight`、`u1q7LearnRate`、`u1q7BgRatio`、`u4q4VarThr` 和 `u8Thr` 的值，用于 GMM CV。
10. **gmm2.json:**
    - `init_var`、`min_var`、`max_var`、`lr`、`bg_r`、`var_thr`、`var_thr_chk`、`ct` 和 `thr` 分别为结构体 `AX_IVE_GMM2_CTRL_T` 中 `u14q4InitVar`、`u14q4MinVar`、`u14q4MaxVar`、`u1q7LearnRate`、`u1q7BgRatio`、`u4q4VarThr`、`u4q4VarThrCheck`、`s1q7CT` 和 `u8Thr` 的值，用于 GMM2 CV。
11. **thresh.json**
    - `mode`、`thr_l`、`thr_h`、`min_val`、`mid_val` 和 `max_val` 分别为结构体 `AX_IVE_THRESH_CTRL_T` 中 `enMode`、`u8LowThr`、`u8HighThr`、`u8MinVal`、`u8MidVal` 和 `u8MaxVal` 的值，用于 Thresh CV。
12. **16bit_8bit.json**
    - `mode`、`gain` 和 `bias` 分别为结构体 `AX_IVE_16BIT_TO_8BIT_CTRL_T` 中 `enMode`、`s1q14Gain` 和 `s16Bias` 的值，用于 16BitTo8Bit CV。
13. **crop_resize.json**
    - 启用 CropImage 时，num 为结构体 `AX_IVE_CROP_IMAGE_CTRL_T` 中 `u16Num` 的值；boxs 为裁剪图像的数组类型，其中 `x`、`y`、`w` 和 `h` 分别为结构体 `AX_IVE_RECT_U16_T` 中 `u16X`、`u16Y`、`u16Width` 和 `u16Height` 的值。
    - 启用 CropResize 或 CropResizeForSplitYUV 模式时，`num` 为结构体 `AX_IVE_CROP_RESIZE_CTRL_T` 中 `u16Num` 的值；`align0`、`align1`、`enAlign[1]`、`bcolor`、`w_out` 和 `h_out` 分别为 `enAlign[0]`、`enAlign[1]`、`u32BorderColor` 以及输出图像的 `width` 和 `height` 的值。
14. **crop_resize2.json**
    - `num` 为结构体 `AX_IVE_CROP_IMAGE_CTRL_T` 中 `u16Num` 的值。
    - `res_out` 为输出图像宽高的数组。
    - **`src_boxs` 为从源图像裁剪的区域数组，`dst_boxs` 为缩放后图像的区域数组。**
15. **matmul.json**
    - `mau_i`、`ddr_rdw`、`en_mul_res`、`en_topn_res`、`order` 和 `topn` 分别为结构体 `AX_IVE_MAU_MATMUL_CTRL_T` 中 `enMauId`、`s32DdrReadBandwidthLimit`、`bEnableMulRes`、`bEnableTopNRes`、`enOrder` 和 `s32TopN` 的值。
    - `type_in` 为结构体 `AX_IVE_MAU_MATMUL_INPUT_T` 中 `stMatQ` 和 `stMatB` 的值。
    - `type_mul_res` 和 `type_topn_res` 分别为结构体 `AX_IVE_MAU_MATMUL_OUTPUT_T` 中 `stMulRes` 和 `sfTopNRes` 的值。
    - `q_shape` 和 `b_shape` 分别为结构体 `AX_IVE_MAU_MATMUL_INPUT_T` 中 `stMatQ` 和 `stMatB` 的 `pShape` 的值。