# Ops Quality Assessment

<section class="whitepaper-hero">
  <p class="hero-kicker">Multimodal Operator Quality Assessment</p>
  <h1>面向多模态算子的插入式质量评估体系</h1>
  <p class="hero-copy">
    这份技术文档说明评估边界、Pipeline 插件执行链路、资源控制策略和核心指标定义。
  </p>
</section>

## 核心结论

!!! abstract "评估链路作为 PipelinePlugin 插入主数据处理 pipeline"
    多模态语义质量评估通过 `OpsQualityPlugin` 接入 pipeline 生命周期。
    插件在每个算子执行后评估当前产物，只记录报告，不改变后续数据流。

<div class="doc-grid cards" markdown>

-   **基础质量监控**

    `QualityPlugin` 在每个算子执行后调用 `DataQualityAssessor`，
    适合字段完整性、类型一致性、唯一性和文本长度等基础质量检查。

-   **算子产物评估**

    `OpsQualityPlugin` 在 pipeline 生命周期中调用 `OpsQualityAssessor`，
    用于图文对齐、QAE grounding、视频结构一致性、文本污染和 before/after 型质量观察。

-   **非侵入式记录**

    插件读取 `input_ds` / `output_ds` 快照并保存评估报告，不把审计字段写回后续 pipeline，
    因而不会污染数据产物流。

</div>

## 评估链路总览

```mermaid
flowchart TB
    %% 样式定义，提升图表的专业度与区分度
    classDef dataNode fill:#e3f2fd,stroke:#1e88e5,stroke-width:2px;
    classDef opNode fill:#fff8e1,stroke:#ffb300,stroke-width:2px;
    classDef pluginNode fill:#f3e5f5,stroke:#8e24aa,stroke-width:2px;
    classDef dbNode fill:#e8f5e9,stroke:#43a047,stroke-width:2px;

    %% 数据节点
    Input([Multimodal Dataset]):::dataNode
    Final([Final Dataset]):::dataNode

    %% 主干流水线
    subgraph Pipeline ["Multimodal Pipeline (数据处理主干)"]
        direction TB
        O1[Operation 1]:::opNode
        O2[Operation 2]:::opNode

        O1 ==>|主数据流| O2
    end

    %% 质量评估与汇报模块
    subgraph QA ["Quality Assurance (质量评估模块)"]
        direction TB
        Plugin{{OpsQualityPlugin}}:::pluginNode
        DB[(In-memory Reports)]:::dbNode

        Plugin -->|统一生成 After-operation Report| DB
    end

    %% 1. 数据流向 (使用粗箭头突出主链路)
    Input ==>|输入数据集| O1
    O2 ==>|输出最终数据| Final

    %% 2. 评估调用流向 (使用细箭头与虚线表示控制/旁路流)
    Input -.可拓展调用 (Initial Assessment).-> Plugin
    O1 -->|完成后调用| Plugin
    O2 -->|完成后调用| Plugin
```

## 阅读路径

1. [设计目标](design-goals.md)：确认为什么评估体系按能力而不是算子类型组织。
2. [Pipeline 边界](pipeline-boundary.md)：区分 `DataQualityAssessor` 与插入式 `OpsQualityPlugin`。
3. [实现路线与评估链路](implementation-roadmap.md) 和 [资源控制](resource-control.md)：理解执行模型。
4. [指标定义](metrics/index.md) 与 [报告格式](report-format.md)：用于实现评估器和验收报告。
