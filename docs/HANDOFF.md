# Z-agent 跨项目交接文档

交接日期：2026-10-13。仓库：<https://github.com/qwe578452466-gif/Z-agent>。本次成果上传至 `work` 分支。

## 当前停在哪里

当前在逐项完成 Skill/Agent 的需求访谈，尚未进入自建 Skill 实现阶段。用户要求暂停并上传成果；**Z-brainstorming 的第 5 轮问题保持待答**。暂停不算回答，不代表确认需求，不进入下一个 Skill。

| 对象 | 已完成 | 下一步 |
|---|---|---|
| 目录与契约 | v0.4 草案；24 个 Skill 登记、6 类专家角色、JSON Schema、模板和示例 | 后续按已确认需求迭代；角色绑定仍是候选 |
| Z-deep-interview | 17 轮回答，完整需求总结已由用户“确定”确认 | 可作为后续设计依据；尚未实现 |
| Z-brainstorming | 已回答 4 轮；第 5 轮问题已落盘 | 从 BS-Q005 继续；清晰度 80.25%、歧义度 19.75% 为模型估计 |
| 其余 22 个 Skill | 规划登记 | 当前项完成并确认后逐项访谈 |
| 6 类专家 Agent | 角色与执行契约草案 | 逐个完成详细需求访谈；目前没有已冻结的专家 SOP |
| 上游依赖 | 已安装 grill-me 与 grilling，记录来源并保留 MIT 许可证 | 使用时遵循用户最新访谈规则 |

登记表中的自建 Skill 全部为 `planned`。示例中的 Softmax、Golden、SOP 和任务结果是契约说明，**不是实际工程验证证据**。宿主调度、持久化、权限、隔离和生命周期仍由宿主提供。

## 建议阅读顺序

1. 本交接文档和 [需求总索引](requirements/README.md)。
2. [Z-deep-interview 已确认完整总结](requirements/summaries/Z-deep-interview.md)、[需求规格](requirements/skills/Z-deep-interview.md) 和 [历史 JSON](requirements/interviews/Z-deep-interview.json)。
3. [Z-brainstorming 当前需求](requirements/skills/Z-brainstorming.md) 和 [问答/状态 JSON](requirements/interviews/Z-brainstorming.json)。JSON 是恢复问题、原始回答和评分的依据。
4. [目录说明](01-directory.md)、[契约说明](02-contracts.md)、[专家三标题规范](03-subagent-execution.md)。
5. [Skill 登记表](../registry/skills.json)、[角色草案](../agents/roles.json)、[Schema](../schemas/)、[模板](../templates/) 和 [示例](../examples/contracts/)。

登记表版本为 0.4，其余专家任务契约为 0.3；不是全部文件都采用同一版本。访谈 JSON 的 `record_kind` 是工作记录，不应直接按专家 Task/Result Schema 解释。

## 必须保留的决定

- 自建或移植后的 Skill 命名为 `Z-{skill}`。已安装上游 Skill 保留原名作为依赖。
- 开发前逐项、单线程访谈；当前对象未完成需求并确认总结，不进入下一项。专家 Agent 不执行这种需求访谈。
- 每个问题和每个回答直接保存 JSON。不能直接保存时终止访谈；不使用仅展示 JSON、手工导出或专家代写作为替代。
- 保留全部原始问答和历史评估。同一事项采用最新明确回答；未修改的其他决定继续有效。重贴原规划报告用于指明来源优先级，不自动撤销后续具体决定。
- 每轮一个开放问题。先解释必要背景、依赖与取舍，帮助拆解；不预先塞答案和推荐，不要求用户先准确说清全部卡点。
- Deep 的 20 轮是回顾节点；仍有关键缺口时继续深度访谈。达到数值阈值不自动收尾。
- 总结面向人类理解，按“示例 → 背景 → 双层总结”组织。需求阶段不额外强加理解测试，详细设计案例与边界测试后续处理。
- 专家执行固定使用 `## goal`、`## boudaries`、`## SOP(each run)`。`boudaries` 拼写按用户要求保留；前两项按任务产生，SOP 由专家总结并版本化。
- 优先依据原体系建设报告研究成熟实现，分别登记作者、许可证与固定版本。未知硬件行为标记待验证。

Z-deep-interview 是开发前直接使用的独立 Skill，不联动 Agent 或其他 Skill；这不妨碍用户另行要求 **Z-brainstorming 在未来设计流程中使用专家与文档 Skill**。

## Z-brainstorming 已明确的运行需求

它面向用户没有做过的任务，由用户手动触发，帮助发现需求漏洞、边界和阻碍，探索实际可行方案、带来思路并完成设计。

Z-brainstorming 负责设计。完成设计时调用 Z-design-document，遵循其文档写作 SOP 生成合规、清楚、可读的架构、接口与技术设计文档。Z-design-document 本身不参与设计，具体写作规范留到该 Skill 的独立访谈。

设计与检查需有专门专家 Agent 参与，通过对抗式审查检验合理性与可读性。具体角色绑定、设计与检查的独立性、意见处理和返修、审查通过标准、最终用户确认、输入门槛、工具与失败恢复仍待访谈。没有默认采用上游的自动提交、直接构建、浏览器原型或全部后续调用。

## 保留的当前问题：BS-Q005

以下是已保存的问题原文，恢复时继续这一题，不增加新轮次：

> Z-brainstorming｜Round 5｜Target: success｜模型估计清晰度 80.25%，歧义度 19.75%。
>
> 已明确：设计与检查需要专门专家 Agent 参与，以对抗式审查检验合理性和可读性。
>
> Superpowers 的 Builder Check 会找出影响后续计划的重要缺口，并说明为什么它们重要；你提出的对抗式审查还要主动检验设计是否站得住。审查可能发现缺少依据、设计与需求冲突，或文档难以理解。修改后需要确认问题已解决；若专家仍有分歧，需要有收敛规则。
>
> 你希望这些审查意见怎样被处理，才能判定这轮设计通过审查？

当前 JSON 保留 5 个问题、4 个回答，唯一待答问题是 BS-Q005。恢复后先记录其回答，再更新评估与下一题；不重新问已回答内容，不把本次暂停指令记成这道题的答案。

## 在其他项目中使用

保留一份独立参考仓库最便于继续维护：

```bash
git clone --branch work https://github.com/qwe578452466-gif/Z-agent.git Z-agent-reference
```

也可解压本次交付的 `Z-agent-handoff.tar.gz`。压缩包只包含此提交的仓库文件，不包含 Git 历史、临时研究 checkout 或云环境凭据。将内容先放入目标项目的独立参考目录，例如 `docs/z-agent-reference/`，保留原有相对目录层级和全部 JSON。

这些资料可用于继续需求访谈、编写后续设计或建立同类体系。自建 Skill 尚无可直接运行的正式 SKILL.md；不要把需求文档当成已实现技能安装。若只使用现有 grill-me/grilling，可选择性合并 `.agents/skills/` 中对应目录，同时保留许可证和来源；先检查目标宿主的发现路径与同名 Skill，避免覆盖项目已有内容。

`examples/contracts/` 的路径包含示例 checkout 名称 `Z-agent/`，是契约示例。复制到其他项目时按目标工作区调整示例引用并重新核验，不能把示例绝对当作目标项目配置。临时研究目录 `/tmp/z-agent-*` 不是恢复依赖，上游固定链接、许可与方法说明已落入仓库。

### 可直接粘贴给下一个会话的恢复指令

```text
请读取 Z-agent 参考目录中的 docs/HANDOFF.md、docs/requirements/README.md、
docs/requirements/interviews/Z-brainstorming.json，以及已确认的 Z-deep-interview 总结。
我们正在逐项访谈 24 个 Skill 和 6 类 Agent 的需求；不要提前实现或进入下一 Skill。
当前 Z-brainstorming 已回答 4 轮，第 5 轮 BS-Q005 保持待答，请从这个问题继续。
访谈由你直接单线程进行，专家不执行访谈；未来 brainstorming 的设计和对抗式审查需要专家参与。
每题每答直接保存 JSON，全部历史保留，同一事项采用最新内容。
先核验参考目录在本项目的实际位置与写入能力，并保留已确认总结的摘要哈希。
```

## 来源与验证

- [oh-my-codex deep-interview](references/oh-my-codex-deep-interview.md)：0.21.10，固定提交 `2e3e629617899c867049e4979ef1355cdea94e9f`；Yeachan Heo，MIT。
- [Superpowers brainstorming](references/superpowers-brainstorming.md)：7.0.0，固定提交 `bb92a77741419a4ab5f06e711a283343f1ada0c3`；Jesse Vincent，MIT。Builder Check 是上游缺口检查，完整对抗式审查契约是本项目尚待明确的适配要求。
- [mattpocock/skills 安装来源](references/mattpocock-skills.md)：已安装文件与核验提交逐字节一致；Matt Pocock，MIT。

本次上传前核验 Schema 与示例、登记数量和阶段分布、问答数量和评分计算、唯一待答问题、专家三标题、已确认总结摘要以及来源许可证。验证的是资料与契约一致性，不代表 MLIR/NPU/Runtime 工程案例或宿主运行时已测试通过。
