# Runtime、Sandbox 与 Workbench

## 目标

理解运行时初始化、权限 profile、sandbox 分层，以及 Python workbench 在整个扫描链路中的位置。

## 本文件覆盖范围

- `runtime.ts` 的职责
- Python / plugin discovery
- `default_permissions` 与 `approvalPolicy`
- sandbox / approval / scope 分层
- workbench / deep worker / finalize

## 关键结论

- `approvalPolicy: "never"` 只代表“不弹交互审批”，不代表“没有技术边界”。
- `default_permissions: "codex_security_scan"` 是运行时边界的一部分。
- workbench 不是附属脚本，而是编排链路的一部分。

## Runtime 学习重点

建议重点回答：

- Codex executable 如何发现
- Python 如何发现
- bundled plugin 如何定位
- state/runtime/tmp/output 目录如何准备
- runtime 相关目录如何避免落入受保护的 repository root

## 权限分层

后续阅读中要明确区分：

1. OS account 权限
2. Codex sandbox 技术边界
3. Codex Security 固定 scan permissions profile
4. approval policy
5. network policy
6. repository scope / output scope

报告明确反复提醒：

- sandbox 和 approval 不是一回事
- 不同平台的 sandbox 实现也不是一回事
- 本地运行仍共享宿主账户权限与本地状态

## `default_permissions` 与 `approvalPolicy`

需要重点核实：

- scan thread 是否固定使用 `codex_security_scan`
- login shell 是否禁用
- 哪些位置由 Codex Security 明确设置，哪些由底层 runtime 持有

建议把它们作为一个关键设计发现记录下来，而不是作为漏洞描述。

## Workbench 的职责

报告中 workbench 职责可整理为：

- register scan
- prepare completion
- complete/finalize scan
- deep worker / worker 状态管理
- 输出中间产物与 canonical artifacts

## Deep worker 观察点

后续读 Python 脚本时，重点看：

- worker 启动参数
- projected environment
- provider 继承
- progress / status 记录
- failure 后是否仍能 finalize

## PoC / 验证机制的学习入口

在公开实现里，验证并不是单一“跑 exploit”的动作，而是多种强度的方法：

- 静态评估
- unit / integration 风格验证
- 调试器 / crash / sanitizer
- 真实接口最小复现

当前离线阶段只采纳：

- 静态阅读
- synthetic harness
- mock/stub

## 学习任务

1. 画一张 runtime 初始化流程图。
2. 建立权限边界解释图：OS -> sandbox -> scan profile -> approval -> scope。
3. 列出所有 workbench 脚本及职责。
4. 标出 deep worker 与标准 scan 的分叉点。

## 与其他文件的关系

- 凭据/env 风险见 [02-credentials-env-and-local-trust.md](/home/lqs/codex_security_learing/docs/codex-security/02-security-analysis/02-credentials-env-and-local-trust.md)
- 状态机与日志风险见 [04-subprocess-parser-unicode-and-state.md](/home/lqs/codex_security_learing/docs/codex-security/02-security-analysis/04-subprocess-parser-unicode-and-state.md)
