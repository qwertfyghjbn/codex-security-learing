# 研究范围、版本与边界固定

## 目标

在进入机制学习和安全分析前，先固定研究对象、版本身份和“哪些内容不在当前证据范围内”。

## 本文件覆盖范围

- 已知项 / 未指定项
- 公开仓库与整体产品的边界
- 版本漂移问题
- 不应先验下结论的主题

## 关键结论

- 研究对象首先是公开 `openai/codex-security` 仓库及其对应官方文档，不是整个产品的所有实现。
- package version、plugin version、Codex runtime version 必须分开记录。
- 用户本地环境的 provider、平台、私有 patch、网络策略在未验证前都属于未指定项。

## 当前研究对象

当前可确定的对象是：

- 公开 CLI / TypeScript SDK
- bundled security plugin
- Python workbench / 辅助脚本
- schemas / tests / 容器化材料

当前不应默认等同于：

- 底层 Codex runtime 的全部实现
- 桌面端宿主
- Cloud backend
- 企业私有配置或私有 patch

## 必须保留为未知项的内容

后续记录时应始终显式标“未指定”的项包括：

- 你本地使用的 Codex Security 版本
- Codex CLI/runtime 版本
- plugin 版本
- 操作系统、CPU 架构、shell
- DeepSeek 接入方式
- 自定义 `codexOverrides`
- 是否存在私有 patch/fork
- 网络限制、代理、企业策略

## 版本漂移问题

报告特别指出：

- 仓库 package metadata 与社区 issue 中提到的 plugin 版本可能不一致
- 这不说明谁对谁错，只说明版本身份不能被一个单号概括

因此推荐建立版本矩阵：

| 维度 | 记录内容 |
|---|---|
| repository commit | 当前研究快照 |
| package version | `@openai/codex-security` |
| bundled plugin version | 本地或仓库中可见版本 |
| Codex SDK dependency | 依赖版本 |
| Codex runtime / CLI version | 本地执行环境版本 |
| docs date | 参考官方文档日期 |

## 边界纪律

后续研究中，遇到下面问题时必须保留为待验证，而不是先下结论：

- DeepSeek 是否原生受支持
- 某个 failure 是否来自 provider / sandbox / safety / worker
- 某个 issue 是否为通用行为
- 某个 binary 行为是否等同于公开源码行为

## 方法论任务

1. 先建立 baseline，而不是先做漏洞判断。
2. 所有分析都标注版本和日期。
3. 公开源码事实与本地环境事实分开记录。
4. 每条“不能确认”的结论都显式写成未指定项或假设。

## 与其他文件的关系

- 证据分级见 [03-evidence-grading-and-hypothesis-control.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/03-evidence-grading-and-hypothesis-control.md)
- 进入动态实验前的门槛见 [05-controlled-experiment-entry-criteria.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/05-controlled-experiment-entry-criteria.md)
