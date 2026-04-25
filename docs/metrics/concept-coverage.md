# `chair_object_hallucination`

## 用途

该指标现在用于评估 **visual subject caption alignment**，不再使用 COCO object list、手写 synonym 或数据集 object annotation。

它回答的问题是：

> 图像或采样帧里的视觉主体，是否被 caption / 中间描述文本正确覆盖？

## 输入字段

- `image_path` 或 `samples[].frame_path`
- `caption`，可通过 `text_field` 配置覆盖

## 推荐模型与资源

- HuggingFace `image-segmentation` 模型，输出必须包含 `mask`，例如 `facebook/detr-resnet-50-panoptic`
- visual recognition model，例如 `Salesforce/blip-image-captioning-base`
- text embedding model，例如 `BAAI/bge-m3`

## 实现逻辑

```text
1. 从 image_path 或 samples[].frame_path 读取图像。
2. 使用 HuggingFace `image-segmentation` 模型，按 `mask` 面积和置信度找到主体区域。
3. 裁剪主体区域，并用视觉识别模型生成主体名称或主体短描述。
4. 对主体名称和 caption / 中间文本重新计算 text embedding。
5. 计算主体文本与 caption 的 cosine similarity。
6. 根据阈值统计主体被提及比例和主体不匹配比例。
```

## 输出指标

| 指标 | 定义 | 方向 |
| --- | --- | --- |
| `subject_mention_rate` | 主体-caption 相似度高于 mention threshold 的比例 | 越高越好 |
| `subject_mismatch_rate` | 主体-caption 相似度低于 mismatch threshold 的比例 | 越低越好 |

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 数据集中没有图像或采样帧字段 | `not_applicable` |
| 缺少可评估文本字段 | `failed_precondition` |
| 主体分割、主体识别或主体-caption 相似度均无法计算 | `failed_precondition` |
