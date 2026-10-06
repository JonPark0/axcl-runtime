[English](README.md) | **中文** | [한국어](README_ko.md)

> 本文译自英文[原文](README.md)，如有出入以原文为准。


1）功能说明：
- ut_ive 是用于演示的 IVE 应用程序

参数说明如下：
--c | --case_index: 默认值：0
    0-DMA
    1-DualPicCalc (Add、Sub、And、Or、Xor、Mse)
    2-HysEdge/CannyEdge
    3-CCL
    4-Erode/Dilate
    5-Filter 5x5
    6-Hist/EqualizeHist
    7-Integ
    8-MagAng
    9-Sobel 5x5 类 Sobel 梯度计算
    10-GMM GMM/GMM2
    11-Thresh
    12-16BitTo8Bit
    13-DMA-Sub-Hist
    14-Crop Resize (CropImage、CropResize、CropResizeForSplitYUV)
    15-CSC
    16-单线程多 CV 测试用例
    17-多线程多 CV 测试用例
-e | --engine_choice:
    0-IVE; 1-TDP; 2-VGP; 3-VPP; 4-GDC; 5-DSP; 6-NPU; 7-CPU; 8-MAU.
    CropImage : 可使用 IVE/VGP/VPP 硬件引擎中的任意一个
    CropResize/CropResizeForSplitYUV : 可使用 VGP/VPP 硬件引擎中的任意一个。
    CSC: 可使用 TDP/VGP/VPP 引擎中的任意一个。
    CropResize2/CropResize2ForSplitYUV: 可使用 VGP 或 VPP 引擎。
-m | --mode_choice: 默认值：0
    对于 DualPicCalc，通过以下选项启用其中一个 CV：
        0-add; 1-sub; 2-and; 3-or; 4-xor; 5-mse.
    对于 HysEdge/CannyEdge，启用 HysEdge 或 CannyEdge 的选项为
        0-hys edge; 1-canny edge.
    对于 Erode/Dilate，通过以下选项启用 Erode 或 Dilate：
        0-erode; 1-dilate.
    对于 Hist/EqualizeHist，通过以下选项启用 Hist 或 EqualizeHist：
        0-hist; 1-equalize hist.
    对于 GMM，通过以下选项启用 GMM 或 GMM2：
        0-gmm; 1-gmm2.
    对于 Crop Resize，通过以下选项启用 CropImage、CropResize 或 CropResizeForSplitYUV：
        0-crop image; 1-crop_resize; 2-cropresize_split_yuv.
    对于 CropResize2，通过以下选项启用 CropResize2 或 CropResize2ForSplitYUV：
        0-crop_resize2; 1-cropresize2_split_yuv.
-t | --type_image: 图像类型索引，取自枚举类型 AX_IVE_IMAGE_TYPE_E 和 AX_IMG_FORMAT_E（若引擎为 IVE，请参考
        AX_IVE_IMAGE_TYPE_E，否则请参考 AX_IMG_FORMAT_E）
    注意：
        1. 对于所有测试，需按照所指定输入、输出文件的顺序指定输入和输出图像类型。
        2. 若未指定类型，即传入的类型值为 -1，则按照 API 文档指定一个合法类型。
        3. 多个输入/输出图像类型之间以空格分隔。
        4. 一维数据类型（如 AX_IVE_MEM_INFO_T 类型）无需指定类型。
-i | --input_files: 输入图像（或数据）文件，若有多个输入，以空格分隔。
-o | --output_files: 输出图像（或数据）文件或目录，若有多个输出，以空格分隔
    注意：
        1. 具体信息请参考各测试用例对应的 /opt/data/eve/ 目录下的 JSON 文件。
        2. 对于 DMA、Crop Resize、CCL 中的 blob 以及 Crop Resize2，必须指定输出目录。
-w | --width: 默认值：1280
-h | --height: 默认值：720
-p | --param_list: 控制参数列表或 JSON 格式的文件
    注意：MagAng、Multi Calc 或 CSC 不需要此参数。
-a | --align_need: 是否启用宽度、高度和 stride 对齐，默认值：0
    0-否；1-是。
-? | --help: 显示使用说明。


2）示例演示：

示例一：显示帮助信息
./sample_ive -?

示例二：DMA 用法（源分辨率：1280 x 720，输入/输出类型：U8C1，使用 Json 文件配置控制参数）
./sample_ive -c 0 -w 1280 -h 720 -i /opt/data/ive/common/1280x720_u8c1_gray.yuv -o /opt/data/ive/dma/ -t 0 0 -p /opt/data/ive/dma/dma.json

示例三：MagAndAng 用法（源分辨率：1280 x 720，输入参数（grad_h、grad_v）的数据类型：U16C1，输出参数
                （ang_output）的数据类型：U8C1）
./sample_ive. -c 8 -w 1280 -h 720 -i /opt/data/ive/common/1280x720_u16c1_gray.yuv /opt/data/ive/common/1280x720_u16c1_gray_2.yuv -o /opt/data/ive/common/mag_output.bin /opt/data/ive/common/ang_output.bin -t 9 9 9 0

3) 结果：
运行成功后，将在输出目录中生成预期的图像或数据文件。


4）注意：

    a)示例代码仅用于 API 演示，实际使用时需根据用户场景配置具体参数。
    b)参数限制请参考名为《42 - AX IVE API》的文档。
    c)存放输入和输出数据的内存须由用户分配。
    d)输入和输出的图像数据须由用户指定。
    e)不同 CV 的输入图像（或数据）数量可能不同。
    f)二维图像的数据类型须明确指定，或使用默认值。
    h)这些关键参数以 Json 字符串或 Json 文件形式提供，请参考 /opt/data/ive/ 下相关目录中的 .json 文件和代码。


5）Json 文件中的关键参数：

    (1) dma.json 中：
        mode、x0、y0、h_seg、v_seg、elem_size 和 set_val 分别为结构体 AX_IVE_DMA_CTRL_T 中对应成员的值，
        即 enMode、u16CrpX0、u16CrpY0、u8HorSegSize、u8VerSegRows、u8ElemSize、u64Val。
        w_out 和 h_out 分别为输出图像的宽和高，仅用于 DMA 的 AX_IVE_DMA_MODE_DIRECT_COPY 模式。

    (2) dualpics.json 中：
        x 和 y 为结构体 AX_IVE_ADD_CTRL_T 中 u1q7X 和 u1q7Y 的值，用于 ADD CV。
        mode 为结构体 AX_IVE_SUB_CTRL_T 中 enMode 的值，用于 Sub CV。
        mse_coef 为结构体 AX_IVE_MSE_CTRL_T 中 u1q15MseCoef 的值，用于 MSE CV。

    (3) ccl.json 中：
        mode 为结构体 AX_IVE_CCL_CTRL_T 中 enMode 的值，用于 CCL CV。

    (4) ed.json 中：
        mask 为结构体 AX_IVE_ERODE_CTRL_T（用于 Erode CV）或 AX_IVE_DILATE_CTRL_T（用于 Dilate CV）中 au8Mask[25] 的全部值。

    (5) filter.json 中：
        mask 为结构体 AX_IVE_FILTER_CTRL_T 中 as6q10Mask[25] 的全部值，用于 Filter CV。

    (6) hist.jsom 中：
        histeq_coef 为结构体 AX_IVE_EQUALIZE_HIST_CTRL_T 中 u0q20HistEqualCoef 的值，用于 EqualizeHist CV。

    (7) integ.json 中：
        out_ctl 为结构体 AX_IVE_INTEG_CTRL_T 中 enOutCtrl 的值，用于 Integ CV。

    (8) sobel.json 中：
        mask 为结构体 AX_IVE_SOBEL_CTRL_T 中 as6q10Mask[25] 的值，用于 Sobel CV。

    (9) gmm.json 中：
        init_var、min_var、init_w、lr、bg_r、var_thr 和 thr 分别为结构体 AX_IVE_GMM_CTRL_T 中 u14q4InitVar、u14q4MinVar、u1q10InitWeight、u1q7LearnRate、u1q7BgRatio、u4q4VarThr 和 u8Thr 的值，用于 GMM CV。
        gmm2.json 中：
        init_var、min_var、max_var、lr、bg_r、var_thr、var_thr_chk、ct 和 thr 分别为结构体 AX_IVE_GMM2_CTRL_T 中 u14q4InitVar、u14q4MinVar、u14q4MaxVar、u1q7LearnRate、u1q7BgRatio、u4q4VarThr、u4q4VarThrCheck、s1q7CT 和 u8Thr 的值，用于 GMM2 CV。

    (10) thresh.json 中：
        mode、thr_l、thr_h、min_val、mid_val 和 max_val 分别为结构体 AX_IVE_THRESH_CTRL_T 中 enMode、u8LowThr、u8HighThr、u8MinVal、u8MidVal 和 u8MaxVal 的值，用于 Thresh CV

    (11) 16bit_8bit.json 中：
        mode、gain 和 bias 分别为结构体 AX_IVE_16BIT_TO_8BIT_CTRL_T 中 enMode、s1q14Gain 和 s16Bias 的值，用于 16BitTo8Bit CV。

    (12) crop_resize.json 中：
        启用 CropImage 时，num 为结构体 AX_IVE_CROP_IMAGE_CTRL_T 中 u16Num 的值；boxs 为裁剪图像的数组类型，其中 x、y、w 和 h 分别为结构体 AX_IVE_RECT_U16_T 中 u16X、u16Y、u16Width 和 u16Height 的值。
        启用 CropResize 或 CropResizeForSplitYUV 模式时，num 为结构体 AX_IVE_CROP_RESIZE_CTRL_T 中 u16Num 的值；align0、align1、enAlign[1]、bcolor、w_out 和 h_out 分别为 enAlign[0]、enAlign[1]、u32BorderColor 以及输出图像的 width 和 height 的值。

    (13) crop_resize2.json 中：
        num 为结构体 AX_IVE_CROP_IMAGE_CTRL_T 中 u16Num 的值；res_out 为输出图像宽高的数组；
        src_boxs 为从源图像裁剪的区域数组；dst_boxs 为缩放后图像的区域数组。

    (14) matmul.json 中：
        mau_i、ddr_rdw、en_mul_res、en_topn_res、order 和 topn 分别为结构体 AX_IVE_MAU_MATMUL_CTRL_T 中 enMauId、s32DdrReadBandwidthLimit、bEnableMulRes、bEnableTopNRes、enOrder 和 s32TopN 的值；type_in 为结构体 AX_IVE_MAU_MATMUL_INPUT_T 中 stMatQ 和 stMatB 的值；type_mul_res 和 type_topn_res 分别为结构体 AX_IVE_MAU_MATMUL_OUTPUT_T 中 stMulRes 和 sfTopNRes 的值；q_shape 和 b_shape 分别为结构体 AX_IVE_MAU_MATMUL_INPUT_T 中 stMatQ 和 stMatB 的 pShape 的值。