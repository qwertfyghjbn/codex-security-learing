# 子进程、解析器、Unicode 与状态机风险

## 目标

把报告中高噪声但高价值的审计面放到同一文件：进程启动、输入解析边界、编码差异、状态收敛与日志泄露。

## 本文件覆盖范围

- `spawn` / `exec` / `subprocess` 启动点
- JSON/TOML/CSV/PDF/ZIP 等 parser 边界
- Unicode/path-list 序列化与平台编码差异
- cancel / abort / finalize / recovery 状态机
- verbose diagnostics / logs / artifacts

## 关键结论

- 这些问题不一定构成直接漏洞，但经常决定系统是否在真实环境里可靠收敛。
- 对 TypeScript + bundled Python 项目，编码、解析器与状态机问题的价值通常高于盲目上 native reverse engineering。

## 子进程安全面

后续搜索时应统一枚举：

- `spawn`
- `execFile`
- `exec`
- `fork`
- `subprocess.run`
- `subprocess.Popen`
- `os.system`

对每个启动点都记录：

- 是否经过 shell
- 参数如何构造
- env 如何注入
- cwd 如何选择
- stdout/stderr 如何解析

## 解析器边界

报告点名的 parser/serialization 面包括：

- JSON
- TOML
- CSV
- PDF
- ZIP / archive
- URL parsing
- SQLite serialization

对这些面要优先问：

- 路径是否可控
- 内容大小是否受限
- 嵌套/递归是否受限
- 异常是否被吞掉
- 资源耗尽是否可能影响状态机

## Unicode 与平台编码

报告明确指出 path list 序列化对特殊分隔字符有专门处理，尤其要关注：

- U+0085
- U+2028
- U+2029

同时也要关注：

- Windows locale
- UTF-8 / CP932 等平台差异
- subprocess 文本解码

## 状态机风险

建议重点审查：

- start
- progress
- cancel
- cost abort
- worker failure
- finalize
- completion
- history / recovery

核心问题：

- 中断后是否仍显示 RUNNING
- completion 失败后是否能 recover
- cleanup 是否覆盖原始错误

## 日志与敏感信息

应把下面这些资产视为敏感：

- verbose diagnostics
- scan history
- progress/event logs
- findings / manifest / coverage
- 可能包含源码片段或漏洞细节的输出

需要审查：

- 是否落盘过量
- 是否存在脱敏
- 是否把 env / provider / endpoint 线索泄漏到日志

## 审计任务

1. 建子进程启动点清单。
2. 建 parser 风险矩阵。
3. 建 Unicode/平台编码检查表。
4. 画 cancel -> finalize -> complete 的状态收敛图。
5. 盘点日志资产与敏感级别。

## 与其他文件的关系

- runtime / worker 机制见 [04-runtime-sandbox-workbench.md](/home/lqs/codex_security_learing/docs/codex-security/01-source-code-learning/04-runtime-sandbox-workbench.md)
- 搜索策略见 [02-reading-order-and-search-playbook.md](/home/lqs/codex_security_learing/docs/codex-security/03-research-methodology/02-reading-order-and-search-playbook.md)
