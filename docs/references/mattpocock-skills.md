# 已安装 mattpocock/skills 来源

- 作者与版权：Matt Pocock；Copyright (c) 2026 Matt Pocock。
- 项目：[mattpocock/skills](https://github.com/mattpocock/skills)。
- 核验提交：49dd158d1076134a641b33efb035946536778336；package.json 版本 1.3.1。此版本是核验提交的仓库元数据，不是安装器记录的独立 Skill 版本。
- 许可证：MIT；[许可原文](licenses/mattpocock-skills-MIT.txt)。
- 已安装目录：`.agents/skills/grill-me/` 和 `.agents/skills/grilling/`，锁定信息见 [skills-lock.json](../../skills-lock.json)。
- 2026-10-13 核验：两目录内 SKILL.md 和 agents/openai.yaml 共四个文件，与上述固定提交逐字节一致。安装时未记录提交，此处登记的是本次验证的匹配来源。

固定来源：[grill-me](https://github.com/mattpocock/skills/tree/49dd158d1076134a641b33efb035946536778336/skills/productivity/grill-me)、[grilling](https://github.com/mattpocock/skills/tree/49dd158d1076134a641b33efb035946536778336/skills/productivity/grilling)。

grill-me 为入口，grilling 提供按决定依赖树推进的方法。上游 grilling 包含同轮多个问题和推荐答案的规则；本项目用户已明确单线程、每轮一个开放问题、先讲解再讨论，使用时以用户规则为准。本次保留上游文件原样，不宣称其内容已经等同于 Z-deep-interview 的最终实现。
