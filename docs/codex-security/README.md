# Codex Security 研究拆分索引

## 目标

把原先的长报告内容重组为三类可持续维护的研究文档：

- 源代码学习
- 潜在安全问题分析
- 研究支撑 / 方法论

## 拆分原则

- 不按原报告章节顺序平移，而按后续可执行任务重组。
- 同一段原文可以进入多个文件，但每个文件只服务一个主目标。
- 所有结论保持证据强度意识：源码事实、官方文档事实、推断、假设、未指定项分开写。
- 这里是重组后的结构化版本，不再依赖原始长报告文件存在。

## 文件组织

```text
docs/codex-security/
├── README.md
├── 01-source-code-learning/
│   ├── 01-system-architecture-and-main-flow.md
│   ├── 02-config-provider-context-budget.md
│   ├── 03-finding-semantics-and-contract.md
│   ├── 04-runtime-sandbox-workbench.md
│   └── 05-tests-and-regression-contracts.md
├── 02-security-analysis/
│   ├── 01-agent-context-and-prompt-injection.md
│   ├── 02-credentials-env-and-local-trust.md
│   ├── 03-path-filesystem-and-artifact-integrity.md
│   ├── 04-subprocess-parser-unicode-and-state.md
│   └── 05-container-and-supply-chain.md
└── 03-research-methodology/
    ├── 01-scope-version-and-boundaries.md
    ├── 02-reading-order-and-search-playbook.md
    ├── 03-evidence-grading-and-hypothesis-control.md
    ├── 04-offline-validation-and-fixtures.md
    └── 05-controlled-experiment-entry-criteria.md
```

## 推荐阅读顺序

### 第一批：骨架与核心边界

1. [01-system-architecture-and-main-flow.md](/home/lqs/codex_security_learing/docs/codex-security/01-source-code-learning/01-system-architecture-and-main-flow.md)
2. [03-path-filesystem-and-artifact-integrity.md](/home/lqs/codex_security_learing/docs/codex-security/02-security-analysis/03-path-filesystem-and-artifact-integrity.md)
3. [01-scope-version-and-boundaries.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/01-scope-version-and-boundaries.md)

### 第二批：核心机制与关键安全面

4. [03-finding-semantics-and-contract.md](/home/lqs/codex_security_learing/docs/codex-security/01-source-code-learning/03-finding-semantics-and-contract.md)
5. [04-runtime-sandbox-workbench.md](/home/lqs/codex_security_learing/docs/codex-security/01-source-code-learning/04-runtime-sandbox-workbench.md)
6. [02-credentials-env-and-local-trust.md](/home/lqs/codex_security_learing/docs/codex-security/02-security-analysis/02-credentials-env-and-local-trust.md)
7. [01-agent-context-and-prompt-injection.md](/home/lqs/codex_security_learing/docs/codex-security/02-security-analysis/01-agent-context-and-prompt-injection.md)

### 第三批：长尾安全面与方法论完善

8. [02-config-provider-context-budget.md](/home/lqs/codex_security_learing/docs/codex-security/01-source-code-learning/02-config-provider-context-budget.md)
9. [05-tests-and-regression-contracts.md](/home/lqs/codex_security_learing/docs/codex-security/01-source-code-learning/05-tests-and-regression-contracts.md)
10. [04-subprocess-parser-unicode-and-state.md](/home/lqs/codex_security_learing/docs/codex-security/02-security-analysis/04-subprocess-parser-unicode-and-state.md)
11. [05-container-and-supply-chain.md](/home/lqs/codex_security_learing/docs/codex-security/02-security-analysis/05-container-and-supply-chain.md)
12. [02-reading-order-and-search-playbook.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/02-reading-order-and-search-playbook.md)
13. [03-evidence-grading-and-hypothesis-control.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/03-evidence-grading-and-hypothesis-control.md)
14. [04-offline-validation-and-fixtures.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/04-offline-validation-and-fixtures.md)
15. [05-controlled-experiment-entry-criteria.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/05-controlled-experiment-entry-criteria.md)

## 三类文档的分工

### 1. 源代码学习

关注机制本身：

- 系统分层
- 主执行链路
- 配置 / provider / 上下文 / 预算
- findings / manifest / coverage / contract
- runtime / sandbox / workbench
- tests 体现的隐含设计契约

### 2. 潜在安全问题分析

关注攻击面与失效模式：

- agent / prompt / context injection
- 凭据传播与环境变量泄漏
- 路径、symlink、文件系统逃逸
- artifact 篡改与完整性失效
- 子进程、解析器、Unicode、状态机问题
- 容器与供应链风险

### 3. 研究支撑 / 方法论

关注如何研究，而不是直接下机制或漏洞结论：

- 版本与边界固定
- 阅读顺序和搜索策略
- 证据分级
- 离线验证与 fixtures
- 进入受控实验前的准入门槛

## 当前状态

- 已完成：目录骨架与全部 15 份专题文档初版
- 当前文档树已作为新的研究基线
- 下一步建议：从“第一批”四份核心文档开始，继续补模块级代码引用和你的个人学习笔记
