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

- CLIP frame embedding pooling
- `nvidia/Cosmos-Embed1-224p`
- OpenCV frame extraction
- segment-level frame diversity scoring

## 实现逻辑

```text
1. 按 segment_id 聚合 samples。
2. 编码每个 sample frame 的图像 embedding。
3. 对同一 segment 的多帧 embedding 做 mean pooling / attention pooling。
4. 如果有 Cosmos 视频片段 embedding，则比较 segment video embedding 与 frame pooled embedding。
5. 如果无视频 embedding，则记录 failed_precondition。
6. 输出每个 segment 的 frame representativeness 和 segment-frame alignment score。
```

## 输出指标

- `segment_frame_alignment_mean`
- `frame_representativeness_mean`
- `frame_diversity_mean`
- `temporal_coverage_rate`

## 失败或不适用条件

| 条件 | 状态 |
| --- | --- |
| 无 `video_path` 或 `segments` | `failed_precondition` |
| segment 无采样帧 | segment-level failure |
| 数据集不包含视频模态 | `not_applicable` |
