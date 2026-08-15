# Findings、Coverage 与 Contract 语义

## 目标

理解 Codex Security 如何表示一次扫描结果，以及为什么“模型输出”不直接等于“可信结果”。

## 本文件覆盖范围

- `findings / manifest / coverage` 三件套
- `contract.ts` 的角色
- schema + semantic + path + digest/seal 验证链
- finding identity / scan identity

## 关键结论

- 结果可信性是独立层，不依赖“模型看起来合理”。
- `contract.ts` 是本项目最值得优先精读的安全边界模块之一。
- 扫描结果至少要跨过四层门槛：结构、语义、路径身份、完整性。

## 结果三件套

报告把下面三个 canonical documents 当成结果闭环核心：

- `scan-manifest.json`
- `findings.json`
- `coverage.json`

理解重点不是文件名本身，而是它们各自承担的语义：

| 文件 | 主要语义 |
|---|---|
| manifest | 这次扫描是什么、覆盖范围是什么、何时完成/封存 |
| findings | 发现了什么问题、这些问题如何与 scan 绑定 |
| coverage | 实际审查了什么、覆盖声明与 scope 是否一致 |

## Contract 验证链

可把 contract 理解成下面这条可信化管道：

```text
untrusted artifacts
  -> JSON/schema validation
  -> cross-document semantic validation
  -> path safety + filesystem identity checks
  -> digest / seal validation
  -> trusted scan result
```

## 重点函数

建议优先逐行标记：

- `loadContract`
- `validateCanonicalContract`
- `requireScanFile`
- `requireCheckedScanFile`
- `validateSeal`
- `validateExpectation`
- `safeRelativePath`
- `safeScopePath`

## 重点机制

### 1. Schema 验证

使用 AJV 2020 做结构校验。这里回答的是：

- JSON 结构是否符合 schema
- 字段形状是否正确

它不回答业务语义是否正确。

### 2. 语义交叉校验

这里回答的问题包括：

- scan IDs 是否跨文档一致
- scope include/exclude 是否一致
- findings / coverage 是否都属于同一 scan 语义域

### 3. 路径与文件系统身份校验

这里回答的问题包括：

- 文件是否是普通文件
- 是否是 symlink
- realpath 后是否仍在允许范围内
- repo-relative path 是否安全

### 4. 完整性校验

这里回答的问题包括：

- artifact SHA-256 是否匹配
- seal 记录与当前文件内容是否一致
- finalize 前后的文档身份是否发生变化

## identity 相关学习点

需要单独追：

- scan identity 在哪里产生
- finding identity 是否由稳定字段和哈希派生
- 哪些字段参与 identity
- 哪些字段只影响展示，不影响 identity

## 结果可信性的核心断言

后续阅读时应始终保留这个断言：

> 模型、worker、脚本写出的 JSON 只是候选结果；只有通过 `contract.ts` 的多层验证后，才是 SDK 可以信任的结果。

## 学习任务

1. 建一张 `manifest / findings / coverage` 对照表。
2. 标出 contract 的结构校验、语义校验、路径校验、完整性校验分别在哪。
3. 为 finding identity 与 scan identity 画一张依赖图。
4. 提炼最小可执行规格函数：safe path、digest verify、cross-doc scan id verify。

## 与其他文件的关系

- 安全视角的路径/完整性风险见 [03-path-filesystem-and-artifact-integrity.md](/home/lqs/codex_security_learing/docs/codex-security/02-security-analysis/03-path-filesystem-and-artifact-integrity.md)
- 离线验证设计见 [04-offline-validation-and-fixtures.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/04-offline-validation-and-fixtures.md)
