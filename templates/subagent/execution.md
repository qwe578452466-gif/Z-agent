## goal

{{task.goal}}

- 任务与尝试：{{task.task_id}} / {{task.run_id}} / {{attempt}}
- 输入基线：{{task.inputs}}
- 必需产物：{{task.outputs}}
- 验收条件：{{task.acceptance_criteria}}

## boudaries

- 仓库与版本基线：{{task.scope.repository_path}} / {{task.scope.base_revision}}
- 允许写入：{{task.scope.write_paths}}
- 只读范围：{{task.scope.read_only_paths}}
- 本次约束：{{task.scope.constraints}}
- 所需能力：{{task.required_capabilities}}
- 宿主实际授权：{{host.actual_grants}}
- 执行范围取宿主授权、角色职责和任务 scope 的交集；超出范围先停止并报告。

## SOP(each run)

- 专家与版本：{{expert_sop.role_id}} / {{expert_sop.version}}
- 作者与固定来源：{{expert_sop.authored_by}} / {{task.sop_ref.path}} / {{task.sop_ref.sha256}}

{{expert_sop.rules}}
