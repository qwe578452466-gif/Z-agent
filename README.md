# Z-agent

面向 Z-document × AI Compiler 的专家 Skill 体系，覆盖需求与文档、任务编排、MLIR/NPU/RVV、裸机 Runtime 和验证审查。

当前正在 [逐项进行需求访谈](docs/requirements/README.md)。当前需求对象完成并确认后才进入下一项。首项 Z-deep-interview 是开发流程前、单线程、无联动的独立 Skill；专家 Agent 的需求作为后续对象另行明确。需求状态与技能实现状态分开记录。

当前交付是 **v0.4 目录与契约草案**：24 个 Skill 的规划登记、6 类角色的职责、可校验的数据结构以及契约示例。登记表使用 0.4 支持独立 Skill，其余专家任务契约保持 0.3。领域 Skill、调度器和宿主适配尚未实现。

按以下顺序阅读：

1. [目录与职责边界](docs/01-directory.md)：哪些内容可分发、哪些属于宿主、哪些属于任务状态。
2. [输入输出与协作契约](docs/02-contracts.md)：Skill、角色、任务、结果、证据、验收和恢复规则。
3. [专家 Subagent 执行规范](docs/03-subagent-execution.md)：固定 goal、boudaries、SOP(each run) 三个标题，区分任务动态内容与专家总结。
4. [技能规划登记](registry/skills.json) 与 [六类专家角色](agents/roles.json)。

[schemas/](schemas/) 使用 JSON Schema Draft 2020-12 描述结构；[templates/skill/](templates/skill/) 是编写起点；[examples/contracts/](examples/contracts/) 是未执行的示例数据，不是工程验证结果。

[专家执行模板](templates/subagent/execution.md) 从任务生成目标和边界，引用 [专家 SOP](agents/sops/README.md) 的固定版本；SOP 每次执行都要遵守并报告逐条结果。

首期范围为 P0 的 12 个 Skill，后续 P1 8 个、P2 4 个。可用状态以实际实现和验证证据为准。

当前访谈已暂停，保留 Z-brainstorming 第 5 轮待答问题。下次接续或迁移到其他项目，请先读 [跨项目交接文档](docs/HANDOFF.md)。
