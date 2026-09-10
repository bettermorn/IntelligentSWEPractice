---
layout: cover
class: text-center
background: '#3a1e5f'
---


# 第一部分
## 系统分析

第 1-2 周

---
layout: two-cols
---
# 第1周：理论

- NASA 系统工程引擎 (NASA Systems Engineering Engine)
- 基于模型的系统工程 (MBSE) 核心思想
- 工业软件的本质、分类与技术壁垒
- 如何科学地定义工程问题与技术创新
- **实践指南**：协同仓库构建与问题定义

::right::

# 第1周：实践

- 分组
- 定义要解决的工程问题
- 在协作平台上建立小组项目仓库

📋 检查：确认定义的工程问题

---
layout: two-cols
---

# NASA 系统工程引擎
### NASA Systems Engineering Engine

NASA SP-2016-6105 标准定义了一个高度结构化的系统工程流程：

1. **系统设计流程 (System Design)**:
   - 利益相关者需求定义 (Stakeholder Expectations)
   - 技术需求定义 (Technical Requirements)
   - 逻辑分解 (Logical Decomposition)
   - 设计解决方案定义 (Design Solution)
2. **产品实现流程 (Product Realization)**:
   - 转换、集成、验证、确认与运行 (Transition, Integration, Verification, Validation, Operations)
3. **技术管理流程 (Technical Management)**:
   - 决策分析、技术规划、需求管理、风险管理等

::right::

```mermaid
flowchart TD
    A["利益相关者需求<br/>Stakeholder Needs"] --> B["技术需求<br/>Technical Requirements"]
    B --> C["逻辑分解<br/>Logical Decomposition"]
    C --> D["设计解决方案<br/>Design Solution"]
    D --> E["产品实现<br/>Product Realization"]
    E --> F["验证、确认与运行<br/>Verification, Validation & Operations"]
    F -. 反馈与迭代 .-> A

    classDef start fill:#f9d5f9,stroke:#8c3c8c,stroke-width:2px;
    classDef process fill:#e8f1ff,stroke:#2864c7,stroke-width:1px;
    classDef endNode fill:#d9e2ff,stroke:#405a9b,stroke-width:2px;

    class A start;
    class B,C,D process;
    class E,F endNode;
```



核心思想：
系统工程不是单一线性过程，而是递归 (Recursive) 和 迭代 (Iterative) 的过程。每一次物理分解都伴随着需求的下发与验证。

---

# 基于模型的系统工程 (MBSE)
### Model-Based Systems Engineering (MBSE)

传统的系统工程基于**文档 (Document-Centric)**，而现代系统工程转向**基于模型 (Model-Centric)**。

```mermaid{scale: 0.5}
flowchart LR
    subgraph D[文档中心的系统工程
Document-Centric Systems Engineering]
        Doc1[PDF / Word 规格说明]
        Doc2[Excel 测试用例]
        Doc3[CAD / Simulink 文件]

        Doc1 <-->|人工同步| Doc2
        Doc2 <-->|人工同步| Doc3
    end

    subgraph M[基于模型的系统工程
MBSE]
        Model((中央系统模型
SysML / SysML v2))
        Model --> View1[结构视图]
        Model --> View2[行为视图]
        Model --> View3[需求追踪视图]
        Model --> View4[参数与约束视图]
    end

    classDef document fill:#fff1f1,stroke:#cc5555,stroke-width:1px;
    classDef model fill:#eef5ff,stroke:#3377cc,stroke-width:2px;
    class Doc1,Doc2,Doc3 document;
    class Model,View1,View2,View3,View4 model;
```

* **SysML (System Modeling Language)**：MBSE 的事实标准，涵盖四根支柱：**结构 (Structure)**、**行为 (Behavior)**、**需求 (Requirements)** 和 **参数 (Parametrics)**。
* **优势**：消除歧义、保持设计一致性、支持早期仿真与验证。

---
layout: two-cols
---

# 什么是工业软件？
工业软件是工业知识的**数字化、模型化与软件化**载体。

* **分类**：
  * **CAD** (计算机辅助设计)：几何建模内核、拓扑关系。
  * **CAE** (计算机辅助工程)：偏微分方程求解、网格剖分。
  * **CAM** (计算机辅助制造)：数控轨迹规划。
  * **MES/PLM**：流程与生命周期管理。

* **技术壁垒**：
  * 数值计算的精度与稳定性（如：Double precision 溢出控制）。
  * 实时性与高并发事务处理。
  * 领域知识（物理、材料、力学）的深度融合。

::right::


```cpp
#include 

struct HalfEdge {
    int origin = -1;
    int next = -1;
    int twin = -1;
    int face = -1;
};

// 判断网格是否为封闭流形结构
bool isManifold(const std::vector& edges) {
    // 工业 CAD 内核通常还需要进行更复杂的几何与拓扑校验。
    // 这里仅检查每条半边是否存在对应的孪生半边。
    for (const auto& edge : edges) {
        if (edge.twin < 0) {
            return false; // 存在开边界
        }
    }

    return true;
}
```
---

# 科学地定义工程问题：5W1H 与 痛点映射
### How to Define an Engineering Problem

研究生阶段的创新应避免“造轮子”，而应致力于“解决真实问题”。

| 维度 (5W1H) | 核心追问 | 本质分析 |
|---|---|---|
| **What (什么问题)** | 这个工程瓶颈的核心现象是什么？ | 识别物理现象、数据瓶颈或算力边界。 |
| **Why (为什么解决)** | 现有的开源软件或商业软件为什么做不好？ | 找到现有方案的技术限制（如高复杂度、高延迟）。 |
| **Who (谁受影响)** | 谁是这个软件系统的最终用户？ | 定义用户画像 (User Persona) 和操作环境。 |
| **Where (在哪里发生)** | 该问题发生在生命周期的哪个阶段？ | 确定是运行期、设计期还是部署期。 |
| **When (何时发生)** | 在什么边界条件/极值场景下会触发？ | 定义极限输入、高并发或边缘设备限制。 |
| **How (如何度量)** | 如何定量评估“问题被成功解决”？ | **关键指标 (KPI)**：吞吐量提高30%，内存降低50%等。 |

---

# 技术的创新路径：颠覆性 vs 渐进式
### Research and Engineering Innovation Paths

```mermaid{scale: 0.5}
flowchart LR
    A["理论创新<br/>Theoretical / Algorithmic"] --> A1["新的数学模型<br/>算法或物理引擎"]
    A1 --> A2["示例：算子分裂法<br/>将复杂度从 O(N²) 降至 O(N log N)"]
    A1 --> A3["示例：强化学习<br/>实现工业控制自适应调节"]

    B["架构与工程创新<br/>Architectural / Engineering"] --> B1["重构系统拓扑结构"]
    B1 --> B2["提升吞吐量、容错能力<br/>与系统可扩展性"]
    B1 --> B3["示例：单体 CAD<br/>重构为 WebGPU 云原生架构"]
    B1 --> B4["示例：C++ + WebAssembly<br/>实现浏览器端百万级网格渲染"]

    classDef theory fill:#e8f1ff,stroke:#2864c7,stroke-width:2px;
    classDef engineering fill:#fff1df,stroke:#d47b00,stroke-width:2px;
    class A,A1,A2,A3 theory;
    class B,B1,B2,B3,B4 engineering;
```


1. 理论创新 (Theoretical / Algorithmic)

引入新的数学模型、算法或物理引擎。

例如：在流体仿真中引入算子分裂法，将时间复杂度从 O(N^2) 降至 O(N log N)。

  
在工业控制中引入强化学习实现自适应调节。


2. 架构/工程创新 (Architectural / Engineering)


重构系统拓扑结构，提升吞吐、容错或可扩展性。

  
例如：将单体桌面版 CAD 重构为基于 WebGPU 的云原生协同 CAD 架构。
  
利用 C++ 与 WebAssembly 混合编译，实现浏览器端百万级网格渲染。

💡 给研究生的建议： 硕士阶段更推荐“场景驱动的工程架构创新”或“先进算法在垂直工业领域的应用创新”，既有学术发表度，又有工程落地性。

---
# 第1周：实践

- 分组
- 定义要解决的工程问题
- 在协作平台上建立小组项目仓库


📋 检查：确认学生定义的工程问题

---

# 第 1 周实践：定义你的工程项目与仓库构建
### Practice: Repository Setup & Problem Definition

各小组需要在协作平台（GitHub / GitLab）上建立项目，并提交规范的 `README.md`。

```bash
# 1. 初始化项目仓库结构
mkdir smart-industrial-app && cd smart-industrial-app
git init

# 2. 规范的分支管理策略
git checkout -b main      # 生产分支
git checkout -b develop   # 开发主分支

# 3. 规范的项目目录结构
mkdir -p docs/{architecture,requirements} \
         src/{backend,frontend,core_engine} \
         tests/{unit,integration} \
         .github/workflows
```

* **任务要求**：在 `docs/requirements/problem_definition.md` 中编写 5W1H 报告。
* **检查点**：第一周结束前，各组向助教提交仓库链接，通过 GitHub Issues 获得第一轮反馈。

---
layout: two-cols
---

# 第2周：理论

- 软件功能的科学定义：功能性与非功能性需求
- 面向对象分析与设计 (OOAD) 核心：从领域模型到高内聚低耦合
- 敏捷方法论在学术/工业研发中的应用 (Scrum, Kanban)
- **实践指南**：编写高质量的需求规范书与 UML 设计

::right::

# 第2周：实践

- 定义要解决工程问题的软件作品功能


📋 检查：确认软件作品的功能

---
layout: default
---
# 软件功能定义：从愿景到系统需求
### Software Functional Definition

需求分析是软件工程中最容易导致失败的环节。我们必须将含糊的用户期望转化为精确的系统需求。

```mermaid
graph TD
    UserVision[用户愿景: 想要一个快速的三维查看器] -->|精细化分析| FunctionalReq[功能需求: 支持 STEP 格式解析与 60FPS 帧率渲染]
    UserVision -->|约束性分析| NonFunctionalReq[非功能需求: 运行内存限制在 512MB 内]
```

* **功能需求 (FRs)**：系统**必须做什么**（输入、处理、输出）。
* **非功能需求 (NFRs)**：系统必须**如何表现**（URPS：可用性 Usability, 可靠性 Reliability, 性能 Performance, 支持性 Supportability）。


坏的需求样例： "系统界面要好看，速度要快。"

好的需求样例 (可测量的)： "系统在加载 100MB 以上的 CAD 模型时，首屏渲染时间（LCP）需小于 3.0s，且 CPU 利用率不高于 60%。"


---
layout: two-cols
---

# 面向对象分析与设计 (OOAD)
### Object-Oriented Analysis & Design

OOAD 的核心在于**控制复杂度**。

1. **OOA (分析)**：在问题域中寻找对象，构建**领域模型 (Domain Model)**。
2. **OOD (设计)**：将分析模型转化为设计类，应用**设计原则 (SOLID)** 和**设计模式**。

**关键原则：SOLID**
* **S**ingle Responsibility (单一职责)
* **O**pen/Closed (开闭原则)
* **L**iskov Substitution (里氏替换)
* **I**nterface Segregation (接口隔离)
* **D**ependency Inversion (依赖倒置)

::right::



```mermaid
classDiagram
    class CADDocument {
        -String documentId
        -List~Geometry~ geometries
        +load(path: String) void
        +render(canvas: Canvas) void
    }

    class Geometry {
        <>
        -Color color
        +draw() void
    }

    class Circle {
        +draw() void
    }

    class Polygon {
        +draw() void
    }

    class StepParser {
        +parse(file: File) List~Geometry~
    }

    CADDocument o-- "1..*" Geometry : contains
    CADDocument ..> StepParser : uses
    Geometry <|-- Circle
    Geometry <|-- Polygon
```


设计说明：`CADDocument` 与 `Geometry` 之间是聚合关系。渲染引擎只依赖抽象的 `Geometry` 类型，新增几何类型时不需要修改 `CADDocument`，符合开闭原则（OCP）。



---

# 敏捷开发方法 (Agile Methodology)
### 针对科学研究与不确定性项目的轻量级管理

研究生的项目往往面临“高学术不确定性”，传统的瀑布模型无法适应，推荐使用 **Scrum / Kanban**。

```mermaid
gantt
    title 一个典型的两周 Sprint（冲刺）流程
    dateFormat YYYY-MM-DD
    axisFormat %m/%d

    section 敏捷活动
    Sprint 计划会 :milestone, plan, 2026-10-08, 0d
    每日站会 :daily, 2026-10-09, 10d
    Sprint 评审会 Demo :milestone, review, 2026-10-20, 0d
    Sprint 回顾会 :milestone, retrospective, 2026-10-21, 0d

    section 研发任务
    骨架代码搭建 :skeleton, 2026-10-09, 4d
    核心算法实现 :algorithm, 2026-10-13, 5d
    测试与文档完善 :testing, 2026-10-18, 3d
```

* **Product Backlog**：所有想做的功能池。
* **Sprint Backlog**：本周期（通常2周）承诺完成的任务。
* **定义完成 (Definition of Done, DoD)**：例如“代码通过单元测试，分支合并入 `develop` 且文档已更新”才算完成。

--
# 第 2 周实践：软件功能规范书撰写
### Practice: Functional Specification & UML Domain Modeling
本周各组必须在协作仓库中提交 `docs/requirements/functional_spec.md`。

```markdown
# 软件系统功能规格说明书 (示例模板)

## 1. 系统角色 (Actor)
* **工业分析师**：执行数据导入、算法配置与结果可视化。
* **系统管理员**：执行用户授权与配置审计。

## 2. 功能树 (Feature Tree)
* 模型导入模块
  * FR-1.1: 支持 STEP 物理数据包解析
  * FR-1.2: 异常文件鲁棒性容错与日志上报
* 求解器模块
  * FR-2.1: 并行化有限元方程组求解 (支持 OpenMP)

## 3. 领域模型 (Domain Model UML)
[在此处插入 Mermaid 类图]
```

* **汇报要求**：第二周课上，每组用 3 分钟展示其领域类图与功能分解，教师确认后方能进入系统原型设计。
