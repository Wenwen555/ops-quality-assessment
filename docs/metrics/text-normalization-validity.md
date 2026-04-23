# `text_normalization_validity`

## 用途

评估文本规范化算子是否把目标模式稳定转换为预期格式。该指标聚焦 **validity / conformity**：规范化后的文本是否符合约定的日期、货币、空白和标点格式。

它不评价改写后的语义保持；语义层面的 drift 应由 `text_semantic_preservation` 评估。

## 输入字段

- `text`
- 可配置 `before_text_key`
- 可配置 `after_text_key`
- 可选：operation tracking 中的 before / after snapshot

## 上游产物

- `text_normalization`
- `remove_repetitions_punctuation`
- `punctuation_normalization_mapping`
- `whitespace_normalization_mapping`

## 推荐工具

- 规则正则
- pattern inventory
- before / after diff
- sampled manual audit

## 实现逻辑

```text
1. 在 before_text 中匹配可规范化 pattern，例如 MM/DD/YYYY、Month DD, YYYY、$50。
2. 在 after_text 中检查是否转换为目标格式，例如 YYYY-MM-DD、50 USD。
3. 统计仍残留的未规范化 pattern。
4. 检测明显错误转换，例如非法日期、货币符号丢失、文本主体被误删。
5. 汇总转换成功率、残留率、异常转换样本。
```

## 输出指标

| 指标 | 含义 | 方向 |
| --- | --- | --- |
| `date_normalization_success_rate` | 日期 pattern 成功规范化比例 | 越高越好 |
| `currency_normalization_success_rate` | 货币 pattern 成功规范化比例 | 越高越好 |
| `residual_unstandardized_pattern_rate` | 规范化后仍残留旧格式的比例 | 越低越好 |
| `invalid_normalized_value_rate` | 转换后不合法值比例 | 越低越好 |
| `text_body_preservation_rate` | 非目标文本主体保留比例 | 越高越好 |
| `normalization_failure_samples` | 失败样本列表 | 用于复核 |

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 缺少 before / after 字段 | `failed_precondition` |
| 样本中没有可规范化 pattern | `not_applicable` |
| pattern 规则未配置 | `failed_precondition` |

