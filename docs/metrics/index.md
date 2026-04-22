# 指标总览

评估指标按能力组织，而不是按算子类型组织。每个指标都定义输入字段、上游产物、推荐模型、实现逻辑、输出指标和不适用条件。

## 指标矩阵

| 指标 | 必要输入字段 | 关联上游产物/算子 | 推荐模型/工具 | 输出指标 |
| --- | --- | --- | --- | --- |
| [`image_text_alignment`](image-text-alignment.md) | `image_path`, `caption`/文本字段 | `image_loading`, `image_captioning` | CLIP, BLIP ITM | CLIPScore, ITM score, hard negative margin |
| [`video_image_alignment`](video-image-alignment.md) | `video_path`, `segments`, `samples[].frame_path` | `video_partition`, `video_sampling` | CLIP, Cosmos-Embed1 | segment-frame alignment, frame representativeness |
| [`cross_modal_semantic_similarity`](cross-modal-semantic-similarity.md) | `image_path`, `caption`, `samples`, `description_units`, `text_signals` | `image_captioning`, `video_multigranularity_description`, `video_text_evidence` | CLIP, BLIP, BGE | modality pair similarity, low score rate |
| [`concept_coverage`](concept-coverage.md) | `caption`, `description_units`, `text_signals`, `samples[].frame_path` | `image_captioning`, `video_multigranularity_description`, `video_scaffold_construction` | KeyBERT, spaCy, GroundingDINO, YOLO | concept precision/recall/F1 |
| [`text_semantic_preservation`](text-semantic-preservation.md) | `before_text_key`, `after_text_key` | `text_normalization`, `spelling_correction`, `text_chunk_mapping` | BGE, Sentence-BERT, BERTScore, NLI | preservation score, contradiction rate |
| [`modality_gap`](modality-gap.md) | image/text/frame embeddings or raw fields | `image_captioning`, `video_sampling`, `video_text_evidence` | CLIP, BGE, MMD | positive-negative margin, embedding drift |
| [`qae_grounding_alignment`](qae-grounding-alignment.md) | `qae_triplets`, `evidence_refs`, `description_units`, `text_signals`, `temporal_structures`, `samples` | `video_qae_materialization`, `video_consistency_revision` | rules, BGE, NLI, VLM/LLM judge | answer support rate, evidence hit rate |
| [`coherence_score`](coherence-score.md) | `segments`, `description_units`, `temporal_structures`, `context_window_refs` | `video_partition`, `video_context_orchestration`, `video_temporal_structure` | BGE, temporal rules | coherence score, temporal error rate |

## 模型与工具清单

| 模型/工具 | 用途 | 默认级别 |
| --- | --- | --- |
| `openai/clip-vit-base-patch32` | 图文相似度、帧文本相似度、视频采样帧 embedding | `standard` |
| BLIP / BLIP ITM | 图文匹配、caption 质量辅助评估 | `standard` |
| `nvidia/Cosmos-Embed1-224p` | 视频片段语义 embedding、视频-图对齐 | `deep` |
| BGE-M3 / Sentence-BERT | 文本语义保持、文本证据相似度 | `standard` |
| BERTScore / ROUGE / BLEU | 文本生成和文本改写相似度 | `standard` |
| NLI 模型 | 文本前后矛盾检测、答案证据支持判断 | `deep` |
| KeyBERT / spaCy / jieba / THULAC | 文本概念抽取 | `standard` |
| GroundingDINO / DETR / YOLO | 图像和采样帧概念抽取 | `deep` |
| TrOCR | OCR 文本证据辅助检查 | `standard` |
| VLM/LLM judge | QAE grounding、复杂视频问答一致性判断 | `deep` |
