# `qae_grounding_alignment`

## 用途

评估视频片段、问题、答案和证据之间的对齐关系，判断 QAE triplet 是否能回溯到可靠证据。

## 输入字段

- `qae_triplets`
- `scaffold_units`
- `description_units`
- `text_signals`
- `temporal_structures`
- `samples`

## 上游产物

- `video_scaffold_construction`
- `video_qae_materialization`
- `video_consistency_revision`
- `video_quality_screening`

## 推荐模型

- BGE / Sentence-BERT
- NLI model
- 可选 VLM/LLM judge

## 实现逻辑

```text
1. 根据 evidence_refs 回溯 scaffold_units、description_units、text_signals、temporal_structures、samples。
2. 判断每个 question 是否有可用 evidence。
3. 判断 answer 是否能被 evidence 文本或采样帧支持。
4. 判断 evidence 的 timestamp_range 是否落在目标 segment 或 temporal unit 内。
```

## 输出指标

- `answer_support_rate`
- `evidence_hit_rate`
- `temporal_grounding_rate`
- `unsupported_answer_rate`
- `missing_evidence_rate`

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 缺少 `qae_triplets` | `not_applicable` |
| 存在 triplet 但缺少 `evidence_refs` | `failed_precondition` |
| 证据引用无法解析 | sample-level failure |
