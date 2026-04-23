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
- object detector
- SSIM / LPIPS
- edge density statistics
- label-preservation rules

## 实现逻辑

```text
1. 为原图和变换图构建配对样本。
2. 检查变换输出数量是否符合配置，例如 num_crops / num_augmentations。
3. 计算原图与变换图的 embedding similarity。
4. 检查目标 object 或 salient region 是否被保留。
5. 对 crop 统计 crop area ratio 和 aspect ratio 是否落在配置范围。
6. 对 Canny 统计 edge density，检测 blank edge map 或过密边缘图。
7. 聚合一致性分数和失败样本。
```

## 输出指标

| 指标 | 含义 | 方向 |
| --- | --- | --- |
| `transform_output_success_rate` | 变换产物成功生成比例 | 越高越好 |
| `expected_output_count_match_rate` | 输出数量符合配置比例 | 越高越好 |
| `semantic_similarity_before_after` | 变换前后视觉 embedding 相似度 | 越高越好 |
| `object_retention_rate` | 关键 object 被保留比例 | 越高越好 |
| `crop_area_ratio_valid_rate` | crop 面积比例符合配置比例 | 越高越好 |
| `edge_density_valid_rate` | Canny 边缘密度合理比例 | 越高越好 |
| `failed_transform_samples` | 失败样本列表 | 用于复核 |

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 缺少原图字段 | `failed_precondition` |
| 缺少变换产物字段 | `failed_precondition` |
| 没有启用图像增强算子 | `not_applicable` |
| 缺少 label / object annotation | label/object 相关指标 `not_applicable` |

