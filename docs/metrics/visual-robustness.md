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

- `openai/clip-vit-base-patch32`
- SSIM

## 实现逻辑

```text
1. 为处理前图像和处理后图像构建 paired samples。
2. 使用 CLIP 编码处理前后图像，计算 embedding drift，并聚合为 `embedding_drift_mean`。
3. 使用 SSIM 计算处理前后图像结构相似度，并聚合为 `structural_similarity_mean`。
4. 按算子类型聚合上述分数，输出数据集级视觉稳定性结果。
```

## 输出指标

| 指标 | 含义 | 方向 |
| --- | --- | --- |
| `embedding_drift_mean` | 处理前后视觉 embedding 平均漂移 | 越低越好 |
| `structural_similarity_mean` | 处理前后结构相似度，例如 SSIM | 越高越好 |

## 可能扩展功能

| 功能 | 说明 |
| --- | --- |
| `perceptual_distance_mean` | 使用 LPIPS / DISTS 更细粒度衡量感知退化。 |
| `quality_stat_delta_mean` | 统计亮度、对比度、模糊度、噪声和颜色分布变化。 |
| `variant_dispersion_mean` | 衡量同一原图多个随机变换产物之间的稳定性。 |
| structured output checks | 对 Canny 等结构化输出检查 blank map、边缘密度和连通域异常。 |

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 缺少处理前 / 处理后配对样本 | `failed_precondition` |
| 处理后图像无法读取或解码 | `failed_precondition` |
| 数据集没有图像模态 | `not_applicable` |
