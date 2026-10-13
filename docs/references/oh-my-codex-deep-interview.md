# oh-my-codex deep-interview 参考依据

用于定义 Z-deep-interview 的功能需求。当前只读取源码，没有安装或执行 oh-my-codex，也没有完成 Z-deep-interview 的实现。

## 来源与归属

- 官方包：`oh-my-codex@0.21.10`，npm repository 指向 `Yeachan-Heo/oh-my-codex`。
- 固定提交：`2e3e629617899c867049e4979ef1355cdea94e9f`，与 npm gitHead 一致。
- 包作者：Yeachan Heo；Skill 文件没有单独作者声明，不推断其唯一作者。
- 许可证：MIT；[保存的许可文本](licenses/oh-my-codex-MIT.txt)，版权归 `2026 Yeachan-Heo`。
- 使用方式：功能研究和需求方法参考；未移植其宿主、CLI、工具或应用代码。

## 功能与实现证据

| 内容 | 固定版本的直接证据 |
|---|---|
| 档位与每轮一问 | [SKILL.md 第 29、45 行起](https://github.com/Yeachan-Heo/oh-my-codex/blob/2e3e629617899c867049e4979ef1355cdea94e9f/skills/deep-interview/SKILL.md#L29) |
| 配置默认值与校验 | [src/config/deep-interview.ts](https://github.com/Yeachan-Heo/oh-my-codex/blob/2e3e629617899c867049e4979ef1355cdea94e9f/src/config/deep-interview.ts#L54) |
| mode state 示例 | [SKILL.md 第 123 行起](https://github.com/Yeachan-Heo/oh-my-codex/blob/2e3e629617899c867049e4979ef1355cdea94e9f/skills/deep-interview/SKILL.md#L123) |
| 分维度评分、完成门与记录 | [SKILL.md 第 297 行起](https://github.com/Yeachan-Heo/oh-my-codex/blob/2e3e629617899c867049e4979ef1355cdea94e9f/skills/deep-interview/SKILL.md#L297) |
| 问题和答案的已实现数据类型 | [src/question/types.ts](https://github.com/Yeachan-Heo/oh-my-codex/blob/2e3e629617899c867049e4979ef1355cdea94e9f/src/question/types.ts#L40) |
| 已回答问题的恢复与等待状态 | [src/question/deep-interview.ts](https://github.com/Yeachan-Heo/oh-my-codex/blob/2e3e629617899c867049e4979ef1355cdea94e9f/src/question/deep-interview.ts#L197) |
| 输出规格与 transcript | [SKILL.md 第 337 行起](https://github.com/Yeachan-Heo/oh-my-codex/blob/2e3e629617899c867049e4979ef1355cdea94e9f/skills/deep-interview/SKILL.md#L337) |
| 工作流未硬性阻止越过未完成访谈 | [workflow-transition.test.ts 第 360 行起](https://github.com/Yeachan-Heo/oh-my-codex/blob/2e3e629617899c867049e4979ef1355cdea94e9f/src/state/__tests__/workflow-transition.test.ts#L360) |
| 缺少完成证据时返回 advisory | [completion-gate.ts 第 421 行起](https://github.com/Yeachan-Heo/oh-my-codex/blob/2e3e629617899c867049e4979ef1355cdea94e9f/src/autopilot/completion-gate.ts#L421) |

## 清晰度与歧义

原版由模型为每个维度打 0～1 分，并给出 justification 和 gap。它没有独立的客观清晰度测量算法；加权计算可以复核，但维度评分是模型判断。

| 维度 | 含义 | Greenfield | Brownfield |
|---|---|---:|---:|
| intent | 为什么要做 | 0.30 | 0.25 |
| outcome | 最终状态 | 0.25 | 0.20 |
| scope | 做到哪里 | 0.20 | 0.20 |
| constraints | 技术和业务约束 | 0.15 | 0.15 |
| success | 如何判断完成 | 0.10 | 0.10 |
| context | 已有代码库理解 | 不适用 | 0.10 |

`clarity = Σ(score × weight)`；`ambiguity = 1 - clarity`。清晰度是歧义度的补数，不能把它当作工程正确率。

| 模式 | 歧义上限 | 等价清晰度下限 | 最多轮次 |
|---|---:|---:|---:|
| quick | 30% | 70% | 5 |
| standard，原版默认 | 20% | 80% | 12 |
| deep | 15% | 85% | 20 |

本项目用户已在 Round 2 选择默认 Deep，覆盖原版 Standard 默认。Round 14 进一步明确：20 轮仍无法收尾就继续深度访谈，因此原版的 Deep 20 轮限制只保留作来源参考；Z 的活动策略没有硬性轮次上限，20 是检查节点。此接续要求属于 Z 定制，不声称原版已提供同样策略。该选择不表示 quick/standard 的覆写入口已经被删除或实现，其配置范围仍需在需求访谈中明确。

## 轮次、追问与收敛

- 每轮严格一个问题。意图、结果、范围、非目标和决策边界优先，随后再细化约束、验收与已有代码上下文。
- 每次回答后保存维度分数、评分理由、缺口和下一焦点；必须有至少一次回访已有答案的例证、假设或取舍检验，即 pressure pass。
- 显示轮次、目标维度和当前歧义。发现源码事实属于上下文更新，不增加用户回答轮次。
- 达到阈值后检查 non-goals、decision boundaries、pressure pass 和 closure audit；分数达标本身不表示完成。
- 保存状态后可恢复，已回答问题不重问。stop/cancel 保存并暂停。
- 原版允许在早退或轮数上限时带残余风险输出；这些不满足本项目“当前 Skill/Agent 访谈完成才进入下一项”的要求。

## 哪些记录字段需要 Z 项目自行明确

原版 mode state 示例有 interview_id、profile、type、rounds、current_ambiguity、threshold、max_rounds、challenge_modes_used、codebase_context、current_stage、current_focus 和 context_snapshot_path。

原版没有为 rounds 的每一条定义严格类型；实际 QuestionRecord 又不包含轮次编号、维度分数和理由。因此，本项目要自行明确 round、question_id、原始答案、before/after 评估、来源更新、完成门和停滞记录之间的关联。这些是 Z 项目的记录设计，不声称它们已经由原版实现。

## Round 12 确认的记录要求

用户要求每次问题以 JSON 保存、每次回答都记录，参考 OMC 记录方式。源码的 QuestionRecord 使用 question_id、session_id、created_at、updated_at、status、question、options 与 answer/answers 关联问题和答案；answer 包含回答类型、选择值或开放文本。访谈状态保留轮次集合、当前歧义、档位、阈值与焦点。

输入校验允许在 allow_other 为真时提供空选项，因而不要求访谈问题必须变成选择题。恢复逻辑会根据已回答记录协调待答义务；问题暂未回答不能被当作已完成。

Z 项目以这些关联与状态机制为参考，额外保存用户要求的轮次、清晰度及讲解内容。当前需求访谈的聚合 JSON 含逐题记录和已知答案历史；实际 Skill 记录器、存储布局与宿主权限处理尚未实现，未复制 OMC 的 renderer、tmux 或 CLI 依赖。

## 本项目的完成门

用户已要求逐项访谈。当前 Z-deep-interview 的完成检查覆盖清晰度阈值、范围与非目标、决策边界、pressure pass、closure audit、本 Skill 的需求覆盖以及摘要确认。Round 13 明确独立、单线程、无联动，关联 Agent 需求与专家 SOP 门不适用于本 Skill；Agent 需求仍作为后续对象单独明确。

原版数值评分与大多数门控属于 Skill 文案；其当前工作流实现和测试允许不带完整门控证据转入规划。本项目不能直接把其 auto-complete 或轮数上限当作自己的需求冻结证据。

## 待验证事项

- 停滞规则提到连续三轮 `±0.05`，原版未明确相邻差值还是窗口范围；本项目需要定义确定规则。
- Codex 宿主是否发现并调用大写 `Z-` 名称尚待实际验证。OMC 的 frontmatter parser 没有强制小写，不能因此证明 Codex 官方兼容性。
- 原版 autoresearch、ralplan、autopilot、tmux 和 omx state 是其宿主能力，不自动成为 Z-deep-interview 的依赖。Round 13 已明确该 Skill 无联动，这些联动行为不移植到其执行流程。
