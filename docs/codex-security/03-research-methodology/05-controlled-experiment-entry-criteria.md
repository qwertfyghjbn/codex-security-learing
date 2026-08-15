# 进入受控实验前的准入门槛

## 目标

规定什么时候可以从静态研究进入受控动态实验，以及进入前必须固定哪些条件。

## 本文件覆盖范围

- 完整离线工作流
- fake key / 最小权限原则
- 进入 VM/容器实验前的清单
- 未来实验优先顺序

## 关键结论

- 不应在边界、版本、假设都未收敛时直接跑原系统。
- 第一阶段应坚持：不运行原始 scan、不使用真实凭据、不执行目标仓库代码。
- 动态实验必须从最低权限、最低外部依赖的环节开始。

## 推荐的完整离线工作流

```text
固定研究快照
  -> 阅读 SECURITY.md
  -> 建立 control-flow / data-flow / trust-boundary 图
  -> 阅读 skills / workbench / schemas
  -> 形成关键设计发现
  -> 建立 hypothesis backlog
  -> 建立 offline spec + synthetic fixtures
  -> fake provider / fake worker harness
  -> 再考虑受控动态实验
```

## 准入前必须固定的条件

### 版本与对象

- repository commit
- package version
- plugin version
- Codex runtime / CLI version
- docs date

### 环境与隔离

- disposable VM 或等效隔离环境
- 独立的低权限 OS 账户
- 需要时再叠加 container
- 明确网络策略

### 凭据策略

在验证 env projection 前，优先使用 fake values：

- `OPENAI_API_KEY=FAKE_OPENAI`
- `CUSTOM_PROVIDER_API_KEY=FAKE_CUSTOM`
- `GITHUB_TOKEN=DO_NOT_USE`
- `AWS_SECRET_ACCESS_KEY=DO_NOT_USE`

真实联网时再切换为：

- 单次
- 低权限
- 可撤销
- 与实验目标严格最小化匹配

## 动态实验前的禁止项

在纯静态阶段不建议直接做：

- `npm install`
- `pnpm install`
- `npm test`
- 运行第三方 target repository
- 使用真实 provider key 做探测性扫描

原因不是这些动作永远不能做，而是它们会把生命周期脚本、hooks、下载、真实联网与目标行为混在一起。

## 未来实验顺序

建议按下面顺序推进：

1. 纯 contract/schema/path fixtures
2. fake Codex + fake workbench
3. fake custom provider + fake key
4. isolated subprocess/env propagation
5. disposable VM 中验证 sandbox/platform 行为
6. 官方 provider 的最小无敏感仓库 scan
7. 自定义/DeepSeek provider 的最小 scan
8. 最后才是 deep scan / multi-agent / 真实安全目标

## 准入 checklist

- [ ] 已固定版本矩阵
- [ ] 已完成主执行链路图
- [ ] 已完成路径/contract 不变量整理
- [ ] 已建立 hypothesis backlog
- [ ] 已有 fake key 策略
- [ ] 已准备独立隔离环境
- [ ] 已定义最小实验目标

## 与其他文件的关系

- 研究边界见 [01-scope-version-and-boundaries.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/01-scope-version-and-boundaries.md)
- 离线规格设计见 [04-offline-validation-and-fixtures.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/04-offline-validation-and-fixtures.md)
