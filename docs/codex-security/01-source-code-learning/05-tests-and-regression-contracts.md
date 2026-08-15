# Tests 与隐含设计契约

## 目标

通过测试文件理解 Codex Security 的真实设计意图，把测试当作一等规格材料，而不是辅助材料。

## 本文件覆盖范围

- `tests-ts/*` 的阅读价值
- 典型测试主题
- issue 到 regression hypothesis 的映射方法

## 关键结论

- 测试目录是理解“系统实际上保证什么”的高价值入口。
- 很多行为不会在 README 或文档里完整写出，但会在测试中被固定。
- 社区 issue 不应直接当漏洞结论，但非常适合转成 regression hypothesis。

## 为什么测试重要

建议把测试看成三类证据：

1. 已明确承诺的行为
2. 历史出错后补上的边界
3. 源码中不易看出的 edge case

## 报告已点名的测试主题

应优先关注：

- environment
- authentication
- events
- cancellation
- recovery
- preflight config
- CLI authentication
- bulk scan discovery
- Unicode / provider / worker lifecycle
- artifact finalization
- path / symlink

## 阅读方式

每读一个测试文件，记录：

- 测试名称
- 保护的行为
- 触发输入
- 预期输出 / 状态
- 对应的生产代码入口
- 是机制学习价值，还是安全边界价值

## 从 issue 到测试假设

社区 issue 的推荐用法：

- 不直接得出“系统有 bug”
- 先抽象成可验证的状态机、编码、worker/env、finalize 问题
- 再找是否已有测试覆盖
- 没有覆盖时，写入 hypothesis backlog

## 推荐建立的 regression matrix

```text
environment
authentication
events
cancellation
recovery
Unicode
provider
worker lifecycle
artifact finalization
path/symlink
```

## 学习任务

1. 为每个测试文件做“测试 -> 设计契约”映射。
2. 建一张 regression matrix。
3. 把社区讨论中提到的现象转写成待验证测试假设。

## 与其他文件的关系

- 证据分级见 [03-evidence-grading-and-hypothesis-control.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/03-evidence-grading-and-hypothesis-control.md)
- 离线规格测试见 [04-offline-validation-and-fixtures.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/04-offline-validation-and-fixtures.md)
