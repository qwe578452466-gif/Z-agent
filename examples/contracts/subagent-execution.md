## goal

未执行的模板示例；SOP 为 draft，不能分派。

在已明确的目标 NPU 约束下提出稳定 Softmax 的聚合 Tiling 方案，并与 Golden 验证；本示例的目标规格和技能实现尚未提供。

- 任务与尝试：task-softmax-001 / run-softmax-example / 1
- 输入基线：target-spec；规格尚未提供，不能核验输入基线。
- 必需产物：tiling-design（设计文档）、golden-report（测试报告）。
- 验收条件：tiling-design-check、golden-check；本例均未通过。

## boudaries

- 仓库与版本基线：Z-agent / 未固定，不能进入 ready
- 允许写入：Z-agent/.z-agent/runs/run-softmax-example/artifacts
- 只读范围：Z-agent/docs、Z-agent/registry、Z-agent/agents
- 本次约束：目标规格须明确 Shape、dtype、数值容差、工具链版本、可用存储空间及已确认的 Bank 约束。；没有硬件或官方证据的行为标为 pending；不冻结为事实。；本任务只写分析与验证产物，不修改产品源码。
- 所需能力：workspace.read / workspace.write / shell.execute / hardware.access
- 宿主实际授权：未核验，本示例不授予权限。
- 执行范围取宿主授权、角色职责和任务 scope 的交集；超出范围先停止并报告。

## SOP(each run)

- 专家与版本：mlir-compiler-expert / 0.1.0
- 作者与固定来源：未确认（draft） / Z-agent/examples/contracts/expert-sop.json / 734d2bff98ad4f58a37554f20b0afd919f388a573ae8a334c723b66ccb2fb691

1. [confirm-contract] 每次开始或恢复时读取任务目标、边界、输入基线、技能版本和验收条件；前提不足先报告阻塞。 （每次执行。）
2. [respect-scope] 只在宿主授权、角色职责和当前任务边界的交集内执行；发现冲突先停止，不临时放宽边界。 （每次执行。）
3. [separate-facts] 区分已核验事实、假设和待验证信息；不把未确认的目标或硬件行为写成冻结事实。 （每次执行。）
4. [verify-change] 修改实现后执行与目标相关的正确性和边界检查，保存原始命令、退出码、产物与实际结果。 （适用条件：本次任务修改了实现。）
5. [report-and-handoff] 逐项报告验收和 SOP 遵守情况；明确失败、未执行及阻塞，保留检查点和下一步动作。 （每次执行。）
