---
name: team-learning-upgrade-daily
description: Generate a daily "team learning upgrade" ops report from multi-agent session history, skill inventory, and capability diagnosis. Use when users ask for recurring team intelligence briefs, cross-agent learning audits, skill allocation suggestions, or cron-based organizational review workflows. Supports channel-aware delivery fallback (Discord/Feishu/other/none) and role-adaptive output (CEO/manager/owner/IC), avoiding hard-coded executive assumptions.
---

# Team Learning Upgrade Daily

## Overview

固化“团队学习升级日报”工作流：每天自动分析多 Agent 会话，输出可执行的组织能力建议、技能分配建议与待审批变更清单。

默认是日报场景，也可用于周报/专项诊断。

## Inputs (minimum)

1. `sessions_list(activeMinutes=...)` 与 `sessions_history(...)` 获取近周期会话证据
2. 技能与配置信息（如 `openclaw.json`、workspace `skills/`、已知工具配置）
3. 用户目标与受众角色（CEO / manager / owner / IC）
4. 可用投递渠道（Discord / Feishu / 其他 / 无）

## Workflow

### Step 1: Collect evidence (全量会话分析)

1. 枚举目标 Agent（至少包含当前活跃 Agent + 核心协作 Agent）
2. 为每个 Agent 采集：
   - session 数量
   - 最近活跃时间
   - 主题命中（技能安装、自动化、协作、故障、治理等）
   - 关键证据片段（原话或事件）
3. 形成覆盖率统计：`已覆盖 / 应覆盖 / 覆盖率% / 缺口原因`

若覆盖率 < 80%，先输出“补数动作 Top3”，再给建议。

### Step 2: Skill inventory & gap mapping (技能盘点)

1. 盘点每个 Agent 的已装技能、常用能力、明显短板
2. 输出能力缺口矩阵：
   - 缺什么能力
   - 证据是什么
   - 建议补什么（skill / SOP / workflow）

### Step 3: Capability diagnosis (组织能力评估)

至少评估三类能力并打分（1-5）：

- 信息处理能力（筛选、归纳、证据化）
- 学习迭代能力（纠错、沉淀、复发治理）
- 协作闭环能力（分工、交接、回执、复盘）

每一项必须给证据，不允许空泛打分。

### Step 4: Dual-track recommendations (双线建议)

分两条线输出：

1. **组织负责人线**（不强制称 CEO，可按角色替换）
   - 决策动作
   - 审批动作
   - 风险动作
2. **执行团队线**
   - Agent 级执行动作
   - 技能补齐动作
   - SOP 修订动作

### Step 5: Change list (变更清单)

把所有建议变成 A/B/C/D/E 优先级清单，每项包含：

- 目标
- 预期收益
- 风险
- 回滚点
- 是否需审批（是/否）

涉及配置变更、网关重启、对外发送策略修改等，一律标注“需审批后执行”。

### Step 6: Channel-aware delivery (渠道自适应投递)

使用如下路由策略，不假设用户一定绑定 Discord/Feishu：

1. 若有多个可用渠道：
   - 主渠道发完整版
   - 次渠道发摘要+链接/文件路径
2. 若只有一个渠道：
   - 在该渠道发送完整版
3. 若无外部渠道可用：
   - 将报告落盘到工作区（如 `latest_report.md`）
   - 在当前会话返回摘要 + 文件位置

### Step 7: Execution loop (执行闭环)

1. 明确“待审批项”与“可自动执行项”
2. 记录下一次跟进时间（默认次日同一时段）
3. 若用户回复“批准/驳回/调整”，更新下一轮策略

## Role adaptation rules (非 CEO 适配)

不要把受众硬编码成 CEO。按角色改写“决策面板”标题与措辞：

- CEO / Founder → 决策面板（战略+资源）
- Manager / Team Lead → 管理面板（优先级+协同）
- Owner / Operator → 运营面板（效率+稳定性）
- IC / Specialist → 个人升级面板（技能+流程）

若用户未指定角色，默认使用 `owner/operator` 口径。

## Output format (日报模板)

1. 一句话结论
2. 覆盖率报告（含缺口）
3. 角色化面板（按受众角色命名）
4. 组织能力雷达 Top3（带证据）
5. 双线补强建议（负责人线 / 执行线）
6. 变更清单（A/B/C/D/E + 风险 + 回滚）
7. 渠道投递结果（已投递/待投递/不可投递）
8. 明日行动清单（可执行）

## Quality gates

- 无证据不建议
- 无优先级不落地
- 无回滚点不允许高风险变更
- 无渠道时必须给本地交付路径
- 建议必须可执行（可直接抄用）

## References

- 渠道路由示例：`references/delivery-matrix.md`
- 报告结构模板：`references/report-template.md`
