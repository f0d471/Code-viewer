# Code Viewer 演进计划

## 背景

code-viewer 和 tutor-skill 是同一框架下的两个 skill：一个看代码，一个教知识。
当前 code-viewer 只完成了 SKILL.md 分发机制的对齐，其余设计均未对齐 tutor-skill。

---

## 需要对齐的设计（按优先级）

### 1. HTML 输出系统 ✅

`tutor-skill/renderers/html-shell.md` → `code-viewer/renderers/html-shell.md`

完成内容：
- CSS 变量体系：4 种语义色（entry 紫、dependency 蓝、warning 红、tip 橙）
- 暗色模式：完整覆盖所有变量
- callout 系统：4 种语义 callout（入口、依赖、警告、提示）
- 深度层级：4 层卡片（L0-L3 对应洋葱模型四层）
- 代码块：highlight.js CDN + 带标注的代码块（code-annotated）
- 模块表格：语义色分行（row-l0 到 row-l3）
- SVG 规范：节点样式用 CSS 变量，自动跟随亮暗模式
- 粘性目录导航
- 响应式 + 打印优化
- 交错动画（尊重 prefers-reduced-motion）

### 2. VSL 设计原则 ✅

`core/vsl-principles.md` 已创建。5 条原则适配代码阅读：
1. 全局优先 → scan 命令的核心
2. 图即内容 → 模块树、调用图、数据流图
3. 关系优先于定义 → 先 grep 谁 import 谁，再读代码
4. 模式 > 列表 → 洋葱模型分层呈现
5. 一次理解 = 永久记忆 → 调用链不跳步

### 3. 硬性规则 + 质量门禁 ✅

`core/rules.md` 已创建：
- 内容规则 10 条（不编造、不跳步、三件事、带行号…）
- 执行规则 3 条（每次一层、暂停指路、每次重读）
- Forbidden 列表 17 条（7 文风 + 4 视觉 + 6 内容）
- Slop Test 3 条
- Quality Self-Check 输出模板

### 4. HTML 模板 ✅

旧的 `templates/*.md` 已删除，新建三个自包含 HTML 模板：
- `scan-report.html` — 全局鸟瞰报告（6 section：概览 → 入口 → 模块表 → 调用图 → 下一步 → 自查）
- `deep-report.html` — 深入分析报告（6 section：职责 → API → 向上 → 向下 → 代码 → 下一步）
- `flow-report.html` — 数据流报告（6 section：描述 → 入口 → 流程图 → 节点详情 → 依赖 → 下一步）

三个模板共享同一套 CSS 设计系统（来自 html-shell.md），各自有独立的 flow/diagram 结构。
命令文件中的模板引用已同步更新为 .html。

### 5. SVG/Mermaid 渲染管线 ✅

- `package.json` + `scripts/render-mermaid.mjs` — 复用 tutor 的渲染管线
- `renderers/callgraph.md` — 重写为 SVG 调用关系图渲染规范（节点样式、箭头、布局规则）
- `renderers/tree.md` — 重写为文件树 + Mermaid 模块树渲染规范（截断规则、渲染流程、分工）

### 6. 分批执行规则 ✅

写入 `core/rules.md` 执行规则 §12-14：
- 单次不超 3 个 section，写完暂停等确认
- 三个报告各有推荐分批方案
- 每次暂停明确告诉用户下一步
- 每个 section 前重新 Read 依赖文件

---

## code-viewer 独有（暂不处理）

- 三种命令的具体流程（scan/deep/flow）
- 洋葱模型分层逻辑
- 入口点速查（entry-points.md）
- 各语言的入口识别规则

---

## 与 tutor-skill 的规则对齐说明

code-viewer 和 tutor-skill 各自有一套 `core/rules.md`，部分内容共享、部分各自适配：

| 规则类型 | 共享 | code-viewer 独有 | tutor-skill 独有 |
|---|---|---|---|
| 内容规则（10 条） | 不编造、不跳步、三件事、带行号、入口先找、不省略工具模块、诚实标记、有依据、截断、写变换 | — | 第 11 条（论点→论据→引用格式） |
| 执行规则 | 每次一层、暂停指路、每次重读 | 分批方案适配 scan/deep/flow | 分批方案适配 chapter/paper |
| Forbidden 文风 | "大致负责"、"结构清晰"、"显然"、煽动开场、无路径、装饰 emoji、用术语解释术语 | — | "不是 X 而是 Y"对偶句式 |
| Forbidden 视觉 | ASCII art、同色 callout、无语言 codeblock、长代码不截断 | — | — |
| Forbidden 内容 | 说逻辑不说具体、不标层级、跳中间层、只给函数名、import 当结论 | deep 报告跳过 WHY | 概念跳步、伪代码替代推导 |
| Slop Test | 删图表后文字独立？同行不觉得是编的？ | scan/deep/flow 各有 1 题 | 能重写推导过程？能画出概念图？ |

**维护注意**：修改规则时先确认是共享规则还是各自独有的，避免改错。共享规则的改动需要同步到两个 skill。

---

## 执行顺序

```
1. HTML 输出系统（renderers/html-shell.md）
      ↓
2. VSL 设计原则（core/vsl-principles.md）
      ↓
3. 硬性规则 + 质量门禁（core/rules.md）
      ↓
4. HTML 模板（templates/*.html）
      ↓
5. SVG/Mermaid 渲染管线
      ↓
6. 分批执行规则（写入 core/rules.md）
      ↓
   之后再处理 code-viewer 独有的命令细节
```
