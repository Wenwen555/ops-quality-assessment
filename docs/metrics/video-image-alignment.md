# `video_image_alignment`

## 用途

评估视频片段与采样图像之间的语义对齐程度，用于判断 `video_sampling` 抽取的帧是否能够代表对应视频片段。

## 输入字段

- `video_path`
- `segments`
- `samples`
- `samples[].frame_path`
- `samples[].segment_id`
- `samples[].timestamp_sec`

## 上游产物

- `video_partition` 产出的 `segments`。
- `video_sampling(save_sample_frames=True)` 产出的 `samples[].frame_path`。

## 推荐模型
- `nvidia/Cosmos-Embed1-224p` 或同类 video-image embedding 模型
- OpenCV frame extraction / timestamp reader

## 实现逻辑

```text
1. 按 segment_id 聚合 samples，并读取每个 segment 的 timestamp_range。
2. 使用同一个 video-image embedding 模型编码视频片段和对应采样帧。
3. 对同一 segment 内的采样帧 embedding 做 mean pooling。
4. 计算 segment video embedding 与 pooled frame embedding 的 cosine similarity，并聚合为 `segment_frame_alignment_mean`。
5. 根据采样帧 timestamp 是否覆盖对应 segment 的有效时间范围，计算 `temporal_coverage_rate`。
```

## 输出指标

- `segment_frame_alignment_mean`
- `temporal_coverage_rate`

## 可能扩展功能

| 功能 | 说明 |
| --- | --- |
| `frame_representativeness_mean` | 在缺少视频片段 embedding 时，用 segment 内帧间一致性近似衡量代表性。 |
| `frame_diversity_mean` | 衡量采样帧是否覆盖不同视觉状态，避免多帧几乎重复。 |
| attention pooling | 用 attention pooling 替代 mean pooling，提高多帧聚合质量。 |

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 无 `video_path` 或 `segments` | `failed_precondition` |
| segment 无采样帧 | `partial_failure` |
| 数据集不包含视频模态 | `not_applicable` |
