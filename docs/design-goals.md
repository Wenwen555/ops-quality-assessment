# 设计目标

本评估体系围绕功能指标组织，而不是按文本算子、图像算子、视频算子分别展开。它面向的是“产物是否可靠”，而不是“某个算子是否成功返回”。

## 目标

- 使用 `MultimodalDataset` 作为统一数据抽象，兼容 AscendDataForge 现有结构。
- 不注册为主数据处理 pipeline 的中间算子，不进入 `OperationRegistry` 作为处理节点。
- 通过 `PipelinePlugin` 在每个算子产物上触发评估。
- 按字段可用性自动判断哪些指标可以评估。
- 缺少字段时将指标标记为 `not_applicable` 或 `failed_precondition`，不阻断整体报告。
- 在插件中记录分阶段评估报告，方便人工复核和后续自动筛选。

## 能力分组

| 能力 | 覆盖问题 | 代表指标 |
| --- | --- | --- |
| 文本噪声与污染 | 重复、特殊字符、黑名单、模板化文本是否污染数据 | `text_noise_contamination` |
| 文本规范化有效性 | 日期、货币、空白、标点是否符合约定格式 | `text_normalization_validity` |
| 图文/帧文对齐 | 图像、视频帧与文本描述是否讲述同一语义 | `image_text_alignment` |
| 视频证据代表性 | 采样帧、片段、时间窗口是否互相支持，并发现重复模板化或证据断裂 | `video_image_alignment`, `coherence_score` |
| 语义保持 | 文本处理前后是否丢失或改变含义 | `text_semantic_preservation` |
| 视觉主体覆盖 | 图像或采样帧主体是否被 caption 或中间文本覆盖，且是否存在主体不匹配 | `chair_object_hallucination` |
| 视觉变换一致性 | crop、flip、Canny 等视觉变换是否保持样本语义和结构 | `visual_transform_consistency` |
| 视觉鲁棒性 | 图像算子处理前后是否保持视觉稳定，且不依赖下游任务 evaluator | `visual_robustness` |
| 问答证据可信度 | QAE triplet 是否能回溯到证据 | `qae_grounding_alignment` |

## 非目标

!!! note "不替代主 pipeline 的基础健康检查"
    AscendDataForge 已有 `DataQualityAssessor` 用于基础数据质量检查。本评估体系不替代它，而是补充语义层、跨模态层和证据层的质量观测。

第一版运行时代码优先实现插件式评估，优先消费已有 score/embedding，并在缺 embedding 时用配置的模型后端补算；CLIP、BLIP、Cosmos-Embed 或 VLM/LLM judge 可作为后续模型后端接入。
