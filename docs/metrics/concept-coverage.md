# `concept_coverage`

## 用途

评估文本、图像、视频帧中关键概念是否被覆盖，避免漏检和幻觉。

## 输入字段

- `caption`
- `description_units[].description_text`
- `text_signals[].text`
- `samples[].frame_path`
- 可选人工 reference concepts

## 上游产物

- `image_captioning`
- `video_multigranularity_description`
- `video_text_evidence`
- `video_scaffold_construction`

## 推荐模型

- KeyBERT
- spaCy
- jieba / THULAC
- BLIP caption concept extraction
- GroundingDINO / DETR / YOLO
- action recognition model

## 实现逻辑

```text
1. 从文本中抽取对象、动作、场景、实体、属性。
2. 从图像或采样帧中抽取视觉对象、动作、场景。
3. 将概念标准化，例如大小写、同义词、中文分词、lemma。
4. 计算视觉概念和文本概念的交集、缺失项和幻觉项。
5. 输出 precision、recall、F1、missing_concepts、hallucinated_concepts。
```

## 输出指标

- `concept_precision`
- `concept_recall`
- `concept_f1`
- `missing_concept_count`
- `hallucinated_concept_count`
- `top_missing_concepts`
- `top_hallucinated_concepts`

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 没有文本字段也没有视觉字段 | `not_applicable` |
| 指定 reference concepts 但无法读取 | `failed_precondition` |
