# `image_text_alignment`

## 用途

评估图像和文字描述的语义对齐程度，主要用于检查 `image_captioning` 或其他图文数据生成、清洗、转换后的质量。

## 输入字段

- `image_path`
- `caption`
- 可选：`text`、`description`、`question`、`answer`

## 上游产物

- `image_captioning` 产出的 `caption`。
- `image_loading` 产出的 `image_path`。
- 图文数据集原始 caption 或 annotation。

## 推荐模型

- `openai/clip-vit-base-patch32`
- BLIP image-text matching
- CLIPScore
- hard negative retrieval

## 实现逻辑

```text
1. 读取 image_path 和 caption。
2. 用 CLIP 分别编码图像和文本。
3. 计算 cosine similarity。
4. 在同 batch 内构造负样本，计算 positive score 与 hardest negative score 的 margin。
5. 可选用 BLIP ITM 输出匹配概率。
6. 聚合 mean、p10、p50、p90、low_score_rate、negative_margin。
```

## 输出指标

- `clip_score_mean`
- `clip_score_p10`
- `itm_score_mean`
- `retrieval_recall_at_1`
- `hard_negative_margin_mean`
- `low_alignment_rate`

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 缺少 `image_path` | `failed_precondition` |
| 缺少所有文本字段 | `failed_precondition` |
| 数据集不包含图像模态 | `not_applicable` |
