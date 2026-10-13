# Skill 与 Agent 需求访谈

当前对象：[Z-brainstorming](skills/Z-brainstorming.md)，面向用户没做过的任务，手动触发，由 Skill 帮助发现边界与需求问题；优先复用成熟上游方法。已核验 Superpowers 7.0.0。已明确由 Z-brainstorming 负责设计并调用 Z-design-document 的写作 SOP 生成合规文档。已明确专家参与设计与检查，并进行对抗式审查。当前已暂停，保留 Round 5 等待审查意见处理与通过标准，已完成 4 轮，模型估计清晰度 80.25%、歧义度 19.75%。[本项 JSON 记录](interviews/Z-brainstorming.json) 保留原始回答与重贴报告。

上一项 [Z-deep-interview](skills/Z-deep-interview.md) 已在 Round 17 通过用户原始答案“确定”完成需求确认；[完整需求总结](summaries/Z-deep-interview.md) 可作为后续设计依据，[历史 JSON](interviews/Z-deep-interview.json) 保留全部问答。确认的是需求，Skill 实现状态仍为 planned。

## 推进规则

1. 使用独立 Z-deep-interview 在开发前逐项明确需求。Skill、Agent 与工程都是可被访谈的需求对象；专家 Agent 不执行访谈。当前对象未完成，不开始下一项。
2. 每轮一个问题。不要求用户先说清卡点，发现问题也是 Skill 应帮助完成的工作。背景说明围绕实际问题展开；场景尚未明确时不预先给答案、推荐或方案对照。模糊问题先介绍必要背景与依赖，帮助拆解，在已有依据上讨论取舍。主动指出缺口，指导和建议补齐后回到原问题。
3. 每次问题以 JSON 保存，每次回答都记录，参考 OMC。稳定问题 ID 关联状态与原文，修订保留历史；助手解释与用户原话分开。推荐、沉默、跳过和超时不表示同意。
4. 全部访谈历史保留。当前需求与既有项目默认按最新访谈内容迭代，同一事项采用最新内容；未被修改的事项继续保留。已有设计草案和候选角色绑定不等于用户确认。
5. 当前需求对象的必要细节完成后，提交完整易读总结，获确认后再进入下一项。独立 Skill 无关联 Agent 完成门；其他对象的实际依赖按对应访谈明确。
6. 未查明事实保留待验证。详细实现、设计案例、边界细化与设计验收属于后续设计；总结不增加独立理解测试。
7. Agent 需求仍需完整访谈，并分别记录目标、边界、SOP、输入输出、失败与恢复。角色是被定义对象，不因此参与访谈执行。
8. 本阶段编写需求，不提前实现未完成访谈的 Skill/Agent；需求确认不等于实现或测试通过。
9. 保存累计轮次、清晰度、歧义和依据；达到数值阈值不自动完成。20 轮无法收尾则继续深度访谈，不因轮次强制停止。
10. 项目自建或移植 Skill 采用 Z-{skill} 命名；上游原始标识保留作来源。

按 P0 → P1 → P2 的既定顺序推进 Skill。其他条目的角色列仍是候选维护与使用关系，尚未冻结；不表示这些角色负责开展需求访谈。

## Skill 顺序与状态

| 顺序 | Skill | 阶段 | 候选维护角色 | 需求状态 |
|---|---|---|---|---|
| 1 | [Z-deep-interview](skills/Z-deep-interview.md) | P0 | 无；独立 Skill | 需求已确认；17 轮，DI-Q019“确定” |
| 2 | [Z-brainstorming](skills/Z-brainstorming.md) | P0 | `design-documentation-expert`，候选 | 已暂停；Round 5 待答；清晰度 80.25% |
| 3 | `Z-design-document` | P0 | `design-documentation-expert` | 待独立访谈；已知关联：文档写作 SOP，由 Z-brainstorming 调用，本身不参与设计（BS-Q003） |
| 4 | `Z-execution-document` | P0 | `design-documentation-expert` | 待访谈 |
| 5 | `Z-markdown-mermaid-writing` | P0 | `design-documentation-expert` | 待访谈 |
| 6 | `Z-subagent-orchestration` | P0 | `main-orchestrator` | 待访谈 |
| 7 | `Z-planning-files-recovery` | P0 | `main-orchestrator` | 待访谈 |
| 8 | `Z-mlir-ir-diagnosis` | P0 | `mlir-compiler-expert` | 待访谈 |
| 9 | `Z-aggregate-tiling` | P0 | `mlir-compiler-expert` | 待访谈 |
| 10 | `Z-npu-memory-bank` | P0 | `npu-rvv-expert` | 待访谈 |
| 11 | `Z-runtime-abi-eventloop` | P0 | `baremetal-runtime-expert` | 待访谈 |
| 12 | `Z-correctness-regression` | P0 | `independent-reviewer` | 待访谈 |
| 13 | `Z-worktree-isolation` | P1 | `main-orchestrator` | 待访谈 |
| 14 | `Z-mlir-dialect-interface` | P1 | `mlir-compiler-expert` | 待访谈 |
| 15 | `Z-mlir-pass-lowering` | P1 | `mlir-compiler-expert` | 待访谈 |
| 16 | `Z-bufferization-staging` | P1 | `mlir-compiler-expert` | 待访谈 |
| 17 | `Z-rvv-microkernel` | P1 | `npu-rvv-expert` | 待访谈 |
| 18 | `Z-baremetal-toolchain` | P1 | `baremetal-runtime-expert` | 待访谈 |
| 19 | `Z-performance-evaluation` | P1 | `npu-rvv-expert` | 待访谈 |
| 20 | `Z-risk-first-code-review` | P1 | `independent-reviewer` | 待访谈 |
| 21 | `Z-evidence-research` | P2 | `design-documentation-expert` | 待访谈 |
| 22 | `Z-skill-authoring-validation` | P2 | `design-documentation-expert` | 待访谈 |
| 23 | `Z-dma-async-schedule` | P2 | `npu-rvv-expert` | 待访谈 |
| 24 | `Z-eda-hardware-integration` | P2 | `npu-rvv-expert` | 待访谈 |

## Agent 需求覆盖

| Agent | 当前需求状态 | 说明 |
|---|---|---|
| [Design & Documentation Expert](agents/design-documentation-expert.md) | 待独立需求访谈 | 原 Z-deep-interview 执行绑定已撤销 |
| Main Orchestrator | 待访谈 | 读取已确认需求进行后续编排 |
| MLIR Compiler Expert | 待访谈 | 作为编译器角色需求对象 |
| NPU & RVV Expert | 待访谈 | 作为硬件与 Kernel 角色需求对象 |
| Bare-metal Runtime Expert | 待访谈 | 作为 Runtime 角色需求对象 |
| Independent Reviewer | 待访谈 | 作为独立审查角色需求对象 |

六类角色的实际职责和执行三标题要求仍需完成各自需求；它们不参与开发前的独立访谈执行。

## 完整性与资料

对 Skill 明确目标、触发/排除、输入输出、方法、能力边界、失败恢复、来源与验证方向；对 Agent 明确长期职责、动态 goal/boudaries、专家 SOP、协作与证据。只为已进入访谈的对象创建需求内容，不批量填造未确认要求。

- [Skill 需求模板](../../templates/requirements/skill.md)
- [Agent 需求模板](../../templates/requirements/agent.md)
- [专家执行三标题规范](../03-subagent-execution.md)：用于后续专家任务，不是独立访谈的执行模板
- [OMC 参考依据](../references/oh-my-codex-deep-interview.md)
- [已安装 grill-me 入口](../../.agents/skills/grill-me/SKILL.md) 与 [grilling 方法](../../.agents/skills/grilling/SKILL.md)：方法受本次用户明确的独立、单线程规则约束

参考方法优先依据原体系报告，逐项核验成熟上游再适配；当前已核验 [Superpowers brainstorming](../references/superpowers-brainstorming.md)，其他项目按对应 Skill 推进，不能宣称均已核验。

跨项目使用与暂停恢复请先读 [交接文档](../HANDOFF.md)。
