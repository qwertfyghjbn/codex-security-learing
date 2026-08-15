# 证据分级与假设控制

## 目标

约束研究结论的强度，避免把源码事实、官方文档、社区现象和环境猜测混写。

## 本文件覆盖范围

- 证据来源优先级
- 事实 / 推断 / 假设 / 未指定项标签
- 社区 issue 的正确使用方式
- 不应过度归因的主题

## 关键结论

- 源码与官方文档是主证据，社区 issue 主要用于生成假设。
- “没有直接看到证据”不等于“系统没有这个能力”；同样，“看到用户报告”也不等于“系统存在通用缺陷”。
- 对 provider、平台、私有 patch、safety block 这类主题，必须强制保留不确定性标签。

## 证据优先级

建议按下面顺序评估：

1. 当前公开源码
2. 官方 CLI / SDK / sandbox / security 文档
3. 官方 tests
4. 官方 skills / schemas / scripts
5. 社区 issue / 用户报告
6. 自己后续在受控环境里的验证

## 推荐标签

```text
[F] Fact
    直接由当前源码/官方文档证明

[I] Inference
    多项事实共同支持的架构解释

[H] Hypothesis
    尚未走完证据链，只能先列为待验证

[U] Unspecified
    用户环境或私有实现未知

[V] Future validation
    需要未来在隔离环境中验证
```

## 典型写法

```text
[F] 当前官方 provider 列表无直接 "deepseek"。
[U] 用户实际 DeepSeek 接入方式未知。
[H] 失败可能与 credential projection 有关。
[V] 需要未来在 fake provider / isolated worker harness 中验证。
```

## 社区讨论的正确用法

社区 issue 适合回答：

- 哪些 failure 模式在真实环境中出现过
- 哪些路径值得补 regression hypothesis
- 哪些平台/编码/env 问题需要单列检查

社区 issue 不适合直接回答：

- 根因已经确定
- 问题对所有平台/版本都成立
- 用户本地现象一定与报告中的 issue 同源

## 必须显式控制的高风险归因

- “DeepSeek 不兼容”
- “问题来自 sandbox”
- “问题来自 cyber safety”
- “问题来自 provider”
- “问题来自本地平台编码”

这些说法在没有直接证据前都只能是 H 或 U。

## 方法论任务

1. 每条关键发现都带标签。
2. 社区 issue 只进入 hypothesis backlog。
3. 源码事实与本地环境事实分开写。
4. 所有未来实验前都写清楚最小验证目标。

## 与其他文件的关系

- 版本与边界见 [01-scope-version-and-boundaries.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/01-scope-version-and-boundaries.md)
- 离线验证落地见 [04-offline-validation-and-fixtures.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/04-offline-validation-and-fixtures.md)
