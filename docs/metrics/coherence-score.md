# `coherence_score`

## 用途

评估视频分段、描述、事件链和上下文拼接是否连贯。

## 输入字段

- `segments`
- `description_units`
- `temporal_structures`
- `context_window_refs`
- `stitch_group_id`

## 上游产物

- `video_partition`
- `video_context_orchestration`
- `video_multigranularity_description`
- `video_temporal_structure`

## 推荐模型

- Sentence-BERT / BGE
- temporal order rule checks
- adjacent segment semantic shift
- duplicate description detection

## 实现逻辑

```text
1. 按 timestamp_range 排序 segments。
2. 检查时间是否重叠、倒序或存在异常 gap。
3. 计算相邻 segment description 的语义相似度。
4. 标记 abrupt semantic shift、near duplicate、blank context。
5. 对 event_chain 检查 summary 是否覆盖窗口内事件。
```

## 输出指标

- `coherence_score`
- `temporal_order_error_rate`
- `abrupt_shift_rate`
- `duplicate_description_rate`
- `context_missing_rate`
- `low_coherence_segments`

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 缺少 `segments` | `failed_precondition` |
| 缺少描述或事件链字段 | `warning` |
| 数据集不包含视频结构产物 | `not_applicable` |
