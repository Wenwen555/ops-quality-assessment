# `text_normalization_validity`

## 用途

评估文本规范化算子是否把目标模式稳定转换为预期格式。该指标聚焦 **validity / conformity**：规范化后的文本是否符合约定的日期、货币、空白和标点格式。

它不评价改写后的语义保持；语义层面的 drift 应由 `text_semantic_preservation` 评估。

## 输入字段

- `text`
- 可配置 `before_text_key`
- 可配置 `after_text_key`

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
1. 基于 pattern inventory 在 before_text 中定位所有应被规范化的目标片段
2. 在 after_text 中重新扫描旧格式 pattern，统计仍残留的未规范化片段，并计算 `residual_unstandardized_pattern_rate`
3. 对 after_text 中的规范化结果做合法性校验，例如日期是否为真实日期、货币数值和单位是否完整、空白和标点是否符合目标格式
4. 统计非法或明显错误的规范化结果，并计算 `invalid_normalized_value_rate`
```

## 输出指标

| 指标 | 含义 | 方向 |
| --- | --- | --- |
| `residual_unstandardized_pattern_rate` | 规范化后仍残留旧格式的比例 | 越低越好 |
| `invalid_normalized_value_rate` | 转换后不合法值比例 | 越低越好 |

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 缺少 before / after 字段 | `failed_precondition` |
| 样本中没有可规范化 pattern | `not_applicable` |
| pattern 规则未配置 | `failed_precondition` |
