# `image_text_alignment`

## 用途

评估视觉内容和文字描述的语义对齐程度，主要用于检查 `image_captioning`、视频采样帧描述或其他图文数据生成、清洗、转换后的质量。

## 输入字段

- `image_path` 或 `samples[].frame_path`
- `caption` 或 `description_units[].description_text`
- 可选：`text_signals[].text`

## 上游产物

- `image_captioning` 产出的 `caption`。
- `image_loading` 产出的 `image_path`。
- `video_sampling` 产出的 `samples[].frame_path`。
- `video_multigranularity_description` 产出的 `description_units[].description_text`。
- 图文数据集原始 caption 或 annotation。

## 推荐模型

- `openai/clip-vit-base-patch32`
- BLIP image-text matching
- CLIPScore
- hard negative retrieval

## 实现逻辑

```text
1. 构建视觉-文本 pair，例如 image-caption、frame-description、frame-text_signal。
2. 用 CLIP 分别编码图像和文本。
3. 计算 cosine similarity。
4. 在同 batch 内构造负样本，计算 positive score 与 hardest negative score 的 margin。
5. 可选对处理前后 embedding drift 做对比，用于发现文本改写或采样策略造成的语义偏移。
6. 可选用 BLIP ITM 输出匹配概率。
7. 按 pair type 聚合 mean、p10、p50、p90、low_score_rate、negative_margin。
```

## 输出指标

- `clip_score_mean`
- `clip_score_p10`
- `itm_score_mean`
- `retrieval_recall_at_1`
- `hard_negative_margin_mean`
- `embedding_drift`
- `low_alignment_rate`
- `pair_type_scores`

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 缺少 `image_path` 且缺少 `samples[].frame_path` | `failed_precondition` |
| 缺少所有文本字段 | `failed_precondition` |
| 数据集不包含图像模态 | `not_applicable` |
