# 凭据传播、环境变量与本地信任边界

## 目标

分析 provider 凭据、环境变量、subprocess/worker 继承，以及本地仓库和本地工具链带来的信任边界问题。

## 本文件覆盖范围

- provider credential propagation
- subprocess / worker env inheritance
- local OS authority 与产品边界
- Git hooks / filters / PATH executable / credential helpers

## 关键结论

- 最重要的风险不是“模型能力不够”，而是凭据和环境边界是否被正确投影。
- 必要凭据丢失和无关凭据泄漏是两个相反但都重要的问题。
- 本地运行不提供多租户式隔离，不能把宿主账户权限误写成产品的安全承诺。

## 核心审计问题

1. provider 需要的 env 是否能稳定进入 coordinator
2. worker/subagent 是否继承同样的 provider 配置与 key
3. 与扫描无关的 secrets 是否被无差别继承
4. 本地 Git/config/PATH 工具是否被默认信任

## 凭据传播链路

```text
user env / keyring
  -> auth selection
  -> selected scan environment
  -> runtime creation
  -> Codex thread
  -> workbench / worker / subprocess
```

对每个环节都应同时检查：

- 必要凭据是否在
- 无关凭据是否被带上

## 风险类型

### 1. 凭据丢失

表现为：

- coordinator 正常，worker 缺 key
- provider 配置存在，但 env_key 没有被投影
- endpoint 存在，但认证 principal 错了

### 2. 凭据泄漏

表现为：

- GITHUB_TOKEN、AWS_*、其他 CI secrets 被无差别继承
- verbose logs 或 artifacts 泄露 env 线索
- unrelated secrets 被暴露给 worker 或模型请求路径

### 3. 错 principal

表现为：

- 多 provider 配置下，使用了错误的 key
- override merge 后，provider identity 被污染

## 本地信任边界

报告特别提醒要把下面这些视为本地授权面的一部分：

- Git hooks
- Git filters
- credential helpers
- PATH executable
- repository-local config

这里的风险不是“这些存在”，而是系统是否误把它们当成可信基础设施。

## 审计重点

- runtime 如何构造 subprocess env
- worker 启动时是否做了 env projection
- 是否存在显式清理 `OPENAI_API_KEY`、`CODEX_API_KEY` 以外其他 secret 的逻辑
- 哪些命令调用会触发本地 Git 或 shell 工具

## 安全分析结论的边界

不要把下面这类观察直接写成漏洞：

- 本地仓库不可信
- PATH 可被劫持
- Git hooks 可能执行代码

只有当公开实现错误宣称自己隔离了这些面，或实现与其边界承诺矛盾时，才构成更强结论。

## 审计任务

1. 画凭据/env 传播图。
2. 标出 subprocess / worker 的 env projection 位置。
3. 列出所有本地信任前提。
4. 为“必要凭据丢失”和“无关凭据泄漏”分别建测试假设。

## 与其他文件的关系

- provider/config 机制见 [02-config-provider-context-budget.md](/home/lqs/codex_security_learing/docs/codex-security/01-source-code-learning/02-config-provider-context-budget.md)
- 进入受控实验前的 fake key 策略见 [05-controlled-experiment-entry-criteria.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/05-controlled-experiment-entry-criteria.md)
