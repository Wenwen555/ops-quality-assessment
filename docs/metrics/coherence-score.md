# `coherence_score`

## 用途

评估视频描述产物中可审计的结构自洽问题。当前算子输出中的分段边界类型、时间结构和多粒度描述锚点，只能说明片段之间存在结构关联，不能可靠证明大语义变化具有合理过渡。因此第一版只检查下面一类问题：

- 语义变化很小，但内容重复、复制粘贴或跨片段模板化。

## 输入字段

- `segments`
- `description_units`
- `temporal_structures`

## 上游产物

- `video_partition`
- `video_context_orchestration`
- `video_multigranularity_description`
- `video_temporal_structure`

## 推荐模型

- BGE-M3

## 实现逻辑

```text
1. 按 timestamp_range 排序 segments。
2. 计算相邻或同组 description 的语义相似度和文本重复度。
3. 根据语义相似度和文本重复度阈值，计算 `duplicate_description_rate`。
4. 将重复率转换为正向结构连贯分数，输出 `coherence_score`。
```

## 输出指标

| 指标 | 含义 | 方向 |
| --- | --- | --- |
| `coherence_score` | 基于重复/模板化诊断聚合后的结构连贯分数 | 越高越好 |
| `duplicate_description_rate` | 相邻或同组片段出现近重复描述的比例 | 越低越好 |

## 可能扩展功能

| 功能 | 说明 |
| --- | --- |
| `template_reuse_rate` | 检测跨片段复用同一模板化表达的比例。 |
| `event_chain_coverage_rate` | 检查 summary / event chain 是否覆盖窗口内关键事件。 |
| `unexplained_event_rate` | 统计窗口内关键事件未被 summary / event chain 解释的比例。 |
| optional NLI | 对事件链覆盖关系做更强的语义蕴含判断。 |

## 已删除的评测功能

| 功能 | 删除原因 |
| --- | --- |
| `unsupported_semantic_shift_rate` | 当前算子不稳定输出 speaker turn、真实 scene id、独立 event boundary 等过渡证据。`boundary_type`、`temporal_structures` 和 `anchor_refs` 更适合作为结构来源，不足以可靠判定语义跳变是否合理。 |

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 缺少 `segments` | `failed_precondition` |
| 缺少 `description_units` | `failed_precondition` |
| 数据集不包含视频结构产物 | `not_applicable` |
