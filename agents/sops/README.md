# 专家 SOP

每位专家总结并维护自己的操作规范，保存为 `<role_id>/<version>.json`。数据结构见 [expert-sop.schema.json](../../schemas/expert-sop.schema.json)，初始编写模板见 [sop.json](../../templates/subagent/sop.json)。

SOP 是专家的可复用总结。每个任务的目标和边界从 Task 生成，使用 [执行模板](../../templates/subagent/execution.md) 的三个专门标题；SOP 的同一版本在不同任务中保持原文和规则顺序。

`draft` 尚不能分派执行。负责专家核对适用范围和规则后标为 `confirmed`，记录真实作者与角色；这表示专家核对，不要求另向用户确认已经授权的常规执行。当前目录没有已确认 SOP，模板和示例不能充当真实专家总结。

确认后保持版本文件不可变。任务以路径、版本和 SHA-256 锁定 SOP；执行反馈用于提出下一版本，不在当前尝试中删改规则或放宽任务边界。权限不足或 SOP 与任务冲突时报告阻塞。
