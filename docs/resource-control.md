# 资源控制

插入式评估默认跟随 pipeline 生命周期触发，因此执行策略必须保守。资源控制的目标是让评估结果可复现、可预算、不会反向拖垮数据生产链路。

## 默认采样

默认只对样本进行评估：

- 默认 `sample_size=1000`。
- 大数据集按比例或上限采样。
- 视频任务限制 `max_videos`、`max_segments_per_video`、`max_frames_per_segment`。
- QAE 任务限制 `max_triplets_per_video`。

## 分层采样

为了避免只抽到简单样本，采样时按以下维度分层：

- 模态类型：`image`、`video`、`text`、`multimodal`。
- 数据来源或 dataset split。
- 是否存在 `caption`、`samples`、`description_units`、`qae_triplets`。

## Embedding Cache

图像、文本、视频帧 embedding 可缓存：

```text
cache_key = model_name + file_path_or_text_hash + preprocessing_version
```

缓存对象包括：

- image embedding。
- text embedding。
- frame embedding。
- segment pooled embedding。
- concept extraction result。

```yaml
cache_dir: /path/to/ops_quality_cache
reuse_cache: true
```

## Batch 推理

所有模型推理默认 batch 化：

- CLIP 图像编码 batch。
- CLIP 文本编码 batch。
- BGE/Sentence-BERT 文本编码 batch。
- BLIP/ITM 小批量推理。
- VLM/LLM judge 限制并发和最大请求数。

## 分级评估

| 等级 | 内容 | 场景 |
| --- | --- | --- |
| `basic` | 字段可用性、轻量 embedding、采样统计 | 快速验收、CI |
| `standard` | CLIP、BGE、概念抽取、低分样本 | 日常质量评估 |
| `deep` | Cosmos-Embed、NLI、GroundingDINO、VLM/LLM judge | 发布前审计、疑难样本复核 |

插入式运行默认使用 `basic` 或轻量 `standard`；发布前审计再启用 `deep`。
