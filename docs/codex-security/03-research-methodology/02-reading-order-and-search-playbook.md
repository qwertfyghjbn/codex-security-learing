# 阅读顺序与搜索作战手册

## 目标

把原始报告中的阅读顺序、源码地图和检索命令整理成可复用的研究手册。

## 本文件覆盖范围

- P0 / P1 / P2 阅读优先级
- 源码地图
- `rg` 搜索命令
- 推荐正则模板

## 关键结论

- 先建立层和主链路，再找细节。
- 在本项目里，`SECURITY.md`、`api.ts`、`config.ts`、`contract.ts`、核心 skills 是 P0。
- 文本搜索优先使用 `rg`，不要一开始就放大到依赖和生成物。

## 阅读优先级

### P0

- `SECURITY.md`
- `src/api.ts`
- `src/config.ts`
- `src/contract.ts`
- `skills/security-scan`
- `skills/validation`
- `skills/threat-model`

### P1

- `runtime.ts`
- `targets.ts`
- `auth.ts`
- `knowledge-base.ts`
- bundled `scripts/`
- schemas
- Docker / Compose

### P2

- tests
- logs/history/cost/multiscan/export
- 社区 issue 与假设池

## 源码地图

推荐先按这些层建目录脑图：

| 层 | 优先位置 | 关注点 |
|---|---|---|
| CLI/API 入口 | `src/cli.ts`, `src/api.ts` | 生命周期、参数、事件 |
| Target | `src/targets.ts` | scope/path 归一化 |
| Config/provider | `src/config.ts` | override merge、provider、profile |
| Auth | `src/auth.ts` | credential discovery |
| Runtime | `src/runtime.ts` | executable/python/plugin discovery |
| KB/input | `src/knowledge-base.ts` | 外部文档准备 |
| 策略/技能 | `_bundled_plugin/skills/*` | 分析策略、验证策略 |
| Workbench | `_bundled_plugin/scripts/*.py` | worker/finalize/report |
| Contract | `src/contract.ts` | schema、path、seal、digest |
| 状态/审计 | `scan-logs.ts`, `cost.ts` 等 | history、progress、recovery |
| Tests | `tests-ts/*` | 隐含设计契约 |

## 推荐搜索命令

```bash
rg -n --hidden -g '!node_modules' \
  'CodexSecurity|run\(|preflight\(|startThread|approvalPolicy|default_permissions|codex_security_scan' \
  sdk/typescript

rg -n --hidden -g '!node_modules' \
  '(spawn|execFile|exec|fork|subprocess\.(run|Popen)|os\.system)\s*\(' \
  sdk docker

rg -n --hidden -g '!node_modules' \
  '(realpath|lstat|readlink|symlink|resolve|relative|isAbsolute|open\(|writeFile|rename|chmod|extract|archive)' \
  sdk docker

rg -n --hidden -g '!node_modules' \
  '(JSON\.(parse|stringify)|Ajv|schema|parseToml|stringifyToml|csv|yaml|toml|deserialize|serialize|pickle|marshal|pdf)' \
  sdk

rg -n --hidden -g '!node_modules' \
  '(API_KEY|TOKEN|SECRET|credential|auth|provider|process\.env|os\.environ|CODEX_HOME|GITHUB_TOKEN|AWS_)' \
  sdk docker
```

## 推荐正则模板

```regex
(?:spawn|execFile|exec|fork|subprocess\.(?:run|Popen)|os\.system)\s*\(
(?:realpath|lstat|readlink|symlink|resolve|relative|isAbsolute|open|writeFile|rename|chmod)\s*\(
(?:API_KEY|TOKEN|SECRET|PASSWORD|credential|auth|process\.env|os\.environ)
(?:JSON\.(?:parse|stringify)|Ajv|schema|parseToml|pickle|marshal|yaml|csv|pdf|zip)
(?:approvalPolicy|default_permissions|sandbox|permission|policy|allow|deny|scope|trusted|untrusted)
(?:scanId|threadId|targetId|event|history|receipt|manifest|coverage|sealedAt|sha256)
```

## 阅读纪律

- 优先 `--hidden`，不要一上来 `-uuu`
- 先聚焦产品源码，再决定是否拉依赖或生成物
- 每找到一个入口函数，就补一张调用卡

## 调用卡模板

```text
Function:
Caller:
Input types:
Preconditions:
Normalization:
Security checks:
Side effects:
External process/API:
Files read:
Files written:
Environment inherited:
Errors:
Abort/cancel behavior:
Retry/recovery behavior:
finally/cleanup:
Returned trust level:
Tests covering it:
Unanswered questions:
```

## 与其他文件的关系

- 研究边界见 [01-scope-version-and-boundaries.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/01-scope-version-and-boundaries.md)
- 离线规格设计见 [04-offline-validation-and-fixtures.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/04-offline-validation-and-fixtures.md)
