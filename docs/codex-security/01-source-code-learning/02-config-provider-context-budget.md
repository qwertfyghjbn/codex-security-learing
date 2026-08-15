# 配置、Provider、上下文与预算编排

## 目标

学习影响扫描行为的“输入编排层”：配置如何被合并，provider 如何被选定，上下文如何被拼装，预算如何影响执行。

## 本文件覆盖范围

- `config.ts` 与 override 边界
- provider / env_key / base_url / wire API 的追踪思路
- 上下文来源与压缩点
- budget / cost / abort 的学习入口

## 关键结论

- `config.ts` 不是简单配置读取器，而是权限边界的一部分。
- provider 问题应按“名称 -> 凭据 -> endpoint -> worker 继承”链路逐层排查，不能直接跳到模型兼容性结论。
- 上下文不是无限追加的文本集合，而是被 skills、workbench、coverage 约束过的输入。

## 配置编排

重点学习以下问题：

- 哪些配置来自 CLI/SDK 参数
- 哪些来自默认配置
- 哪些来自 `codexOverrides`
- 哪些是 Codex Security 强持有、不可被用户覆盖的配置

报告中特别强调要关注：

- `model_provider`
- `model_providers`
- plugin loading 相关项
- native multi-agent v2 相关项
- `__proto__`、`constructor`、`prototype` 这类保留 key 的拒绝逻辑

## Provider 追踪框架

建议按下面这条链路阅读：

```text
CLI --provider / SDK overrides
  -> provider validation
  -> provider config { name, base_url, env_key, wire_api }
  -> auth selection
  -> selected environment
  -> runtime creation
  -> coordinator thread
  -> worker/subagent inheritance
```

对每一层都回答四个问题：

| 问题 | 要找的证据 |
|---|---|
| provider 名称在哪里被允许 | enum、CLI parser、config validation |
| API key 名称如何确定 | `env_key`、env projection 逻辑 |
| endpoint / wire 协议如何确定 | `base_url`、`wire_api`、provider abstraction |
| worker 是否继承同样 provider | deep worker / subagent / projected env |

## DeepSeek 相关阅读纪律

这份报告的立场应保持：

- 当前官方 provider 列表没有直接 `deepseek`
- 这不等于“DeepSeek 无法间接使用”
- 你本地如何接入 DeepSeek，属于环境事实，不能从公开源码先验推出

因此学习时应避免把“provider 未直接列出”写成“系统不兼容”。

## 上下文来源

后续代码阅读中，应至少区分这些上下文源：

- repository text
- user context
- threat model
- knowledge base
- repository policy / AGENTS / README / 注释
- coverage / findings / validation 中间产物

## 上下文编排问题

需要明确：

1. 哪些上下文直接进入 scan prompt
2. 哪些上下文先被 skills 转换为结构化任务
3. 哪些上下文只作为参考资料，不具备控制权
4. 哪些地方发生裁剪、归纳、过滤、压缩

## 上下文压缩的观察点

建议从这些位置找压缩点：

- `security-scan` / `threat-model` / `validation` 等 skills
- knowledge base 准备逻辑
- coverage 或 files reviewed 的约束
- workbench 生成的中间文件
- completion 阶段对结果的规范化

## 预算与成本

预算问题在报告里不是主轴，但有明确入口：

- `cost.ts`
- `api.ts` 中的 abort / cancellation 路径
- events / progress / history 相关模块

建议区分三个层面：

- 模型/worker 成本
- 生命周期控制：超预算后如何停止
- 结果一致性：budget abort 后 status、history、artifacts 是否一致

## 学习任务

1. 列出 `config.ts` 中所有“不可覆盖的关键项”。
2. 画一张 provider/env 传播图。
3. 建立上下文来源矩阵，标每一项的权威级别。
4. 建立成本控制状态图，标出 abort 后的收敛路径。

## 与其他文件的关系

- 上下文不可信带来的安全问题见 [01-agent-context-and-prompt-injection.md](/home/lqs/codex_security_learing/docs/codex-security/02-security-analysis/01-agent-context-and-prompt-injection.md)
- 凭据/env 风险见 [02-credentials-env-and-local-trust.md](/home/lqs/codex_security_learing/docs/codex-security/02-security-analysis/02-credentials-env-and-local-trust.md)
- 方法论上的“不要先验下结论”见 [03-evidence-grading-and-hypothesis-control.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/03-evidence-grading-and-hypothesis-control.md)
