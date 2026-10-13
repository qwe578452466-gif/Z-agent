# Superpowers brainstorming 来源与适配依据

核验日期：2026-10-13。用途：Z-brainstorming 需求访谈的上游依据；当前仅研究源码，未安装、运行或移植该 Skill。

## 来源与版本

- 项目： [obra/superpowers](https://github.com/obra/superpowers)。
- 作者及版权标注：Jesse Vincent；Copyright (c) 2025 Jesse Vincent。
- 插件版本：7.0.0，来自固定提交的 `.claude-plugin/plugin.json`。
- 固定提交：`bb92a77741419a4ab5f06e711a283343f1ada0c3`，提交日期 2026-10-09。
- 许可证：MIT；[原文副本](licenses/superpowers-MIT.txt)。复制或实质移植时应保留版权与许可声明。
- [brainstorming 原文](https://github.com/obra/superpowers/blob/bb92a77741419a4ab5f06e711a283343f1ada0c3/skills/brainstorming/SKILL.md)。
- [Builder Check 原文](https://github.com/obra/superpowers/blob/bb92a77741419a4ab5f06e711a283343f1ada0c3/skills/brainstorming/builder-check-prompt.md)。
- [插件元数据](https://github.com/obra/superpowers/blob/bb92a77741419a4ab5f06e711a283343f1ada0c3/.claude-plugin/plugin.json)、[许可证](https://github.com/obra/superpowers/blob/bb92a77741419a4ab5f06e711a283343f1ada0c3/LICENSE)、[发布记录](https://github.com/obra/superpowers/blob/bb92a77741419a4ab5f06e711a283343f1ada0c3/RELEASE-NOTES.md)。

7.0.0 发布记录明确说明 brainstorming 重建。以下结论以该版本为准，不把旧版“固定给几个方案”等印象写成当前源码事实。

## 上游实际方法

| 阶段 | 源码行为 | 对本项目的启发 |
|---|---|---|
| 判断已知信息 | 先检查请求中的意图与原因是否足够；简单明确任务可直接处理 | 不假设所有任务必须套同样长流程；Z 的具体排除条件仍待确认 |
| 理解与澄清 | 一次一个开放问题，先听用户描述；不附示例答案列表；卡住时才提供菜单 | 用户不必预先列出全部阻碍，Skill 应帮助发现问题 |
| 建立依据 | 了解已有工作、目标、非目标、反目标、资源和技术选择；可提出调研 | 复用现有项目与成熟方案，而不是凭空提出设计 |
| 探索方向 | 私下展开多个想法并淘汰弱方案，仅在有助于决定时展示比较 | 不把固定数量的方案或推荐选项作为开场义务 |
| 分段复述 | 把已理解的内容复述给用户检查；明确标出推测和由执行者作出的选择 | 避免把模型假设记成用户决定，及时纠正理解 |
| 判断工作规模 | 区分明确小任务、小改动、需书面设计的项目；独立项目按顺序拆开 | 探索深度应随任务而变，具体 Z 规则待访谈 |
| 书面设计 | 项目输出可供领域执行者据以规划的文档，覆盖意图、目标/非目标/反目标、约束、已决定和留给执行者的事项 | 上游把部分设计文档职责包含在 brainstorming 内，已在 BS-Q003 明确：Z-brainstorming 负责设计，调用 Z-design-document 的写作 SOP 生成文档 |
| Builder Check | 查找影响执行计划的重要缺口；已答内容补入文档，小问题自行决定并标注，只有用户能决定的问题回到用户 | 上游审查方法可参考；不因此在当前访谈中启动专家或 Subagent |
| 用户确认 | 全部描述获确认前不构建；用户明确跳过问答或同意临时原型有专门例外 | 审批对象及例外是否适用于 Z，尚未确认 |
| 后续计划 | 项目文档获确认后调用 writing-plans | 与本项目后续 Skill 的交接方式尚未确认，不自动移植调用链 |

## 上游契约及待适配点

输入不是固定结构文件：可以是请求、已有项目和讨论中补充的信息。产物随规模变化，小改动可在会话描述，项目形成书面设计。默认软件文档路径是 `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md`，并提交 Git；上游明确允许用户偏好覆盖路径与提交习惯。

上游默认广泛自动触发；用户已在 BS-Q002 明确 Z-brainstorming **手动触发**，此处采用用户要求。BS-Q004 已确认未来设计工作流需要专家 Agent 参与设计与检查并进行对抗式审查，具体专家与审查契约仍待明确。这是用户提出的适配要求；不能把上游 Builder Check 直接等同于已定义完整的对抗式审查。直接构建、原型、浏览器工具、自动提交和 writing-plans 调用仍只是参考事实，尚未成为已确认需求。

本项目已单独规划 Z-deep-interview、Z-design-document 和 Z-execution-document。BS-Q003 明确 Z-brainstorming 负责设计并调用 Z-design-document；后者提供文档写作 SOP，本身不参与设计。输入门槛、需求问题处理、方案深度、设计完成与确认标准，以及其他上游行为是否采用仍待访谈。

## 本次记录边界

用户在 BS-Q002 要求按 2026-10-12 体系建设报告优先寻找成熟方法。该报告全部原文保留在 [Round 2 JSON](../requirements/interviews/Z-brainstorming.json) 的原始回答中。它提供来源优先级；不据其早期角色描述撤销后来明确的“开发前独立、单线程访谈，专家不执行访谈”规则。

本次只核验与当前 Skill 直接相关的 Superpowers，不宣称报告中其他七个项目已全部核验。后续逐项研究对应来源，分别登记作者、许可证和固定版本。
