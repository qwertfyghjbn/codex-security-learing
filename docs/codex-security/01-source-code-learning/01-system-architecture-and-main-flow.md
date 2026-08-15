# 系统分层与主执行链路

## 目标

从源码学习角度建立 Codex Security 的总体结构图，先回答“系统怎么跑”，再回答“漏洞发现能力分布在哪些层”。

## 本文件覆盖范围

- 公开实现的边界
- 分层编排模型
- `CodexSecurity.run()` 主链路
- 信任边界转换点

## 关键结论

- 公开仓库覆盖的是 CLI、TypeScript SDK、bundled security plugin、Python workbench、schemas、tests、容器化材料。
- 不能把公开仓库等同于整个 Codex Security 产品；底层 Codex runtime、桌面宿主、云端服务端逻辑不是同一层。
- 它不是“单一规则引擎 + 漏洞规则库”，而是“多层编排系统”。

## 公开实现的分层

建议先把系统理解成下面几层：

1. CLI / API 入口  
   负责接收参数、装配 options、触发生命周期。

2. 配置 / 凭据 / target 归一化  
   负责 model/provider、repository/scope、auth、output/state/runtime 位置等前置条件。

3. Runtime / Codex thread  
   负责创建和驱动 Codex 运行时，设置权限 profile 与 thread 行为。

4. Skills / References  
   负责表达安全分析工作流，而不是只靠某个 `rules.json`。

5. Python workbench  
   负责 register、prepare、worker、finalize 等外围编排。

6. Structured artifacts  
   负责以 `manifest / findings / coverage` 形式落地结果。

7. Contract validation  
   负责把不可信输出转换成经过 schema、语义、路径、digest/seal 校验后的可信结果。

## 推荐心智模型

```text
input/config
  -> local validation
  -> runtime preparation
  -> Codex thread
  -> skill-driven analysis workflow
  -> workbench output
  -> canonical artifacts
  -> contract validation
  -> trusted scan result
```

## 主执行路径

应优先手工跟踪 `src/api.ts` 中的主路径。报告里的抽象链路可以整理为：

```text
CodexSecurity.run()
  -> operation tracking
  -> #run(repository, options)
  -> #validateLocalInputs(...)
  -> output/state/runtime/tmp path checks
  -> mergedCodexConfig(...)
  -> provider + authentication
  -> runtime preparation
  -> plugin Python resolution
  -> output directory preparation
  -> scan skill filesystem validation
  -> register-cli-scan / workbench
  -> createCodex(...)
  -> startThread(... approvalPolicy: "never")
  -> runStreamed(...)
  -> scan events
  -> prepare-scan-completion
  -> complete-scan
  -> load + validate sealed contract
```

## 为什么这条链路重要

这条链路里每个箭头都对应一个需要学习的边界：

- 参数何时被标准化
- 哪些校验先于副作用发生
- 哪些位置进入 runtime / subprocess / worker
- 哪些结果仍然是不可信 JSON
- 哪些条件下结果才被提升为可信扫描结果

## 信任边界转换点

后续阅读代码时，建议重点标下面这些转换：

| 转换点 | 作用 |
|---|---|
| user input -> local normalized input | 过滤路径、scope、config |
| local config -> runtime config | 锁定 provider 与 permissions |
| repository/context -> skill input | 把仓库内容当作分析数据而不是控制指令 |
| model/worker output -> artifacts | 产生不可信结构化结果 |
| artifacts -> contract validated result | 通过 schema、语义、路径、digest/seal 验证 |

## 学习任务

1. 为 `run()` 主链路画一张调用图。
2. 为每一步补充输入、输出、副作用、外部依赖。
3. 记录哪一步第一次接触 runtime，哪一步第一次接触 worker。
4. 明确哪一步开始处理“不可信结果”。

## 与其他文件的关系

- config/provider 细节见 [02-config-provider-context-budget.md](/home/lqs/codex_security_learing/docs/codex-security/01-source-code-learning/02-config-provider-context-budget.md)
- contract 细节见 [03-finding-semantics-and-contract.md](/home/lqs/codex_security_learing/docs/codex-security/01-source-code-learning/03-finding-semantics-and-contract.md)
- runtime/sandbox 细节见 [04-runtime-sandbox-workbench.md](/home/lqs/codex_security_learing/docs/codex-security/01-source-code-learning/04-runtime-sandbox-workbench.md)

## 待验证问题

- 公开源码中的主路径与本地安装版本是否一致
- 深扫 worker 的链路与标准 scan 的分叉点具体在哪里
- history / recovery / cancellation 是否完全沿同一状态机收敛
