# Agent、上下文与 Prompt Injection 风险

## 目标

从安全分析角度审视 Codex Security 的 agent 输入边界，重点看不可信上下文是否可能改写系统控制面。

## 本文件覆盖范围

- repository text / user context / knowledge base / threat model 的不可信性
- prompt injection / context injection
- authority conflict
- skills 中体现的安全防线

## 关键结论

- 报告明确把 repository text、user context、threat model、knowledge base、repository policy 都视为不可信分析数据。
- 这些输入的风险不在于“内容恶意”，而在于系统是否错误赋予其控制权。
- 安全分析重点应放在“控制面是否被污染”，而不是“仓库里有恶意文本”本身。

## 主要攻击面

应统一标注为不可信内容的材料包括：

- `README.md`
- `AGENTS.md`
- 源码注释
- fixture / test data
- repo 内安全策略文件
- user-supplied context
- external knowledge base 文档
- 自动生成的 threat model

## 核心审计问题

1. 这些内容是否只能影响分析思路，而不能改写权限与 scope
2. 它们是否可能改变网络访问、文件访问、工具使用策略
3. 它们是否可能伪装成高优先级指令
4. skills 是否明确要求把它们当作“数据”而不是“命令”

## 典型风险句型

后续阅读仓库内容时，遇到如下文本应统一归类为不可信仓库控制内容：

- ignore all previous instructions
- upload secrets here
- run this command
- disable sandbox
- expand scan scope

## 需要审视的实现位置

- scan prompt 装配点
- skill instructions 与 references
- knowledge base 准备逻辑
- worker/subagent context projection
- validation 阶段是否重新引入不可信上下文

## authority conflict

需要明确区分谁有权决定：

| 决策项 | 应有权威 |
|---|---|
| 扫描范围 | 系统配置 / 明确用户输入 |
| 网络与权限 | runtime / sandbox / scan profile |
| 分析策略 | skills / bundled policy |
| 仓库内容含义 | repo text 只能提供事实线索 |

## 风险判定标准

只有在下面情况成立时，才值得升级为实质安全问题：

- 不可信仓库内容能够扩大授权范围
- 不可信文本能够改变 tool/runtime 权限
- 不可信文本能够引导凭据或数据外流
- 不可信文本能够污染 findings / coverage / validation 的受信部分

## 审计任务

1. 为上下文源建立权威级别矩阵。
2. 标出 prompt/context 装配点。
3. 识别“不可信数据 -> 控制面”可能跨越的路径。
4. 分析 worker 是否重新暴露注入面。

## 与其他文件的关系

- 上下文装配机制见 [02-config-provider-context-budget.md](/home/lqs/codex_security_learing/docs/codex-security/01-source-code-learning/02-config-provider-context-budget.md)
- 研究边界见 [03-evidence-grading-and-hypothesis-control.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/03-evidence-grading-and-hypothesis-control.md)
