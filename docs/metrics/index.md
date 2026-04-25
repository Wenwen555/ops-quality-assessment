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
| [`text_noise_contamination`](text-noise-contamination.md) | regex, n-gram stats, blocklist | n-gram repetition, blocklist hit rate |
| [`text_normalization_validity`](text-normalization-validity.md) | regex rules, pattern inventory, before-after diff | residual pattern rate, invalid value rate |
| [`image_text_alignment`](image-text-alignment.md) | CLIP | CLIPScore mean, low alignment rate |
| [`video_image_alignment`](video-image-alignment.md) | Cosmos-Embed1, OpenCV timestamp reader | segment-frame alignment, temporal coverage |
| [`chair_object_hallucination`](concept-coverage.md) | subject segmentation, visual recognition, BGE-M3 | subject mention/mismatch rate |
| [`text_semantic_preservation`](text-semantic-preservation.md) | BGE-M3 | preservation score, semantic drift rate |
| [`visual_transform_consistency`](visual-transform-consistency.md) | CLIP | transform success, semantic similarity |
| [`visual_robustness`](visual-robustness.md) | CLIP, SSIM | embedding drift, structural similarity |
| [`qae_grounding_alignment`](qae-grounding-alignment.md) | evidence ref resolver, BGE-M3 | answer support rate, evidence hit rate |
| [`coherence_score`](coherence-score.md) | BGE-M3 | coherence score, duplicate description rate |

## 模型与工具清单

| 模型/工具 | 用途 |
| --- | --- |
| regex / pattern inventory / before-after diff | 文本噪声检测、规范化残留检测和非法规范化值检测 |
| `openai/clip-vit-base-patch32` | 通用视觉/图文 embedding：图文对齐、视觉变换相似度、视觉 embedding drift |
| `nvidia/Cosmos-Embed1-224p` | 视频片段语义 embedding、视频-图对齐 |
| OpenCV frame extraction / timestamp reader | 视频采样帧读取和 temporal coverage 计算 |
| BGE-M3 | 通用文本 embedding：文本语义保持、QAE answer-evidence 支持、描述重复检测 |
| SSIM | 图像处理前后的结构相似度计算 |

## 可选扩展模型与工具

| 模型/工具 | 扩展用途 |
| --- | --- |
| BLIP / BLIP ITM | 扩展 `image_text_alignment` 的图文匹配概率。 |
| NLI 模型 | 扩展文本矛盾检测、事件链覆盖判断和 QAE evidence 支持判断。 |
| LPIPS / DISTS | 扩展 `visual_robustness` 的感知距离评估。 |
| GroundingDINO / DETR / YOLO | 扩展 object retention 和 detector-based CHAIR。 |
| VLM/LLM judge | 扩展复杂多模态 QAE grounding 判断。 |
| hard negative retrieval | 扩展图文对齐的正负样本区分度评估。 |
