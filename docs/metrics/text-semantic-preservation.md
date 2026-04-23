# `text_semantic_preservation`

## 用途

评估文本处理前后是否保持语义，适用于规范化、纠错、去停用词、词形还原、分块等文本处理链路。

## 输入字段

- `source_text`
- `text`
- `normalized_text`
- `cleaned_text`
- `chunks`
- 可配置 `before_text_key`、`after_text_key`

## 上游产物

- `text_normalization`
- `remove_stopwords`
- `spelling_correction`
- `stemming_lemmatization`
- `remove_repetitions_punctuation`
- `text_chunk_mapping`
- `token_count_mapping`

## 推荐模型

- BGE-M3：通用文本 embedding 模型，用于计算 before / after 的语义相似度，并输出 `semantic_preservation_score` 和 `semantic_drift_rate`

## 实现逻辑

```text
1. 读取 before_text_key 和 after_text_key。
2. 使用 embedding cosine similarity 计算每个样本的 `semantic_preservation_score`。
3. 根据语义保持分数阈值识别明显语义漂移样本，并计算 `semantic_drift_rate`。
```

## 输出指标

- `semantic_preservation_score`
- `semantic_drift_rate`
- `nli_contradiction_rate`（可选扩展，第一版不实现）

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 缺少 before/after 文本字段 | `failed_precondition` |
| 数据集没有文本处理产物 | `not_applicable` |
