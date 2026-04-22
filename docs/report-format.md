# 报告格式

评估链路默认输出三类产物：Markdown 报告、JSON 报告和低质样本清单。三者分别服务于人工阅读、自动消费和样本复核。

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
- 低质样本 Top-K。
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
  "dataset_uri": "/path/to/dataset",
  "sample_size": 1000,
  "metrics": {
    "image_text_alignment": {
      "status": "passed",
      "score": 0.82,
      "details": {
        "clip_score_mean": 0.31,
        "low_alignment_rate": 0.04
      }
    },
    "video_image_alignment": {
      "status": "warning",
      "score": 0.68,
      "details": {
        "segment_frame_alignment_mean": 0.44,
        "missing_frame_rate": 0.02
      }
    }
  },
  "issues": [],
  "recommendations": []
}
```

## 低质样本清单

默认文件名：

```text
low_quality_samples.jsonl
```

每行记录一个低质样本：

```json
{
  "sample_id": "sample_001",
  "metric": "image_text_alignment",
  "score": 0.18,
  "reason": "caption has low CLIP similarity with image",
  "fields": {
    "image_path": "/path/to/image.jpg",
    "caption": "..."
  }
}
```

## 推荐汇总表

| 产物 | 读者 | 用途 |
| --- | --- | --- |
| `ops_quality_report.md` | 研究员、工程师、评审者 | 快速判断整体质量和高风险指标。 |
| `ops_quality_report.json` | 自动化系统 | 触发质量门禁、趋势追踪、看板展示。 |
| `low_quality_samples.jsonl` | 数据审核人员 | 定位低分样本并做人工复核。 |
