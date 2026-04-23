# `visual_transform_consistency`

## 用途

评估图像增强或视觉变换后是否保持应保留的视觉语义与结构。该指标关注 **consistency**：变换产物是否仍然是同一个可用样本，而不是只检查是否成功输出文件。

适用于 crop、flip、rotation、Canny、resize 等视觉变换。

## 输入字段

- `image_path` 或原始图像数据
- `crop_image_data_list`
- `flip_image_data_list`
- `canny_image_data`
- 可选：`objects`、`bbox`、`mask`

## 上游产物

- `image_random_crop`
- `image_random_flip`
- `image_canny`
- `image_size_filter`
- `image_aspect_ratio_filter`

## 推荐模型与工具

- CLIP / DINO image embedding
- image decoder / file existence checker

## 实现逻辑

```text
1. 为原图和变换图构建配对样本。
2. 检查变换产物是否存在且可解码，计算 `transform_output_success_rate`。
3. 使用 CLIP / DINO 分别编码原图和变换图，计算变换前后的 embedding similarity。
4. 聚合所有有效 pair 的相似度，输出 `semantic_similarity_before_after`。
```

## 输出指标

| 指标 | 含义 | 方向 |
| --- | --- | --- |
| `transform_output_success_rate` | 变换产物成功生成比例 | 越高越好 |
| `semantic_similarity_before_after` | 变换前后视觉 embedding 相似度 | 越高越好 |

## 可能扩展功能

| 功能 | 说明 |
| --- | --- |
| `expected_output_count_match_rate` | 检查输出数量是否符合 num_crops / num_augmentations 配置。 |
| `object_retention_rate` | 使用 object detector 或已有标注判断关键 object 是否被保留。 |
| `crop_area_ratio_valid_rate` | 检查 crop 面积比例和 aspect ratio 是否落在配置范围内。 |
| `edge_density_valid_rate` | 对 Canny 输出检查边缘密度是否合理。 |

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 缺少原图字段 | `failed_precondition` |
| 缺少变换产物字段 | `failed_precondition` |
| 没有启用图像增强算子 | `not_applicable` |
