# `text_noise_contamination`

## 用途

评估文本样本中的噪声、污染和模板化问题。该指标面向数据清洗和训练前验收，不评价文本语义是否正确，而是检查文本是否包含会损害训练、检索、生成或人工复核质量的表层污染。

该指标对应 data quality 中的 validity / consistency / cleanliness 维度，比 “text hygiene” 更适合写入正式技术文档。

## 输入字段

- `text`
- 可配置 `text_key`
- 可选：`input_text`、`caption`、`description_units[].description_text`、`text_signals[].text`
- 可选：`blocklist`
- 可选：`special_chars`

## 上游产物

- `remove_repetitions_punctuation`
- `ngram_sample_filter`
- `special_char_filter`
- `blocklist_filter`
- 文本 loading / cleaning 后的原始文本字段

## 推荐工具

- 正则规则
- n-gram repetition 统计
- blocklist / allowlist
- tokenizer：whitespace、jieba、NLTK

## 实现逻辑

```text
1. 读取目标文本字段，空值单独统计。
2. 计算 n-gram repetition ratio，识别模板化或重复生成。
3. 检测重复词、重复标点、异常连续字符。
4. 检测 HTML tag、路径片段、代码片段、控制字符等特殊污染。
5. 统计 blocklist 命中次数和命中样本比例。
6. 聚合噪声分布，并输出 Top-K 高污染样本。
```

## 输出指标

| 指标 | 含义 | 方向 |
| --- | --- | --- |
| `empty_text_rate` | 空文本样本比例 | 越低越好 |
| `ngram_repetition_rate_mean` | 平均 n-gram 重复率 | 越低越好 |
| `high_repetition_sample_rate` | 超过重复阈值的样本比例 | 越低越好 |
| `repeat_punctuation_rate` | 重复标点污染比例 | 越低越好 |
| `special_char_hit_rate` | 特殊字符命中样本比例 | 越低越好 |
| `blocklist_hit_rate` | blocklist 命中样本比例 | 越低越好 |
| `top_contaminated_samples` | 高污染样本列表 | 用于复核 |

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 缺少目标文本字段 | `failed_precondition` |
| 所有文本均为空 | `failed_precondition` |
| 未配置 blocklist | blocklist 相关指标 `not_applicable` |

