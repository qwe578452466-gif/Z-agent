# Skill 包

当前技能均处于规划状态，见 [登记表](../registry/skills.json)。模板位于 [templates/skill/](../templates/skill/)，不作为可执行 Skill 注册。

实现时使用 `skills/<skill_id>/`，包括 `SKILL.md`、`manifest.json`、参考资料、正反例和边界用例；领域技能还需要真实工程案例。详见 [目录](../docs/01-directory.md) 与 [契约](../docs/02-contracts.md)。

添加实现包后再更新登记表的状态、路径和版本。`validated` 必须有对应验证证据，不能仅依据文件齐全或 Schema 校验通过设置。

登记表区分独立 Skill 与专家工作流使用。Z-deep-interview 是开发流程前、单线程、无联动的独立 Skill，不绑定执行角色，不需要专家 Task 或 SOP。
