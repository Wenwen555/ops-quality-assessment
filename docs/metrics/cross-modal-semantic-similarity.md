# `cross_modal_semantic_similarity`

## 用途

评估不同模态产物是否表达一致语义，覆盖 image-text、frame-description、frame-text signal、description-answer 等 pair。

## 输入字段

- `image_path`
- `caption`
- `samples[].frame_path`
- `description_units[].description_text`
- `text_signals[].text`
- `qae_triplets[].question`
- `qae_triplets[].answer`

## 上游产物

- `image_captioning`
- `video_multigranularity_description`
- `video_text_evidence`
- `video_temporal_structure`
- `video_qae_materialization`

## 推荐模型

- CLIP
- BLIP
- BGE-M3
- Sentence-BERT

## 实现逻辑

```text
1. 构建同一样本内的 image-text、frame-description、frame-text_signal、description-answer 对。
2. 对视觉内容使用 CLIP/BLIP 编码。
3. 对文本内容使用 BGE/Sentence-BERT 编码。
4. 在可比较空间内计算 cosine similarity，或使用跨模态模型直接计算图文相似度。
5. 输出按 pair type 分组的均值、分位数和低分比例。
```

## 输出指标

- `image_caption_similarity`
- `frame_description_similarity`
- `frame_text_signal_similarity`
- `description_answer_similarity`
- `cross_modal_low_score_rate`

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 没有任意可构建的跨模态 pair | `not_applicable` |
| 指定必测 pair 的字段不完整 | `failed_precondition` |
