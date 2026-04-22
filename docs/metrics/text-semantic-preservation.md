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

- BGE-M3
- Sentence-BERT
- BERTScore
- ROUGE-L
- BLEU
- NLI contradiction model

## 实现逻辑

```text
1. 读取 before_text_key 和 after_text_key。
2. 计算 embedding cosine similarity。
3. 计算 BERTScore 和 ROUGE-L。
4. 对高风险样本运行 NLI contradiction 检测。
5. 对 chunks 聚合后与 source_text 比较，判断分块是否造成语义丢失。
```

## 输出指标

- `semantic_preservation_score`
- `bertscore_f1`
- `rouge_l`
- `nli_contradiction_rate`
- `semantic_drift_rate`
- `low_preservation_samples`

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 缺少 before/after 文本字段 | `failed_precondition` |
| 数据集没有文本处理产物 | `not_applicable` |
