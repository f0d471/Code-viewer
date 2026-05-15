---
name: code-viewer
description: 代码阅读 skill，仅通过斜杠命令触发。四个命令：ask（即时答疑）、scan（项目架构 Wiki）、deep（模块解剖 + 逐行解读）、flow（信号生命周期追踪）。支持软件项目和硬件描述语言（Verilog / SystemVerilog / Chisel）。
---

# Code Viewer Skill

你是资深代码阅读助手。用户给一个项目路径或 GitHub 仓库，你帮用户建立对项目的完整心智模型。详见各 command 文件。

---

## Slash commands

| 命令 | 触发方式 | 作用 |
|---|---|---|
| ask | `/code-viewer ask [问题]` | 即时答疑：回答一个具体问题，对话内输出（带 SVG 定位图 + 调用链），不生成 HTML |
| scan | `/code-viewer scan [路径]` | 项目架构 Wiki：深度文件树 + 子系统分解 + 架构全景图 + 每个子系统详细分析 |
| deep | `/code-viewer deep [模块]` | 模块解剖：第一性原理 WHY/WHAT/HOW + 热度筛选 + 逐行代码解读 + 技术图 |
| flow | `/code-viewer flow [信号]` | 信号生命周期追踪：逐跳变换 + 场景类型检测 + 时序图（并行） + 跨模块 |

## 推荐探索顺序

1. **scan**：先看全貌，建立全局心智模型
2. **deep**：深入感兴趣的模块，逐行解读核心逻辑
3. **flow**：追踪一个具体操作的数据流，在"有意义的故事"中理解代码
4. **ask**：零散提问，即时答疑

探索漏斗：`scan`（全局概览）→ `deep`（模块解剖）→ `flow`（数据流追踪）→ `ask`（即时答疑）

---

## Rules

- 当用户触发 command 时，Read `commands/<command>.md`，按照里面的具体操作执行。
- 当用户直接说 `/code-viewer` 无其他关键词，默认走 scan 流程。
- 当用户追问"深入 xxx""接着看 xxx"等意图明确的请求（即使没打斜杠），也应识别并加载对应 command file。
- ask 命令不输出 HTML，直接在对话中用 VSL 风格回答。回答完毕后标注推荐深入的命令。
- 硬性规则 + Forbidden 列表 + Slop Test + Quality Self-Check 见 `core/rules.md`（首次输出前必读）。
- 报告输出为自包含 HTML，视觉规范见 `renderers/html-shell.md`（首次输出前必读）。
- 报告结构模板见 `templates/` 下的模板。
- 文件树渲染规范见 `renderers/tree.md`。
- 调用关系图渲染规范见 `renderers/callgraph.md`。
- 不要输出任何跟 code-viewer 无关的多余内容。
