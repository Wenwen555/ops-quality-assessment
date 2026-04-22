# `modality_gap`

## 用途

评估同一样本内不同模态之间的距离，以及与随机负样本之间的 margin。

## 输入字段

- `image_path`
- `caption`
- `samples[].frame_path`
- `description_units`
- `text_signals`

## 上游产物

- `image_captioning`
- `video_sampling`
- `video_multigranularity_description`
- `video_text_evidence`

## 推荐模型

- CLIP
- BGE / Sentence-BERT
- MMD / embedding mean distance
- random negative sampling

## 实现逻辑

```text
1. 为样本内各模态生成 embedding。
2. 计算正样本跨模态距离。
3. 随机抽取 batch 内负样本，计算负样本距离。
4. 计算 positive-negative margin。
5. 对处理前后 embedding drift 做对比。
```

## 输出指标

- `positive_pair_similarity`
- `negative_pair_similarity`
- `alignment_margin`
- `embedding_drift`
- `modality_gap_score`

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 只能构建单一模态 embedding | `not_applicable` |
| 启用 margin 但 batch 中负样本不足 | `failed_precondition` |
