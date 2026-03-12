# Delivery Matrix

## Goal
在不确定用户绑定渠道的情况下，保证报告可达。

## Decision order

1. 识别可用渠道（当前会话渠道 + 已配置渠道）
2. 决定主渠道（优先当前会话渠道）
3. 执行投递并记录结果
4. 失败时降级，不中断交付

## Routing rules

### Case A: Discord + Feishu 都可用
- 主渠道：当前会话渠道
- 次渠道：另一渠道
- 内容策略：主渠道完整版，次渠道摘要+链接

### Case B: 仅单渠道可用
- 在该渠道发送完整版

### Case C: 无外部渠道可用
- 报告保存到本地：`latest_report.md`
- 在当前会话回复：摘要 + 文件路径 + 下一步建议

## Failure fallback

- 单渠道发送失败：重试一次；仍失败则落盘并回报失败原因
- 双渠道其中一个失败：另一个渠道照常发送；失败渠道改为回执说明

## Minimal delivery log

- report_id
- timestamp
- channel_attempted
- result(success/fail)
- fallback_used
