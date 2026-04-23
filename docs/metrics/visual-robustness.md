# `visual_robustness`

## 用途

评估图像算子处理前后，样本自身的视觉稳定性。该指标关注 **operator-level robustness** 而不是面对下游任务时的 robustness：经过 crop、flip、Canny、resize 等处理后，图像是否出现过度退化或结构漂移。

本阶段评测只做原图与算子产物之间的前后对比，以及同一原图多次变换产物之间的稳定性分析。

## 输入字段

- 处理前图像：`image_path` 或原始图像数据
- 处理后图像：`crop_image_data_list`、`flip_image_data_list`、`canny_image_data`
- 可选：算子配置，例如 crop scale、resize size、Canny threshold、flip direction

## 上游产物

- `image_random_crop`
- `image_random_flip`
- `image_canny`

## 推荐模型与工具

- CLIP / DINO image embedding
- SSIM / LPIPS / DISTS
- blur、brightness、contrast、noise、color histogram 等图像统计量
- edge density、connected components、blank image detector
- operator configuration parser

## 实现逻辑

```text
1. 为处理前图像和处理后图像构建 paired samples。
2. 计算原图与处理后图像的 embedding drift 和 perceptual distance。
3. 统计亮度、对比度、模糊度、噪声、颜色分布等质量变化。
4. 对随机变换产物计算 variant dispersion，识别同一原图下不稳定输出。
5. 对 Canny 等结构化输出检查 blank map、过密边缘、过稀边缘和连通域异常。
6. 按算子类型聚合稳定性分数，并输出高漂移、高退化样本。
```

## 输出指标

| 指标 | 含义 | 方向 |
| --- | --- | --- |
| `embedding_drift_mean` | 处理前后视觉 embedding 平均漂移 | 越低越好 |
| `perceptual_distance_mean` | 处理前后感知距离，例如 LPIPS / DISTS | 越低越好 |
| `structural_similarity_mean` | 处理前后结构相似度，例如 SSIM | 越高越好 |
| `quality_stat_delta_mean` | 亮度、对比度、模糊度、噪声等统计量变化 | 越低越好 |
| `variant_dispersion_mean` | 同一原图多个随机变换产物之间的离散度 | 越低越好 |


## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 缺少处理前 / 处理后配对样本 | `failed_precondition` |
| 处理后图像无法读取或解码 | `failed_precondition` |
| 数据集没有图像模态 | `not_applicable` |
