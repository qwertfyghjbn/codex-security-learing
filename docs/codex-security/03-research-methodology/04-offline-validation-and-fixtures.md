# 离线验证、可执行规格与 Synthetic Fixtures

## 目标

把报告中的离线研究方法落成一套“可执行规格”思路：不运行原系统，不用真实凭据，也能验证对安全不变量的理解。

## 本文件覆盖范围

- 可执行规格思路
- synthetic fixtures 设计
- path / URL / config / contract / Unicode / env / state-machine 的离线测试主题
- mock / stub 切入点

## 关键结论

- 最有价值的学习方式不是重写整个产品，而是提炼最小安全不变量。
- 离线规格测试能显著降低把 provider、runtime、权限、平台问题混在一起的风险。
- `contract.ts`、`config.ts`、path handling、env projection、state machine 都适合脱离原系统验证。

## 可执行规格的目标

先重建这些最小纯函数或近纯函数：

- `safe_relative_path`
- `sanitize_remote_url`
- `derive_finding_id`
- `verify_cross_document_scan_ids`
- `verify_scope_match`
- `verify_artifact_digest`
- `merge_config` 的关键安全约束
- `serialize_paths` 的 Unicode 边界
- `project_worker_env`
- 取消/恢复状态机 reducer

## 推荐 fixture 分类

### 1. 路径与 scope

示例：

- `src/a.ts` -> accept
- `../secret` -> reject
- `/etc/passwd` -> reject
- `a/../../secret` -> reject
- `C:\outside\secret` -> reject

### 2. Remote URL

示例：

- `https://github.com/org/repo` -> accept
- `https://user:password@github.com/org/repo` -> reject
- `https://github.com/org/repo?token=fake` -> reject
- `https://github.com/org/repo#fragment` -> reject

### 3. Config override

示例：

- `__proto__` -> reject
- `constructor` -> reject
- `prototype` -> reject
- plugin loading override -> reject

### 4. Contract tamper

示例：

- `manifest.scan.id != findings.scanId`
- digest 与实际文件内容不匹配
- seal 时序不一致

### 5. Unicode/path-list

示例：

- 普通中文/日文/韩文文件名
- 含 `U+0085`
- 含 `U+2028`
- 含 `U+2029`

### 6. Provider/env projection

示例：

- 必要 provider key 被保留
- 无关 secret 被剔除
- worker 继承与 coordinator 一致

### 7. 状态机

示例：

- start -> progress -> abort
- start -> worker failure -> finalize
- start -> completion failure -> recovery

## Mock / Stub 的切入点

报告指出可以优先寻找这些 seam：

- `createCodex`
- runtime preparation
- Python resolver
- output preparation
- workbench invocation
- finding matcher
- fake thread / fake worker / fake environment

## 推荐的产出形态

建议在研究目录里保留：

```text
tests/
  contract-spec.pseudo
  path-spec.pseudo
  provider-env-spec.pseudo
  state-machine-spec.pseudo
```

## 方法论任务

1. 为每一类不变量定义 fixture 目录。
2. 先写伪代码规格，再决定是否做真实 harness。
3. 所有 fixture 都使用 synthetic values，不用真实 key 或真实漏洞仓库。
4. 把 mock 面控制在 runtime / worker / env，不要一开始 mock 整个系统。

## 与其他文件的关系

- contract 机制见 [03-finding-semantics-and-contract.md](/home/lqs/codex_security_learing/docs/codex-security/01-source-code-learning/03-finding-semantics-and-contract.md)
- 路径与完整性风险见 [03-path-filesystem-and-artifact-integrity.md](/home/lqs/codex_security_learing/docs/codex-security/02-security-analysis/03-path-filesystem-and-artifact-integrity.md)
