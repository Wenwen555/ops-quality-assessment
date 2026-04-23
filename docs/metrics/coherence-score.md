# `coherence_score`

## 用途

评估视频描述产物中可审计的结构自洽问题。当前算子输出中的分段边界类型、时间结构和多粒度描述锚点，只能说明片段之间存在结构关联，不能可靠证明大语义变化具有合理过渡。因此本指标只考虑下面两类问题：

- 语义变化很小，但内容重复、复制粘贴或跨片段模板化。
- 事件链不可解释，即 summary / event chain 没有覆盖窗口内关键事件。

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

- Sentence-BERT / BGE
- duplicate description detection
- event coverage rules / optional NLI

## 实现逻辑

```text
1. 按 timestamp_range 排序 segments。
2. 计算相邻或同组 description 的语义相似度和文本重复度。
3. 对高相似片段检测 near duplicate、copy-paste 和模板化描述。
4. 对 event_chain 和 summary 检查是否覆盖窗口内关键事件。
5. 输出重复/模板化样本和事件链覆盖不足样本。
```

## 输出指标

| 指标 | 含义 | 方向 |
| --- | --- | --- |
| `coherence_score` | 重复/模板化与事件链覆盖诊断聚合后的结构连贯分数 | 越高越好 |
| `duplicate_description_rate` | 相邻或同组片段出现近重复描述的比例 | 越低越好 |
| `template_reuse_rate` | 跨片段复用同一模板化表达的比例 | 越低越好 |
| `event_chain_coverage_rate` | summary / event chain 覆盖窗口内关键事件的比例 | 越高越好 |
| `unexplained_event_rate` | 窗口内关键事件未被 summary / event chain 解释的比例 | 越低越好 |


## 已删除的评测功能

| 功能 | 删除原因 |
| --- | --- |
| `unsupported_semantic_shift_rate` | 当前算子不稳定输出 speaker turn、真实 scene id、人工 event boundary 等独立过渡证据。`boundary_type`、`temporal_structures` 和 `anchor_refs` 更适合作为结构来源，不足以可靠判定语义跳变是否合理。 |

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 缺少 `segments` | `failed_precondition` |
| 缺少描述或事件链字段 | `warning` |
| 数据集不包含视频结构产物 | `not_applicable` |
