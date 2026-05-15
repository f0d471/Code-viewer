# Code Viewer

面向视觉-空间型学习者（VSL）的代码阅读 skill。看到代码就头晕？让 Claude 帮你建立完整的心智模型。

## 它是什么

Code Viewer 是一个 [Claude Code](https://claude.com/code) skill，把"读代码"这件事拆解成四个递进的命令：

```
ask（即时答疑）→ flow（数据流追踪）→ scan（全局概览）→ deep（模块解剖）
```

不是让 AI 读完代码然后给你一份摘要——而是帮你从一个**具体问题**出发，逐步建立对整个项目的理解。

## 四个命令

### `/code-viewer ask [问题]`

> "这个函数为什么要这么写？""这个变量从哪来的？""这两个模块为什么要分开？"

即时回答一个具体问题。不生成 HTML 报告，直接在对话中输出：

- 一句话结论
- SVG 定位图（目标在调用链中的位置）
- 关键代码引用（带文件路径和行号）
- 调用链：谁 → 我 → 谁
- 推荐深入的命令

```
/code-viewer ask main 函数里 config.load() 做了什么
```

### `/code-viewer flow [信号]`

> "跟着一个操作走一遍"

追踪一条数据流/信号的完整生命周期——从触发到终结，每一步变换不跳步。

自动检测场景类型（硬件/软件后端/前端/系统），切换对应的术语体系和图形表达。

输出自包含 HTML：
- 信号定义（起点、终点、时间尺度）
- 水平流程图
- 时序图（并行结构用泳道表示）
- 每个节点的输入 → 变换 → 输出

```
/code-viewer flow 用户登录
/code-viewer flow AXI4 读事务
/code-viewer flow HTTP 请求处理
```

### `/code-viewer scan [路径]`

> "给我一张完整的项目地图"

生成一份项目架构 Wiki。不是快速鸟瞰，而是帮你建立完整的心智模型。

输出自包含 HTML：
- 深度文件树（关键目录展开到底）
- 子系统分解 + 依赖关系图
- 架构全景图（SVG）
- 推荐阅读顺序（基于洋葱模型 L0→L3）
- 每个子系统的详细分析（默认折叠，点击展开）

```
/code-viewer scan ./my-project
/code-viewer scan https://github.com/user/repo
```

### `/code-viewer deep [模块]`

> "把这个模块从头到尾解剖一遍"

两阶段分析：

**Phase 1 — 模块解剖（第一性原理）**
- WHY：为什么存在？解决什么需求？
- WHAT：功能是什么？公开接口有哪些？
- HOW：核心怎么实现？关键技术决策是什么？
- 依赖：完全展开（不只一层）

**Phase 2 — 逐行解读（热度筛选 + 核心逻辑）**
- 先识别哪些代码是核心逻辑，哪些是胶水/配置
- 只对核心逻辑做完整的 WHY/WHAT/HOW 三问
- 每个文件顶部有面包屑调用链，提醒你在调用链中的位置

质量标准：读完 Phase 1，能回答这个模块的一切；读完 Phase 2，能从零重写核心逻辑。

```
/code-viewer deep src/handler.py
/code-viewer deep npc/vsrc/alu.v
```

## 推荐探索顺序

对"看到代码就头晕"的人来说，最高效的模式是**自顶向下 + 目标驱动**：

| 步骤 | 命令 | 你获得什么 |
|------|------|-----------|
| 1 | `ask` | 一个具体问题的答案，建立初步兴趣 |
| 2 | `flow` | 一个"有意义的故事"，理解数据怎么流 |
| 3 | `scan` | 全局地图，知道项目分几层、每个子系统干什么 |
| 4 | `deep` | 核心模块的逐行解读，能从零重写 |

核心理念：不要从"一棵抽象的文件树"开始，要从"一个有意义的故事"开始。

## 安装

```bash
# 全局安装（所有项目可用）
git clone https://github.com/f0d471/Code-viewer.git ~/.claude/skills/code-viewer
```

Windows：
```powershell
git clone https://github.com/f0d471/Code-viewer.git "$env:USERPROFILE\.claude\skills\code-viewer"
```

安装后重启 Claude Code（或 `/reload`），四个斜杠命令即可使用。

## 特性

- **自包含 HTML 报告**：零外部依赖（除 highlight.js CDN），CSS 变量支持亮色/暗色模式
- **渐进式揭示**：scan 报告的子系统详情默认折叠，按需展开
- **面包屑调用链**：deep 报告中始终显示你在调用链的位置
- **热度筛选**：deep Phase 2 只对核心逻辑做完整解读，胶水代码标注"略"
- **场景类型检测**：flow 自动识别硬件/软件/前端/系统，切换术语
- **VSL 设计原则**：图即内容、关系优先于定义、一次理解 = 永久记忆
- **支持硬件描述语言**：Verilog / SystemVerilog / Chisel，用时序图表示并行

## 项目结构

```
code-viewer/
├── SKILL.md                    # Skill 入口 + 命令定义
├── commands/
│   ├── ask.md                  # 即时答疑流程
│   ├── flow.md                 # 信号生命周期追踪流程
│   ├── scan.md                 # 项目架构 Wiki 流程
│   └── deep.md                 # 模块解剖流程（两阶段）
├── core/
│   ├── rules.md                # 硬性规则 + Forbidden + 质量门禁
│   ├── vsl-principles.md       # VSL 设计原则（5 条）
│   ├── onion-model.md          # 洋葱模型分层策略
│   └── entry-points.md         # 各语言入口识别规则
├── renderers/
│   ├── html-shell.md           # HTML 设计系统规范
│   ├── callgraph.md            # SVG 调用关系图规范
│   └── tree.md                 # 文件树渲染规范
├── templates/
│   ├── shared-styles.css       # 共享 CSS（变量、callout、排版）
│   ├── scan-report.html        # scan 报告模板
│   ├── deep-report.html        # deep 报告模板
│   └── flow-report.html        # flow 报告模板
├── scripts/
│   └── render-mermaid.mjs      # Mermaid → SVG 渲染脚本
└── evolution.md                # 演进计划
```

## 设计理念

> "看到代码就头晕目眩，必须要讲解才看得懂"

Code Viewer 基于 Linda Silverman 的视觉-空间型学习（VSL）理论：

1. **全局优先** — 先看整张地图，再走某条路
2. **图即内容** — 每个模块关系、调用链、数据流都有 SVG
3. **关系优先于定义** — 先看谁调用谁，再看函数签名
4. **模式 > 列表** — 结构化视觉分组比平铺清单更易记
5. **一次理解 = 永久记忆** — 调用链不跳步，在脑中建成完整画面

## License

MIT
