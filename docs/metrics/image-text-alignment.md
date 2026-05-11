# `image_text_alignment`

## 用途

评估视觉内容和文字描述的语义对齐程度，主要用于检查任意文本字段与图像是否语义一致，也可以专门检查 `image_captioning` 生成的 caption artifact。

## 输入字段

- `image_field` 指向的图像字段，默认 `image`；也可以配置为 `image_path`
- `samples[].frame_path`
- `caption` 或任意配置的文本字段
- `text_source`: `caption` 或 `any_text`
- 可选：`text_signals[].text`

## 文本来源模式

| `text_source` | 行为 |
| --- | --- |
| `caption` | 只读取 `text_field`，默认是 `caption`，用于 caption + image alignment。 |
| `any_text` | 读取 `any_text_fields` 中存在的文本字段，默认覆盖 `text`、`input_text`、`output_text`、`caption`、`before_text`、`after_text`、`description_units[].description_text` 和 `text_signals[].text`。 |

## 上游产物

- `image_captioning` 产出的 `caption`。
- `image_loading` 产出的 `image_path`。
- `video_sampling` 产出的 `samples[].frame_path`。
- `video_multigranularity_description` 产出的 `description_units[].description_text`。
- 图文数据集原始 caption 或 annotation。

## 推荐模型

- `openai/clip-vit-base-patch32`

## 实现逻辑

```text
1. 根据 `text_source` 构建视觉-文本 pair，例如 image-text、image-caption、frame-description、frame-text_signal。
2. 用 CLIP 分别编码图像和文本。
3. 计算图像 embedding 与文本 embedding 的 cosine similarity，得到 pair-level CLIPScore。
4. 聚合所有 pair 的平均分，输出 `clip_score_mean`。
5. 根据 CLIPScore 阈值统计低对齐 pair 的比例，输出 `low_alignment_rate`。
```

## 输出指标

- `clip_score_mean`
- `low_alignment_rate`

## 可能扩展功能

| 功能 | 说明 |
| --- | --- |
| `clip_score_p10` | 观察低分尾部质量，比均值更敏感。 |
| `itm_score_mean` | 使用 BLIP image-text matching 输出匹配概率。 |
| `hard_negative_margin_mean` | 构造 batch 内 hard negative，评估正负样本区分度。 |
| `retrieval_recall_at_1` | 将图文对齐转成检索任务进行评估。 |
| `embedding_drift` | 对处理前后图文 embedding 变化做额外追踪。 |

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 缺少 `image_path` 且缺少 `samples[].frame_path` | `failed_precondition` |
| 缺少所有文本字段 | `failed_precondition` |
| 数据集不包含图像模态 | `not_applicable` |
