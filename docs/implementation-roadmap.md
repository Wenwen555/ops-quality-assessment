# 实现路线

本网站只建设技术文档，不实现运行时代码。后续代码实现建议放在 AscendDataForge 的 `quality/ops-quality-assessor/` 模块下。

## 建议模块

```text
quality/ops-quality-assessor/
  MULTIMODAL_EVALUATION_REPORT.md
  assessor.py
  config.py
  metrics.py
  evaluators.py
  model_backends.py
  sampling.py
  report_writer.py
```

| 模块 | 职责 |
| --- | --- |
| `assessor.py` | 统一入口 `OpsQualityAssessor.evaluate(...)`。 |
| `config.py` | 配置模型 `OpsQualityAssessmentConfig`。 |
| `metrics.py` | 评估结果数据结构。 |
| `evaluators.py` | 各指标 evaluator。 |
| `model_backends.py` | CLIP、BLIP、BGE、VLM/LLM 等模型封装和缓存。 |
| `sampling.py` | 采样和分层采样逻辑。 |
| `report_writer.py` | Markdown、JSON、JSONL 输出。 |

## 建议接口

```python
class OpsQualityAssessor:
    def __init__(self, config: OpsQualityAssessmentConfig):
        self.config = config

    def evaluate(self, dataset_or_path) -> OpsQualityReport:
        dataset = self._load_dataset(dataset_or_path)
        schema = self._inspect_schema(dataset)
        plan = self._build_metric_plan(schema)
        sample = self._sample(dataset, plan)
        results = self._run_evaluators(sample, plan)
        return self._write_report(results)
```

## Evaluator Registry

内部 evaluator registry 独立于主 `OperationRegistry`，只在评估模块内部使用。

```python
EVALUATOR_REGISTRY = {
    "image_text_alignment": ImageTextAlignmentEvaluator,
    "video_image_alignment": VideoImageAlignmentEvaluator,
    "cross_modal_semantic_similarity": CrossModalSemanticSimilarityEvaluator,
    "concept_coverage": ConceptCoverageEvaluator,
    "text_semantic_preservation": TextSemanticPreservationEvaluator,
    "modality_gap": ModalityGapEvaluator,
    "qae_grounding_alignment": QaeGroundingAlignmentEvaluator,
    "coherence_score": CoherenceScoreEvaluator,
}
```

## 验收标准

### 文档验收

- 报告明确说明主 pipeline 不触发具体语义评估任务。
- 报告解释独立评估的资源控制策略，说明默认采样、缓存和分级评估。
- 报告按评估指标组织，而不是按文本、图像、视频算子章节组织。
- 报告包含每个指标的输入字段、推荐模型、关联上游产物和输出分数。

### 后续实现验收

- `OpsQualityAssessor.evaluate(dataset_or_path, config)` 可以接收 `MultimodalDataset` 或数据路径。
- 字段缺失时指标输出 `not_applicable` 或 `failed_precondition`，不导致整体崩溃。
- 必测项 `image_text_alignment` 和 `video_image_alignment` 可独立运行。
- 评估报告包含 Markdown、JSON 和低质样本 JSONL。
- 评估不依赖主数据处理 pipeline 的中间 hook。
