# 指标总览

评估指标按能力组织，而不是按算子类型组织。每个指标都定义输入字段、上游产物、推荐模型、实现逻辑、输出指标和不适用条件。

## 功能分组

<div class="metric-taxonomy">
  <div class="metric-card metric-card--alignment">
    <p class="metric-card__count">6 metrics</p>
    <h3>一致性与跨模态对齐</h3>
    <p class="metric-card__label">Consistency / Cross-modal Alignment</p>
    <p class="metric-card__summary">检查文本、图像、视频帧、问答证据和事件链之间是否互相支持。</p>
    <p class="metric-card__links"><a href="image-text-alignment/">image_text_alignment</a><a href="concept-coverage/">concept-coverage / CHAIR</a><a href="video-image-alignment/">video_image_alignment</a><a href="text-semantic-preservation/">text_semantic_preservation</a><a href="qae-grounding-alignment/">qae_grounding_alignment</a><a href="coherence-score/">coherence_score</a></p>
  </div>
  <div class="metric-card metric-card--lightweight">
    <p class="metric-card__count">2 metrics</p>
    <h3>轻量质量检查</h3>
    <p class="metric-card__label">Lightweight Quality Assessment</p>
    <p class="metric-card__summary">不依赖重模型，优先用规则、统计和 before-after diff 发现文本噪声与规范化问题。</p>
    <p class="metric-card__links"><a href="text-noise-contamination/">text_noise_contamination</a><a href="text-normalization-validity/">text_normalization_validity</a></p>
  </div>
  <div class="metric-card metric-card--robustness">
    <p class="metric-card__count">1 metric</p>
    <h3>图像鲁棒性</h3>
    <p class="metric-card__label">Robustness</p>
    <p class="metric-card__summary">围绕图像算子处理前后的稳定性，检查视觉漂移、感知退化和变换产物异常。</p>
    <p class="metric-card__links"><a href="visual-robustness/">visual_robustness</a></p>
  </div>
</div>

## 指标矩阵

| 指标 | 推荐模型/工具 | 输出指标 |
| --- | --- | --- |
| [`text_noise_contamination`](text-noise-contamination.md) | regex, n-gram stats, tokenizer, blocklist | repetition rate, special char hit rate, blocklist hit rate |
| [`text_normalization_validity`](text-normalization-validity.md) | regex rules, pattern inventory, before-after diff | normalization success rate, residual pattern rate |
| [`image_text_alignment`](image-text-alignment.md) | CLIP, BLIP ITM | CLIPScore, ITM score, hard negative margin |
| [`video_image_alignment`](video-image-alignment.md) | CLIP, Cosmos-Embed1 | segment-frame alignment, frame representativeness |
| [`chair_object_hallucination`](concept-coverage.md) | COCO object labels, synonym mapping, optional detector | `CHAIRi`, `CHAIRs`, object mention precision |
| [`text_semantic_preservation`](text-semantic-preservation.md) | BGE, Sentence-BERT, BERTScore, NLI | preservation score, contradiction rate |
| [`visual_transform_consistency`](visual-transform-consistency.md) | CLIP/DINO, detector, SSIM/LPIPS, edge stats | transform success, object retention, edge density validity |
| [`visual_robustness`](visual-robustness.md) | CLIP/DINO, SSIM/LPIPS/DISTS, image statistics | embedding drift, perceptual distance, degradation rate |
| [`qae_grounding_alignment`](qae-grounding-alignment.md) | rules, BGE, NLI, VLM/LLM judge | answer support rate, evidence hit rate |
| [`coherence_score`](coherence-score.md) | BGE, duplicate detection, event coverage rules | duplicate rate, template reuse rate, event coverage |

## 模型与工具清单

| 模型/工具 | 用途 |
| --- | --- | 
| `openai/clip-vit-base-patch32` | 图文相似度、帧文本相似度、视频采样帧 embedding |
| BLIP / BLIP ITM | 图文匹配、caption 质量辅助评估 |
| `nvidia/Cosmos-Embed1-224p` | 视频片段语义 embedding、视频-图对齐 |
| BGE-M3 / Sentence-BERT | 文本语义保持、文本证据相似度 |
| BERTScore / ROUGE / BLEU | 文本生成和文本改写相似度 |
| NLI 模型 | 文本前后矛盾检测、答案证据支持判断 |
| regex / tokenizer / blocklist | 文本噪声和规范化有效性评估 |
| DINO / SSIM / LPIPS | 视觉变换一致性评估 |
| GroundingDINO / DETR / YOLO | object retention 和 CHAIR detector-based 扩展 |
| VLM/LLM judge | QAE grounding、复杂视频问答一致性判断 |
