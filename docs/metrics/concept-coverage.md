# `chair_object_hallucination`

## 用途

基于 CHAIR（Caption Hallucination Assessment with Image Relevance）衡量 caption 或多模态中间文本产物中的 **object hallucination**。

该指标回答的问题是：

> 文本中提到的 object，有多少其实不在图像或视觉证据中？

这里采用 Rohrbach et al., 2018 在 *Object Hallucination in Image Captioning* 中提出的 CHAIR 设计，把视觉相关性落实到 object mention 与 ground-truth visual object 的匹配。

!!! note "指标方向"
    CHAIR 是 hallucination rate，越低越好。若需要“文本提到的 object 有多少是真的存在”的正向分数，可以使用 `1 - CHAIRi` 作为 object mention precision。

## 输入字段

- `caption`
- `image_path`
- ground-truth object annotations，例如 `objects`、`object_labels` 或数据集标注。
- 可选：reference captions，用于补充 object synonym / human mention。
- 可选：`description_units[].description_text`、`text_signals[].text`，用于将 CHAIR 扩展到视频或中间描述产物。

## 上游产物

- `image_loading` 产出的 `image_path`。
- `image_captioning` 产出的 `caption`。
- 数据集原生 object annotation，例如 COCO objects。
- 可选视频描述产物，例如 `video_multigranularity_description`、`video_text_evidence`。

## 推荐模型与资源

- COCO object label set
- synonym list，用于把文本 object mention 映射到标准 object 类别。
- tokenizer + singularization / lemmatization。

## 实现逻辑

```text
1. 从 caption 或目标文本字段中抽取 object mentions。
2. 对 mentions 做标准化，包括 tokenization、singularization / lemmatization、synonym mapping 和 compound object disambiguation。
3. 从 ground-truth annotations 中得到 image objects。
4. 将文本 mentions 与 image objects 对齐。
5. 计算 object mention 级 hallucination rate，输出 `CHAIRi`。
6. 计算 sentence 级 hallucination rate，输出 `CHAIRs`。
```

## 输出指标

| 指标 | 定义 | 方向 |
| --- | --- | --- |
| `CHAIRi` | `|hallucinated objects| / |all objects mentioned|` | 越低越好 |
| `CHAIRs` | `|sentences with hallucinated object| / |all sentences|` | 越低越好 |

## 可能扩展功能

| 功能 | 说明 |
| --- | --- |
| `object_mention_precision` | 使用 `1 - CHAIRi` 表示 object mention 的正向精确率。 |
| detector-based CHAIR | 在缺少 ground-truth object annotations 时，用 DETR、YOLO 或 GroundingDINO 产物近似视觉 object set。 |
| reference caption expansion | 使用 reference captions 补充 synonym / human mention 映射。 |
| hallucination distribution | 统计 hallucinated object 的分布，用于数据集层面的错误类型分析。 |

## 与原始 CHAIR 的关系

原始 CHAIR 主要面向 image captioning，并限制在 MSCOCO segmentation challenge 的 80 类 objects 上。本文档建议优先保持这一语义：

- 若数据集有 COCO-style object annotations，直接按 CHAIR 原始设定计算。
- 若数据集没有 object annotations，可在扩展版本中使用 detector 产物作为近似 ground truth，但报告中必须标记为 detector-based CHAIR。
- 若扩展到视频描述或 QAE 证据文本，应按帧、片段或 evidence window 先定义视觉 object set，再计算 CHAIR。

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 缺少待评估文本字段 | `failed_precondition` |
| 缺少 ground-truth objects | `failed_precondition` |
| 文本中没有 object mention | `not_applicable` |

## 参考

- Rohrbach et al., 2018, [*Object Hallucination in Image Captioning*](https://aclanthology.org/D18-1437.pdf).
- 指标名：CHAIR, Caption Hallucination Assessment with Image Relevance.
- 核心变体：`CHAIRi` 和 `CHAIRs`。
