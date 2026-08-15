# 容器部署与供应链攻击面

## 目标

分析 Docker/Compose 硬化边界，以及第三方依赖引入的输入面和供应链风险。

## 本文件覆盖范围

- Dockerfile / Compose 的安全硬化
- 非 root、cap drop、seccomp、no-new-privileges
- 第三方依赖的输入面
- 供应链与再分发的注意点

## 关键结论

- 容器硬化是“某类部署方式的额外保护”，不能外推为所有本地运行都自动拥有同样边界。
- 依赖风险更适合先做“攻击面 inventory”，不要直接跳到未经证实的漏洞判断。

## 容器硬化观察点

报告已提到的额外硬化包括：

- 非 root 用户
- `cap_drop: ALL`
- `no-new-privileges:true`
- seccomp
- 只读 bind mount 输入

后续审计中应明确：

- 这些硬化在哪些部署路径生效
- 哪些本地 CLI/SDK 路径并不自动继承这些限制

## 供应链观察点

报告点名的一些依赖类别：

- AJV
- ZIP / archive 处理
- CSV
- PDF
- TOML
- Codex runtime / SDK 依赖

这些依赖的分析重点是：

- 它们是否直接处理不可信输入
- 是否参与结果可信化
- 是否位于高权限路径

## 依赖审计的正确姿势

建议先做：

- 依赖到输入面的映射
- 依赖到 sink 的映射
- 哪些依赖在扫描前置阶段运行
- 哪些依赖只在结果解析阶段运行

不建议先做：

- 在没有 source-to-sink 证据时直接写“存在 RCE”
- 把常见 parser 库简单等同于 exploitable 漏洞

## 许可与分发边界

报告也提到：

- 公开 package 采用 Apache-2.0
- 学习笔记、独立 synthetic harness 通常比复制整套源码更简单
- 私有 patch、桌面未公开部分、云端实现的许可边界不能从公开仓库直接推出

## 审计任务

1. 为 Docker/Compose 建一张“部署边界与适用范围”表。
2. 为解析类依赖建立攻击面清单。
3. 标出哪些依赖位于高权限路径。
4. 区分“开源可见部分”与“运行时外部依赖部分”。

## 与其他文件的关系

- 研究边界见 [01-scope-version-and-boundaries.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/01-scope-version-and-boundaries.md)
- 受控实验前置条件见 [05-controlled-experiment-entry-criteria.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/05-controlled-experiment-entry-criteria.md)
