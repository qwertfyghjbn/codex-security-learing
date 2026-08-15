# 仓库 Agent 策略

本文件是当前仓库的主 Agent 策略文件。

- `AGENTS.md` 是共享规则的权威来源。
- `CLAUDE.md` 是 Claude 专用包装层，共享策略不得偏离本文件。
- 共享运行规则统一放在 `docs/agents/` 下。

## 目的

本仓库用于学习 Codex Security、整理源码学习资料，以及执行结构化安全分析。

仓库当前支持两条 Agent 工作通道：

- Codex + GPT：负责通用工程任务、文档整理、仓库结构维护、非安全类实现工作。
- Claude Code + Grok：负责安全审计、攻击面分析、漏洞假设、对抗性复核。

## Agent 路由

路由细则见 [docs/agents/agent-routing.md](/home/lqs/codex_security_learing/docs/agents/agent-routing.md)。

默认分工：

- Codex 负责仓库组织、学习文档、Agent 脚手架、非安全工程任务。
- Claude 负责安全审计、威胁复核、可利用性分析、安全发现写作。
- 任何会改变安全结论或声称存在漏洞的任务，Claude 是主审通道。
- 任何会改变仓库结构或共享运行文档的任务，Codex 是主编辑通道。

## 运行时保护

运行时与执行边界见 [docs/agents/runtime-guardrails.md](/home/lqs/codex_security_learing/docs/agents/runtime-guardrails.md)。

强默认规则：

- 未经明确批准，不使用真实密钥或真实凭据。
- 分析阶段优先使用假密钥与 synthetic 输入。
- 静态研究阶段不执行第三方仓库代码、不运行 package lifecycle scripts、不运行不受控安装器。
- 安全发现不得自动改写共享项目文档，需先独立成文再人工确认。
- 两个 Agent 并行工作时，优先使用独立分支或独立 worktree。

## 文件归属

主归属如下：

- Codex 负责：
  - `docs/codex-security/`
  - `docs/agents/`
  - 一般性的仓库组织文档
- Claude 负责：
  - `docs/security/`
  - `reports/security/`
  - `docs/agents/findings/`
- 共享但受控：
  - `AGENTS.md`
  - `CLAUDE.md`
  - `CONTEXT.md`
  - `docs/adr/`

规则：

- 共享文件应只有一个主编辑角色。
- 如果某任务必须由非主角色编辑共享文件，必须说明原因。
- 安全发现应先写成独立文档，再决定是否合并进共享总结。

## Git 工作规则

- 变更范围要清晰、可追踪。
- Codex 与 Claude 的工作优先放在独立分支。
- 提交信息前缀建议固定：
  - `codex: ...`
  - `claude-security: ...`
- 避免两个 Agent 同时修改同一文件。

## Agent skills

### Issue tracker

本仓库的 issue 追踪器为 `origin` 指向的 GitHub Issues。详见 [docs/agents/issue-tracker.md](/home/lqs/codex_security_learing/docs/agents/issue-tracker.md)。

### Triage labels

本仓库使用默认的五个分诊标签：`needs-triage`、`needs-info`、`ready-for-agent`、`ready-for-human`、`wontfix`。详见 [docs/agents/triage-labels.md](/home/lqs/codex_security_learing/docs/agents/triage-labels.md)。

### Domain docs

本仓库当前按 single-context 布局处理 domain docs。未来如新增 `CONTEXT.md` 或 `docs/adr/`，它们将成为术语和架构决策的主来源。详见 [docs/agents/domain.md](/home/lqs/codex_security_learing/docs/agents/domain.md)。

## 当前仓库状态

- Git 远程：GitHub 私有仓库
- Issue tracker：GitHub Issues
- Domain-doc 布局：single-context
- 安全审计通道：Claude Code + Grok
- 通用工程通道：Codex + GPT

## 变更策略

- 共享运行规则变更时，先更新 `docs/agents/*`。
- `CLAUDE.md` 必须保持与本文件对齐。
- 除非确有工具兼容需求，不要再新增并行的 Agent 策略文件。
