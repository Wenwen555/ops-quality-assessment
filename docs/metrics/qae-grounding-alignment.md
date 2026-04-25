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

- BGE-M3

## 实现逻辑

```text
1. 根据 evidence_refs 回溯 scaffold_units、description_units、text_signals、temporal_structures、samples。
2. 判断每个 QAE triplet 是否能解析到可用 evidence，计算 `evidence_hit_rate`。
3. 使用 BGE-M3 计算 answer 与 evidence text 的语义相似度。
4. 根据相似度阈值判断 answer 是否被 evidence 支持，计算 `answer_support_rate`。
```

## 输出指标

- `answer_support_rate`
- `evidence_hit_rate`

## 可能扩展功能

| 功能 | 说明 |
| --- | --- |
| `temporal_grounding_rate` | 判断 evidence 的 timestamp_range 是否落在目标 segment 或 temporal unit 内。 |
| `missing_evidence_rate` | 使用 `1 - evidence_hit_rate` 表达缺失证据比例。 |
| NLI model | 对 answer-evidence 支持关系做更强的蕴含判断。 |
| VLM/LLM judge | 在 evidence 包含采样帧或复杂多模态证据时做可选判断。 |

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 缺少 `qae_triplets` | `not_applicable` |
| 存在 triplet 但缺少 `evidence_refs` | `failed_precondition` |
| 证据引用无法解析 | `warning` |
