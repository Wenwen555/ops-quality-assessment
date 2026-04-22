# 设计目标

本评估体系围绕功能指标组织，而不是按文本算子、图像算子、视频算子分别展开。它面向的是“产物是否可靠”，而不是“某个算子是否成功返回”。

## 目标

- 使用 `MultimodalDataset` 作为统一数据抽象，兼容 AscendDataForge 现有结构。
- 不注册为主数据处理 pipeline 的中间算子，不进入 `OperationRegistry` 作为处理节点。
- 通过独立评估入口读取最终数据产物、快照或 manifest。
- 按字段可用性自动判断哪些指标可以评估。
- 缺少字段时将指标标记为 `not_applicable` 或 `failed_precondition`，不阻断整体报告。
- 输出 Markdown、JSON 和低质样本清单，方便人工复核和后续自动筛选。

## 能力分组

| 能力 | 覆盖问题 | 代表指标 |
| --- | --- | --- |
| 跨模态对齐 | 图像、视频帧、文本、答案是否讲述同一语义 | `image_text_alignment`, `cross_modal_semantic_similarity` |
| 视频证据代表性 | 采样帧、片段、时间窗口是否互相支持 | `video_image_alignment`, `coherence_score` |
| 语义保持 | 文本处理前后是否丢失或改变含义 | `text_semantic_preservation` |
| 概念完整性 | 关键对象、动作、场景、属性是否覆盖 | `concept_coverage` |
| 问答证据可信度 | QAE triplet 是否能回溯到证据 | `qae_grounding_alignment` |

## 非目标

!!! note "不替代主 pipeline 的基础健康检查"
    AscendDataForge 已有 `DataQualityAssessor` 用于基础数据质量检查。本评估体系不替代它，而是补充语义层、跨模态层和证据层的质量观测。

本阶段不实现运行时代码，不加载 CLIP、BLIP、Cosmos-Embed 或 VLM/LLM judge。文档只定义后续实现的边界、输入、输出和验收标准。
