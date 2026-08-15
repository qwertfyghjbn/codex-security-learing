# 路径、文件系统与 Artifact 完整性

## 目标

分析本地文件系统边界、路径归一化、symlink/realpath 防护，以及结果产物的完整性机制。

## 本文件覆盖范围

- target path / output/state/runtime 路径边界
- path normalization / containment
- symlink / lstat / realpath
- artifact tamper / digest / seal / TOCTOU

## 关键结论

- 这是公开实现里最值得优先做安全审计的一条主线。
- 路径问题既影响输入边界，也影响结果可信性。
- `contract.ts` 的路径身份校验与 `api.ts` 的前置路径校验应被看成同一防线的两个阶段。

## 输入侧路径风险

需要重点审查：

- repository path
- target include/exclude paths
- output directory
- state directory
- runtime / temp 目录

核心问题：

- 是否允许 `../`
- 是否允许绝对路径
- Windows 风格路径如何处理
- repo-relative path 是否被稳定约束

## Containment 审计框架

对每个路径敏感点都检查：

1. 原始输入是什么
2. 如何 normalize
3. 是否做 `realpath`
4. 是否做 containment 判断
5. 是否在使用前后都保持同一身份

## symlink / filesystem identity

建议把以下操作统一视为关键检查点：

- `lstat`
- `realpath`
- `readlink`
- 普通文件校验
- 父目录身份校验

需要关注：

- scan skill 文件必须为普通非 symlink 文件
- canonical artifacts 是否在读取前再次验证真实路径身份

## Artifact 完整性

结果侧的关键问题包括：

- findings / coverage / manifest 是否属于同一 scan
- scope 是否一致
- remote URL 是否安全
- artifact digest 是否匹配
- seal 是否正确约束封存状态

## 典型风险场景

### 1. 路径逃逸

- `../secret`
- `/etc/passwd`
- `a/../../secret`
- `C:\outside\secret`

### 2. symlink 绕过

- 输出目录下替换为 symlink
- canonical artifact 指向外部路径
- skill 文件被间接链接到非预期位置

### 3. 结果伪造

- manifest.scan.id != findings.scanId
- coverage.scope 与 manifest.scope 不一致
- digest 仍引用旧文件内容

### 4. TOCTOU

- 检查后替换
- finalize 前后切换文件身份
- seal 记录与当前实物不一致

## 风险判定标准

后续只有在下面条件成立时，才应升级为高价值问题：

- 非授权路径能够被读取或写入
- 非授权文件能够冒充 canonical artifact
- 路径身份变化不会被 contract 阶段识别
- scope/scan/document 不一致仍能通过可信化

## 审计任务

1. 建立路径敏感点目录。
2. 画 `api.ts` 前置路径检查与 `contract.ts` 后置结果检查的双阶段图。
3. 列出所有 symlink 防护点与潜在遗漏点。
4. 为 path/scope/tamper 场景建立 synthetic fixtures。

## 与其他文件的关系

- 结果语义机制见 [03-finding-semantics-and-contract.md](/home/lqs/codex_security_learing/docs/codex-security/01-source-code-learning/03-finding-semantics-and-contract.md)
- 离线 fixtures 设计见 [04-offline-validation-and-fixtures.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/04-offline-validation-and-fixtures.md)
