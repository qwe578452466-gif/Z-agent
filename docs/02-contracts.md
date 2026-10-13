# 输入输出与协作契约 v0.4

本文的“必须”是实现需要遵守的规则，不代表调度器或宿主适配已经实现。JSON Schema 校验结构；文件存在性、权限、依赖图、工程证据和独立性还需要运行时校验。

## 1. 公共约定

| 项目 | 约定 |
|---|---|
| 数据格式 | UTF-8 JSON；技能正文为 Markdown；事件流为 JSONL |
| 契约版本 | 登记表 0.4；Skill manifest、角色、专家 Task/Result 等保持 0.3。各文件显式声明版本，按对应 Schema 校验；结构变化升级版本，未知版本拒绝读取并要求显式迁移 |
| Skill 版本 | SemVer；不兼容契约升级 major，兼容能力增加升级 minor，文字修正升级 patch |
| 标识 | 本项目 Skill ID 固定为 `Z-{skill}`，例如 `Z-deep-interview`；角色、任务、运行、产物和证据 ID 仍使用小写字母、数字和连字符 |
| 时间 | ISO 8601，包含时区；统一保存 UTC，例如 `2026-10-12T10:00:00Z` |
| 路径 | 任务与运行文件路径相对宿主 workspace 根；包内路径相对 Skill 根；不接受绝对路径或 `..` |
| 未知信息 | 明确使用 `null`、`pending`、`hypothesis` 或阻塞原因；不填造假值 |
| 密钥 | 只记录变量名和存在状态；不得记录值、代理凭证、含凭证 URL 或完整进程环境 |

路径 Schema 只做词法检查。适配层必须解析真实路径、确认其在授权根目录内，并拒绝通过符号链接逃逸。任务给出的路径不能扩大宿主权限。

Schema 中的 `https://z-agent.invalid/schemas/<version>/…` 是本地标识，不是下载地址；验证器必须从本仓库加载并注册 Schema，不向该域名请求内容。

## 2. Skill 包与注册契约

### Skill 包

`SKILL.md` 是宿主当前会话读取的方法入口，frontmatter 至少包括 `name` 和 `description`。`name` 与 manifest 的 `id` 一致；description 表达能力与触发条件，不宣称尚未完成的验证。

后续本项目新建或移植的 Skill 使用大写 `Z-` 前缀，包路径为 `skills/Z-{skill}`，登记 ID、manifest.id、frontmatter.name、Task.skill_refs 和 Skill 依赖引用保持一致。上游名称记录在来源资料中；已有外部工具 grill-me/grilling 保留其安装来源和依赖标识。Agent role_id 不随 Skill 命名规则改名。

`common.skill_id` 与通用 `common.id` 分开校验，避免 Skill 命名变化影响任务、角色和证据标识。此次 ID 契约变化升级为 0.3；以前版本必须显式迁移。Schema 接受名称不代表宿主发现/调用兼容性已验证。

`manifest.json` 对应 [skill.schema.json](../schemas/skill.schema.json)，包括：

| 字段 | 契约 |
|---|---|
| `id`, `version`, `kind`, `status` | 稳定 ID、技能版本、documentation/workflow/domain 和实际成熟度 |
| `entrypoint` | 包内方法入口，当前固定为 `SKILL.md` |
| `activation.when`, `activation.unless` | 可判断的触发与排除条件；不满足前提时停止或转交 |
| `inputs`, `outputs` | 命名、类型、是否必需、含义及可选的包内 Schema 路径 |
| `requirements` | 所需宿主能力、工具及版本条件、环境变量名；只声明需求 |
| `depends_on` | 真正的技能包依赖，不把“推荐使用”当作硬依赖 |
| `sources` | 资料 URL、作者、版本/提交、许可证、使用方式及核实状态 |
| `validation` | 正例、反例、边界用例路径及领域工程案例记录 |

生命周期：`planned → draft → experimental → validated → retired`。`planned` 只存在于登记表；其他状态表示已有实现包。发现回归时可以把 `validated` 降为 `experimental`，记录原因和对应版本，不能继续按已验证能力调度。

`validated` 的门槛：输入输出、触发与排除条件完整；正例、反例、边界用例实际通过；来源可追溯；故障处理经过验证。domain 技能至少一个实际工程案例通过，证据对应当前技能版本和目标环境。结构校验通过不满足这些门槛。

### 登记与选择

[registry/skills.json](../registry/skills.json) 为每个 Skill 记录阶段、种类、使用方式、负责角色、允许使用的角色、状态、包路径和版本。0.4 增加 `usage_mode`，区分 `standalone` 与 `agent_workflow`。此前 0.3 的角色绑定登记已显式迁移；其他 Schema 沿用其声明版本。

- `planned` 的路径与版本必须是 `null`，不得分派执行。
- 已实现的路径、版本、种类和状态必须与 manifest 一致。
- `agent_workflow` 条目的 `owner_role` 为候选维护角色，必须包含在非空 `allowed_roles` 中；允许使用不等于工具授权。
- `standalone` 条目的 `owner_role` 为 `null`、`allowed_roles` 为空，直接由宿主当前会话使用，不通过专家 Task 分派。本次 Z-deep-interview 采用此方式。
- 产品任务只使用 `validated` 技能；`skill_validation` 模式可以使用 draft/experimental 包，但结果不能因此称为生产验收通过。`retired` 不用于新任务。
- 冻结任务时锁定具体技能版本。版本为 `null` 的计划任务可以保存，但不能进入 `ready`。

目前登记的阶段合计必须是 P0 12、P1 8、P2 4，共 24 个；实际成熟度独立于阶段。

### 来源与未确认事实

来源的 `usage` 分为 reference、adapted、copied。复制或改编前必须核实作者、许可证，以及版本或提交；来源未知时只作为候选参考，不能发布相关复制材料。

硬件 Bank 映射、DMA 顺序、指令语义、ABI 等约束要区分官方依据、实验观测和假设。一个模拟结果不能自动推广到真实芯片或其他版本；未知约束不能写入已冻结的事实基线。

### 独立访谈与开发流程

Z-deep-interview 在开发流程前直接使用，单线程、无 Agent 或其他 Skill 联动。用户是访谈参与者，Skill/Agent/工程是需求对象。访谈材料可以供后续开发读取，但该 Skill 不发起专家任务、自动调用主 Agent、分派 Subagent 或启动其他 Skill。

专家角色读取已确认需求开展后续工作，不执行需求访谈。独立 Skill 不使用下列专家 Task、SOP 锁定与 Result 交接门；其需求完成条件在对应需求文档中定义。

## 3. 专家角色契约

[roles.schema.json](../schemas/roles.schema.json) 描述角色配置。角色是职责声明，宿主负责实例化、授权和上下文边界。

| 角色 ID | 核心输入 | 输出与边界 |
|---|---|---|
| `main-orchestrator` | 已批准的要求、设计/执行文档、任务图、专家结果 | 任务、状态、分派、验收记录；不直接修改产品源码 |
| `design-documentation-expert` | 已确认需求、约束、参考资料 | 设计、执行文档、技能定义；不开展需求访谈 |
| `mlir-compiler-expert` | 固定版本的 IR、Pass Pipeline、源码、目标约束 | 编译器方案/改动、IR 诊断、回归证据 |
| `npu-rvv-expert` | 硬件规格、Kernel、访存与向量约束 | Bank/DMA/RVV 分析、Kernel 改动、性能与正确性证据 |
| `baremetal-runtime-expert` | ISA、工具链、链接布局、ABI、状态机 | 固件/Runtime 改动、ABI 文档、执行与回归证据 |
| `independent-reviewer` | 任务契约、实现产物、执行结果与证据 | 风险与符合性审查报告；不修复产品源码、不审查自己的执行结果 |

独立审查要求与实现者使用不同 `executor_id`、独立上下文和当前产物快照；允许使用同一底层模型。主 Agent 是最终验收者，Reviewer 的 approved 是验收前提，不是最终任务状态。

专家负责总结、核对并维护本角色的 SOP。首次执行、重试和恢复的执行说明都必须按 `goal`、`boudaries`、`SOP(each run)` 三个独立标题生成：前两项来自当前任务，SOP 来自专家维护的固定版本，不在任务中临时改写。格式、来源及逐条遵守规则见 [专家执行规范](03-subagent-execution.md)。

## 4. Task：分派前必须明确的内容

[task.schema.json](../schemas/task.schema.json) 定义不可变的任务规格：

| 字段 | 内容 |
|---|---|
| `task_id`, `run_id`, `mode` | 唯一身份；development / skill_validation / design |
| `goal`, `owner_role` | 可检查的目标和执行角色 |
| `sop_ref` | 专家 role_id、SOP 版本、文件路径及 SHA-256；必须与负责角色一致 |
| `skill_refs` | 具体技能及版本；`null` 版本只允许用于未就绪计划 |
| `depends_on` | 必须先被 accepted 的任务 ID |
| `inputs` | 产物路径、类型、基线 revision/digest；不只有一段含糊提示 |
| `outputs` | 预期产物、类型、路径和是否必需 |
| `scope` | 仓库路径、基线 revision、允许写入路径、只读路径、业务约束 |
| `required_capabilities` | 宿主需要实际提供的能力 |
| `acceptance_criteria` | 带 ID 的条件、验证方式及是否必需 |
| `failure_policy` | 尝试次数上限、重试策略、升级处理角色 |

至少有一个必需验收条件。不能用“完成开发”“效果良好”代替具体行为和检查；数值容差、目标 Shape、ABI 字段、工具链/目标版本或性能基线属于必需输入时必须明确。

任务进入 ready 前检查：依赖已验收、图无环、必需输入存在且身份匹配、技能版本与状态可用、角色允许使用技能、所需权限与工具可用、写入范围无冲突。另需核验专家 SOP 已 confirmed、作者与角色匹配、固定版本和 digest 有效，以及三标题执行说明完整。`design` 可以分析计划中的技能，但不能假装调用尚未实现的技能。

计划任务尚无 SOP 时，sop_ref 的版本、路径和 sha256 可以同时为 null；已选定 SOP 时三者必须同时完整。缺少已确认 SOP 的任务不能进入 ready。sop_ref 是 SOP 的唯一引用源，执行标题下的内容仅由其渲染。

冻结后的任务规格不原地改写。需求、基线或写入范围改变时创建新任务 ID，并记录其替代关系；原任务取消或保留结果供追溯。

输入/输出的工件版本与任务基线必须一致。任务实际授权是宿主权限、角色职责和任务 scope 的交集；不得用 Shell、技能说明或子 Agent 绕过限制。

## 5. Result、Artifact 与 Evidence

[result.schema.json](../schemas/result.schema.json) 定义一次尝试的结果：

- 身份：task/run/attempt、执行者 ID 和角色。
- `status`：completed / blocked / failed / cancelled；completed 表示执行者完成，不表示最终验收。
- `artifacts`：实际生成的产物；没有产物时为空，不能列出计划文件充当产物。
- `evidence`：与本次目标、命令、环境和产物关联的证据。
- `acceptance`：逐条引用条件 ID，报告 passed / failed / blocked / not_run，并关联证据或原因。
- `sop_checks`：按锁定 SOP 的规则顺序逐条记录遵守状态与证据；未遵守、未执行或被阻塞不能称为完成。
- `blockers`：原因、所需动作、是否有条件重试。
- `handoff`：恢复提示、检查点位置、剩余动作。

所有任务条件必须有且只有一条 acceptance 记录。passed/failed 必须引用证据；blocked/not_run 必须解释原因。completed 不允许遗留 blocker，且所有必需条件都要通过；可选条件可以未运行，但须显式记录。

completed 必须有 sop_checks；所有适用规则必须 followed 并关联证据。not_applicable 仅用于条件确实不成立的 conditional 规则，必须说明理由。every_run 规则不得跳过；blocked、violated、not_run 都不能进入 completed。独立审查核实实际遵守情况，结构校验不能代替执行证据。

公共类型见 [common.schema.json](../schemas/common.schema.json)：

| 类型 | 必需信息与语义 |
|---|---|
| Artifact | ID、kind、路径、媒体类型、revision、sha256；未知身份可为 null，但冻结输入和被验收产物必须可核验 |
| Evidence | ID、kind、claim、confidence、相关产物 ID、时间、执行记录或来源 |
| Execution | 原始命令、工作目录、时间、退出码、outcome、工具/目标信息、可用时的测试计数 |
| Source | URL、作者、版本/提交、许可证、usage、核实状态 |

confidence 为 verified/hypothesis/pending，表示依据的核实程度；它与执行结果的 passed/failed 是不同维度。真实观测到的测试失败也可以是 verified evidence。

执行记录必须保留底层退出状态。区分 passed、failed、expected_failure、skipped、not_run；套件计数另行记录 expected_failures、unexpected_passes、skipped 和 disabled。zero-test、全部跳过或未执行不能验证必需测试条件。

功能验证没有测试计数时，必须记录具体请求或操作、预期/实际行为和对应产物。模型自评、PID、开放端口、编译通过都不能独自证明应用功能或数值正确。

blocked 表示前提缺失；failed 表示实际执行或验收失败。未知硬件、环境缺陷与已确认的仓库缺陷分别标记，不能把仓库 Bug 全部归因于环境。

## 6. 验收、审查与状态机

任务执行状态只由主 Agent 写入 `state.json`；专家在自己的 attempt 目录提交结果与检查点，Reviewer 提交独立报告。state 中的状态与 Result.status 是两个层次。

| 当前状态 | 可进入状态 | 前提 |
|---|---|---|
| planned | ready / blocked / cancelled | 检查依赖、输入和能力；缺失前提进入 blocked |
| ready | running / blocked / cancelled | 生成 attempt，绑定实际执行者；派发失败不能宣称执行成功 |
| running | awaiting_review / blocked / failed / cancelled | completed 结果送审；其他结果保留原始原因 |
| awaiting_review | accepted / ready / blocked / failed / cancelled | approved 才能验收；有修改要求创建新 attempt；无法判断时阻塞 |
| blocked | ready / cancelled | 缺失前提得到补足且重新检查，才可恢复 |
| failed | ready / cancelled | 有新诊断或实质性修正，并且未超出重试上限 |
| accepted / cancelled | 无 | 终态；新需求或回归产生新任务，不覆盖历史 |

`accepted` 必须同时满足：最新结果 completed、全部必需条件 passed、适用 SOP 规则已遵守、证据对应当前产物及锁定 SOP、独立 Reviewer approved、需求和输入基线未变、主 Agent 已记录验收事件。

审查报告至少列出检查的任务/attempt、产物 SHA-256、需求符合性、正确性、测试证据、未确认事实、发现项及严重程度。decision 为 approved / changes_requested / inconclusive。不能只写“看起来没问题”。

审核后的产物发生改变，旧审核不能沿用；创建新的审查记录。缺少独立审查能力时，保留 awaiting_review/blocked，不能由实现者自评替代。

## 7. 长任务恢复与失败处理

[run-state.schema.json](../schemas/run-state.schema.json) 保存运行基线、任务图、attempt、最近结果、检查点、审查记录、决策和事件文件路径。状态文件是事实索引，设计和执行文档保留完整上下文。

事件使用 common Schema 的 `$defs/event`：schema_version、run_id、递增 sequence、event_id、时间、task_id、attempt、type、payload。checkpoint 使用 `$defs/checkpoint`：身份、已完成/剩余动作、以及副作用操作的 operation_key、观测状态和证据产物。

一致性约定：主 Agent 是状态与事件的唯一写入者；先追加并可靠写入事件，再通过同目录临时文件和原子替换更新状态。恢复时重放遗漏的完整事件；仅对崩溃留下的不完整尾记录做诊断性恢复。不能静默忽略中间损坏或顺序缺口。

按以下顺序恢复：

1. 检查版本、JSON 完整性、路径、run/task 身份和事件序列；未知版本或损坏先阻塞，不盲目覆盖。
2. 对照日志修复快照索引，核验文档/仓库基线、产物及锁定 SOP 的 digest；输入变化要求重规划，不能用新 SOP 偷换本次版本。
3. 对原 running 任务检查宿主实例与进程。没有可靠状态时标为阻塞，说明执行结果未知；不假设进程已停止或继续运行。
4. 按 checkpoint 核查副作用。operation_key 用于关联尝试，不保证外部工具恰好执行一次；结果未知的非幂等动作先确认，不能自动重复。
5. 重新确认工具、能力、凭证存在性及依赖后创建新 attempt；再次读取三标题执行说明并遵守 SOP 的每次启动检查，沿用已验证产物并保留历史日志。

仅暂时性故障可自动重试，而且必须有新诊断或状态变化。缺凭证、未知硬件、权限不满足和契约错误需要具体外部动作或任务修订；秘密值只通过宿主安全配置输入。

## 8. 契约校验与工程验收分开

结构校验包括 Schema 合法性、示例结构、24/12/8/4 数量、角色 ID、登记绑定、示例身份/依赖/条件引用、三标题结构、SOP 引用和逐条检查。

后续运行时校验还必须覆盖路径实际可读写、权限交集、真实产物和 digest、依赖 DAG、版本可用性、独立审查、证据新鲜度、测试实际执行以及故障恢复。JSON Schema 本身不能完成这些检查。

当前 [examples/contracts/](../examples/contracts/) 仅展示一个“计划技能尚未实现、硬件规格不足、专家 SOP 仍为 draft”的阻塞场景。时间、路径和 ID 均是示例；未运行任何 Softmax、Bank 或 ABI 工程验证，也不证明调度、恢复或实际 SOP 遵守已实现。
