# 目录与职责边界 v0.4

本文件将需求报告转为开发起点。目录和数据契约是草案；硬件规格、工具链版本、实际项目路径、性能阈值和宿主 API 尚未冻结。

## 目录

```text
Z-agent/
├── README.md
├── LICENSE                          # 本项目原创内容的许可证
├── docs/
│   ├── 01-directory.md               # 目录、职责、边界
│   ├── 02-contracts.md               # 协作与数据契约
│   ├── 03-subagent-execution.md      # 专家执行的三个固定标题与 SOP
│   └── requirements/
│       ├── README.md                # 逐项访谈索引；需求状态与实现状态分开
│       ├── skills/Z-deep-interview.md  # 当前 Skill 的问题、答案和未决项
│       ├── agents/design-documentation-expert.md
│       └── interviews/Z-deep-interview.json  # 清晰度、轮次与完成门记录
├── registry/
│   └── skills.json                  # 24 个技能；独立/工作流使用方式、候选角色、状态
├── agents/
│   ├── roles.json                   # 6 类角色；职责和允许输出
│   └── sops/                        # 专家维护；<role_id>/<version>.json
├── schemas/
│   ├── common.schema.json           # Artifact、证据、执行记录
│   ├── registry.schema.json
│   ├── roles.schema.json
│   ├── skill.schema.json
│   ├── task.schema.json
│   ├── result.schema.json
│   ├── expert-sop.schema.json
│   └── run-state.schema.json
├── templates/
│   ├── skill/
│   │   ├── SKILL.md                 # 可复用的编写模板
│   │   └── manifest.json            # 元数据模板，状态为 draft
│   ├── subagent/
│   │   ├── execution.md             # goal / boudaries / SOP(each run)
│   │   └── sop.json                 # 专家总结起点，尚未确认
│   └── requirements/
│       ├── skill.md                 # Skill 的完整需求访谈模板
│       └── agent.md                 # 角色与增量职责的需求访谈模板
├── examples/
│   └── contracts/
│       ├── task.json                # Softmax 任务示例
│       ├── result.json              # 被缺失前提阻塞的结果示例
│       ├── run-state.json           # 与上述任务一致的恢复状态示例
│       ├── checkpoint.json          # 未执行动作与恢复入口
│       ├── events.jsonl             # 与快照关联的示例事件
│       ├── expert-sop.json          # 未确认的示例 SOP
│       └── subagent-execution.md    # 三标题的已填充示例，未执行
├── skills/
│   └── README.md                    # 技能包组织规范；当前没有实现包
└── .z-agent/                        # 运行时生成，Git 忽略；当前不创建
    └── runs/<run_id>/
        ├── state.json               # 主 Agent 写入的状态快照
        ├── events.jsonl             # 追加事件记录
        ├── documents/               # 需求、设计、执行文档
        ├── tasks/<task_id>/
        │   ├── task.json            # 本次分派的不可变任务规格
        │   └── attempts/<attempt>/
        │       ├── execution.md     # 本次执行的目标、边界与锁定 SOP
        │       ├── result.json
        │       ├── checkpoint.json
        │       └── logs/
        ├── artifacts/               # IR、报告、测试结果等
        └── reviews/                 # 独立审查报告
```

未来每个已实现的技能使用如下包结构。`references/`、`examples/`、`tests/` 是包内资料；与工程项目的源码和测试分开。`scripts/` 仅在确有执行工具时添加。

```text
skills/<skill_id>/
├── SKILL.md                         # Agent 读取的专业方法
├── manifest.json                    # 注册和校验所需元数据
├── references/
│   └── README.md                    # 资料说明；来源元数据在 manifest.sources
├── examples/                        # 正例、反例、输入输出样例
├── tests/                           # 边界用例与工程案例定义
└── scripts/                         # 可选；有明确输入输出的辅助工具
```

第一步只建立规范和模板，不创建 24 个空技能包。注册表中所有技能的 `status` 为 `planned`，`path` 和 `version` 为 `null`，因此均不能据此宣称可执行。

## 分层与职责

| 层 | 负责内容 | 开发边界 |
|---|---|---|
| Skill | 触发条件、方法、输入输出、证据要求、故障处理 | 不创建执行权限或虚构目标硬件事实 |
| 专家角色 | 总结角色 SOP、选择适用技能、执行任务、输出结果与证据 | 遵守本次任务边界、锁定 SOP 和宿主授权 |
| 主 Agent | 依赖排序、分派、状态更新、失败处理、最终验收 | 不直接修改产品源码；可写任务文档和编排状态 |
| Independent Reviewer | 独立检查需求、实现、证据及风险 | 不审查自己的执行结果；不直接修复产品源码 |
| 宿主/适配层 | 工具授权、工作区、Agent 实例、进程、密钥和存储生命周期 | 本仓库目前只声明需求，不实现或假设这些能力 |

开发前的独立访谈不实例化专家角色，也不使用主 Agent 分派。六类专家是后续开发的角色配置，不执行需求访谈。

角色是配置，不是六个固定运行时。不同角色可以使用相同模型；实际执行和审查使用不同 Agent 实例及独立上下文。角色声明和 Schema 都不构成权限授予。

`registry/skills.json` 是技能阶段、使用方式和候选角色绑定的唯一登记源。Z-deep-interview 登记为 standalone，无角色绑定；其他条目保留待访谈的 agent_workflow 规划。`agents/roles.json` 定义职责和可写产物类型，不重复维护技能清单。

每个技能的 `manifest.sources` 是来源元数据的唯一登记源；`references/` 保存资料与说明，不另设手工维护的重复来源清单。

本项目后续新建或移植的 Skill 均采用 `Z-{skill}` 标识和包目录；登记、manifest、frontmatter 和任务引用一致。资料研究保存在 `docs/references/`，当前访谈轮次与模型清晰度保存在 `docs/requirements/interviews/`，分别区分来源事实、用户决定和评估。

专家 SOP 位于 `agents/sops/`，与单个 Skill 的专业方法分开维护。每次专家分派使用三个固定标题；目标与边界从 Task 动态生成，第三段引用专家总结的固定版本，详见 [执行规范](03-subagent-execution.md)。

## P0 的开发闭环

开发流程前，用户直接使用独立 Z-deep-interview，单线程完成需求访谈、JSON 记录和人类总结。访谈不联动专家或主 Agent，确认后的材料作为后续输入。

1. 文档专家基于已确认需求进行方案探索、设计与文档工作，记录约束、设计取舍和缺少的输入。
2. 执行文档描述目标、任务依赖、修改范围和验收条件；主 Agent 转换为任务契约。
3. 主 Agent 检查已实现技能、输入、宿主能力、依赖和专家 SOP，生成三标题执行说明，再分派给适用专家。
4. 专家严格遵守锁定 SOP，提交结果、产物、逐条遵守记录与证据；失败时记录阻塞原因、已完成工作和恢复入口。
5. 独立审查后，由主 Agent 按验收条件决定是否接收；未满足条件的任务继续执行或明确阻塞。
6. 中断后根据文件状态恢复，并重新确认实例、进程、工作区及凭证的有效性。

P0 的 Reviewer 可以使用 `Z-correctness-regression` 和本项目的验收契约。`Z-risk-first-code-review` 的专项实现属于 P1，不成为整个 P0 闭环的前置条件。

## 宿主能力与部署

后续适配层至少需要提供：工作区读取、授权范围内写入、命令执行、Agent 分派、能力查询，以及任务文件的持久化。进程管理、网络访问、Worktree 和硬件访问按任务需要协商。

任务调度时取“宿主实际授权 ∩ 角色职责 ∩ 任务范围”的交集。缺失能力返回阻塞结果，不能通过修改角色文件扩大权限。Shell 也不能用于绕过写入或网络边界。

云环境 onboarding 使用宿主已经提供的隔离 checkout；用户未明确要求时不创建额外 Worktree。`Z-worktree-isolation` 在 P1 实现，并依赖具体宿主能力和任务授权。

`.z-agent/` 的 Git 忽略只防止提交本地状态，不提供安全隔离或跨机器持久化。同一工作区中的恢复与宿主销毁后的恢复是不同能力；后者需要宿主提供快照、备份或持久存储。运行中的进程、连接和凭证都需要重新确认。

未来跨平台分发只打包技能定义、资料与所需辅助工具；通过适配层转换角色及工具声明。源格式保持统一，具体宿主 API 在实现适配层时确定。

## 来源与许可证

需求报告列出的开源项目是候选参考，不代表已经审阅、复制或确认许可证。引入时逐项记录作者、原始 URL、版本/提交、许可证和使用方式；复制或改编的材料保留原许可证及归属说明。

仓库根目录的 MIT 许可证适用于本项目原创内容，不能自动覆盖第三方代码、模板或技能材料。没有确认来源和授权的材料不得作为可分发的复制内容。
