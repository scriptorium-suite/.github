# Scriptorium

> **研究上下文，不随一次对话消失。**

Scriptorium 是面向 AI4Science 科研工作者的本地优先、Agent 原生工作流套件。
它通过开放的 Markdown/JSON 文件契约，把文献治理、项目记忆、Agent 协作和可选的
汇报输出连接起来；各组件既能独立使用，也能由统一入口协同运行。

**Public Alpha v0.1.0 · Windows-first · 源码安装 · 全合成、无凭据 demo**

[开始使用](https://github.com/scriptorium-suite/scriptorium#source-quickstart-on-windows) ·
[中文产品案例](https://github.com/scriptorium-suite/scriptorium/blob/main/docs/case-study.zh-CN.md) ·
[可复现证据](https://github.com/scriptorium-suite/scriptorium/blob/main/docs/showcase/README.zh-CN.md) ·
[设计借鉴与致谢](https://github.com/scriptorium-suite/scriptorium/blob/main/ACKNOWLEDGEMENTS.zh.md)

## 现在能验证什么

旗舰仓库提供一条使用真实公共接口、但只包含合成数据的纵向链路：

- 3 条合成文献记录，2 条命中研究边界，1 条相似主题负例被排除；
- Spec、Steward 与 Provenance 依次完成契约校验、文献综述、项目摄取、FTS5 检索与只读 MCP 验证；
- 9 个跨组件阶段退出码均为 0，6 项机器断言通过，生成 9 个可检查产物；
- Windows / Ubuntu、Python 3.11 / 3.12 CI 验证，另有隔离 Windows 源码安装验收；
- demo 不请求 API Key，不读取真实 Zotero、Agent 对话或私人研究资料，也不调用在线模型。

这组证据证明的是公共接口和契约链路可复现，不代表真实科学结论，也不等同于完整科研自动化。

## 套件组成

| 项目 | 独立职责 | 当前边界 |
|---|---|---|
| [**Scriptorium**](https://github.com/scriptorium-suite/scriptorium) | 统一安装诊断、项目初始化、安全盘点、按需同步与合成验收入口 | Public Alpha v0.1.0 |
| [**scriptorium-spec**](https://github.com/scriptorium-suite/scriptorium-spec) | 十种版本化 JSON Schema 与跨组件约定的单一事实源 | v2.2.0 发布目标 |
| [**Steward**](https://github.com/scriptorium-suite/steward) | 文献治理、综述组装与项目组合；可独立部署 | v0.2.0 发布目标，可选组件 |
| [**Provenance**](https://github.com/foxsplendid/Provenance) | 本地项目记忆、全文索引、审批写回与只读 MCP | v0.17.0 发布目标，核心运行组件 |
| [**Lectern**](https://github.com/foxsplendid/Academic-Slides-Agent) | 从结构化证据生成原生可编辑幻灯片 | 独立预发布产品，尚未进入无凭据黄金路径 |

组件不导入彼此的内部模块；它们只交换由 `scriptorium-spec` 定义的版本化文件。
Obsidian 与 Zotero 是可选适配器，不是数据所有权前提。

## 隐私与信任边界

- 用户选择的本地 Markdown、PDF、项目目录和运行数据仍由用户持有；套件不建立新的云端主库。
- 核心路径无遥测；配置在线模型或第三方 provider 时，数据出站由对应 host/provider 决定。
- 写操作采用预览与显式执行分离；高价值声明先进入候选/审批区，不能由 Agent 自动晋升为事实。
- 旗舰合成 demo 及本次证据包只使用合成 fixture。我们不把“本地优先”夸大为“任何配置下都完全离线”。

## 尚未完成

- `inventory` 当前只做不读取正文的安全盘点与路由，实际迁移仍需受审阅的 manifest/apply 流程；
- Claude Code `SessionEnd` 实时对等路径与 Lectern 跨仓 E2E 仍待验证；
- 当前以 GitHub 源码安装为主，尚无 PyPI、winget 或图形安装器；
- 尚需外部用户 Alpha 来验证首次成功时间、上下文恢复可接受率和审批负担。

---

## English

**Scriptorium keeps research context alive across agent sessions.** It is a
local-first, agent-native workflow suite that connects literature governance,
project memory, agent collaboration, and optional slide delivery through open,
versioned Markdown/JSON contracts.

The Public Alpha uses real public interfaces with synthetic data: three literature
records, nine cross-component stages, six machine-readable assertions, nine output
artifacts, local FTS5 search, and read-only MCP verification. It requests no
credentials and makes no live-model call. This proves a reproducible integration
path—not scientific results or end-to-end research automation.

[Flagship repository](https://github.com/scriptorium-suite/scriptorium) ·
[Chinese product case study](https://github.com/scriptorium-suite/scriptorium/blob/main/docs/case-study.zh-CN.md) ·
[Contract source of truth](https://github.com/scriptorium-suite/scriptorium-spec)

Apache-2.0. No telemetry in the suite-controlled core. Optional model providers
have their own egress boundaries.
