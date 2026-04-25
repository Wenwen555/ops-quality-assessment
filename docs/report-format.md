# 报告格式

评估链路默认先在 `OpsQualityPlugin` 中保存分阶段内存报告；需要落盘时可再导出 Markdown 报告和 JSON 报告。

## Markdown 报告

默认文件名：

```text
ops_quality_report.md
```

建议内容：

- 数据集摘要。
- 字段可用性。
- 已执行指标。
- 跳过指标及原因。
- 各指标分数表。
- 必测项结论。
- 模型和配置摘要。
- 资源消耗摘要。

## JSON 报告

默认文件名：

```text
ops_quality_report.json
```

建议结构：

```json
{
  "assessed_at": "2026-04-22T00:00:00",
  "stage": "after_operation",
  "operation_name": "image_captioning",
  "op_index": 2,
  "sample_size": 1000,
  "metrics": {
    "image_text_alignment": {
      "score": 0.82,
      "details": {
        "clip_score_mean": 0.31,
        "low_alignment_rate": 0.04
      }
    },
    "video_image_alignment": {
      "score": 0.68,
      "details": {
        "segment_frame_alignment_mean": 0.44,
        "missing_frame_rate": 0.02
      }
    }
  }
}
```


## 推荐汇总表

| 产物  | 用途 |
| --- | --- |
| `ops_quality_report.md` | 快速判断整体质量和高风险指标。 |
| `ops_quality_report.json`  | 触发质量门控、趋势追踪、看板展示。 |
