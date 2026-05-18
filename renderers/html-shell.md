# HTML 设计系统

本文件定义 code-viewer 输出的 HTML 报告的全部视觉规范。**首次输出前必读。**

> **CSS 维护**：所有共享样式集中在 `templates/shared-styles.css`。修改颜色、变量、callout 类型等只需改这一个文件。各模板内只保留独有样式（scan=subsystem-card, deep=code-block, flow=timing-diagram）。Agent 输出 HTML 时从 shared-styles.css 注入完整 CSS。

---

## 1. 设计哲学

报告是只读产物——用户阅读，不编辑。所以我们可以用 HTML/CSS 的全部表达力，但要克制：

- **信息密度优先于视觉炫技。** 每一个视觉元素都要承载信息。
- **色彩 = 语义。** 颜色只用来区分代码层级和强调关系，不用来装饰。
- **SVG 是一等公民。** 所有图表（调用关系图、模块树、数据流图）都用内联 SVG，不用 ASCII art。
- **自包含。** 除 highlight.js CDN 外零外部依赖。CSS 在 `<style>` 里，SVG 在 `<body>` 里。

---

## 2. CSS 变量（必须使用）

所有颜色、间距、字体都通过 CSS 变量定义。agent 填充内容时直接使用变量名，不要硬编码颜色值。

```css
:root {
  /* 文字 */
  --text-primary: #1a1a2e;
  --text-secondary: #4a4a6a;
  --text-muted: #8888a0;

  /* 背景 */
  --bg-page: #fafaf8;
  --bg-card: #ffffff;
  --bg-code: #f4f4f0;

  /* 语义色——代码层级 */
  --color-entry: #7c3aed;           /* 入口/核心骨架 */
  --color-entry-bg: #f5f3ff;
  --color-core: #7c3aed;            /* 核心业务逻辑 */
  --color-core-bg: #f5f3ff;
  --color-dependency: #2563eb;      /* 依赖/外部调用 */
  --color-dependency-bg: #eff6ff;
  --color-warning: #dc2626;         /* 警告/待深入 */
  --color-warning-bg: #fef2f2;
  --color-success: #059669;         /* 已分析/已确认 */
  --color-success-bg: #ecfdf5;
  --color-tip: #d97706;             /* 提示/推荐下一步 */
  --color-tip-bg: #fffbeb;

  /* 强调 */
  --color-accent: #7c3aed;
  --color-highlight: #fbbf24;

  /* 间距 */
  --space-xs: 0.25rem;
  --space-sm: 0.5rem;
  --space-md: 1rem;
  --space-lg: 1.5rem;
  --space-xl: 2.5rem;
  --space-2xl: 4rem;

  /* 字体 */
  --font-body: "Noto Sans SC", "Source Han Sans SC", system-ui, sans-serif;
  --font-heading: "Noto Sans SC", "Source Han Sans SC", system-ui, sans-serif;
  --font-mono: "JetBrains Mono", "Fira Code", "Source Code Pro", monospace;

  /* 尺寸 */
  --content-width: 56rem;
  --sidebar-width: 14rem;

  /* SVG */
  --svg-node-fill: #e8e8f0;
  --svg-node-stroke: #4a4a6a;
  --svg-arrow: #4a4a6a;
  --svg-entry: #7c3aed;
  --svg-entry-fill: #ede9fe;
  --svg-dependency: #2563eb;
  --svg-dependency-fill: #dbeafe;
}
```

### 暗色模式

```css
@media (prefers-color-scheme: dark) {
  :root {
    --text-primary: #e8e8f0;
    --text-secondary: #a0a0b8;
    --text-muted: #6a6a80;
    --bg-page: #0f0f1a;
    --bg-card: #1a1a2e;
    --bg-code: #16162a;
    --color-entry: #a78bfa;
    --color-entry-bg: #1e1340;
    --color-core: #a78bfa;
    --color-core-bg: #1e1340;
    --color-dependency: #60a5fa;
    --color-dependency-bg: #0a1a30;
    --color-warning: #f87171;
    --color-warning-bg: #1a0a0a;
    --color-success: #34d399;
    --color-success-bg: #0a2018;
    --color-tip: #fbbf24;
    --color-tip-bg: #1a1500;
    --svg-node-fill: #1e1e3a;
    --svg-node-stroke: #a0a0b8;
    --svg-arrow: #a0a0b8;
    --svg-entry-fill: #2e1e5a;
    --svg-dependency-fill: #0e1e3a;
  }
}
```

---

## 3. 排版规范

### 3.0 CSS Reset（必须）

所有报告必须在 CSS 最前面包含全局重置，否则 box-sizing 会导致间距和宽度计算错误：

```css
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
```

### 3.1 字体加载（必须）

**在 `<head>` 中加载 Google Fonts**，不能只依赖 CSS font-family fallback。Windows 上 Noto Sans SC 和 JetBrains Mono 通常未安装，不加载会导致 fallback 到极细的系统字体。

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Noto+Sans+SC:wght@400;500;700&family=JetBrains+Mono:wght@400;700&display=swap" rel="stylesheet">
```

`<style>` 内也加 `@import` 作为 fallback（见 shared-styles.css 顶部）。

### 3.2 排版

---

## 4. 代码块

代码阅读报告的核心内容。用 highlight.js 做语法高亮（唯一允许的外部依赖，等价于 tutor 的 KaTeX）。

### 4.1 CDN 引入

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/highlight.js@11.9.0/styles/github.min.css">
<script defer src="https://cdn.jsdelivr.net/npm/highlight.js@11.9.0/lib/core.min.js"></script>
<script defer src="https://cdn.jsdelivr.net/npm/highlight.js@11.9.0/lib/languages/python.min.js"
  onload="hljs.highlightAll();"></script>
```

按目标项目的语言动态加载对应语言包。常见：`python.min.js`、`javascript.min.js`、`java.min.js`、`go.min.js`、`rust.min.js`、`c.min.js`、`verilog.min.js`。

暗色模式自动跟随（highlight.js 的 github 样式 + CSS 变量覆盖）：

```css
@media (prefers-color-scheme: dark) {
  .hljs { background: var(--bg-code) !important; color: var(--text-primary) !important; }
}
```

### 4.2 代码块样式

```css
pre {
  background: var(--bg-code);
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  padding: var(--space-md) var(--space-lg);
  overflow-x: auto;
  font-family: var(--font-mono);
  font-size: 0.9rem;
  line-height: 1.6;
  margin: var(--space-lg) 0;
}

pre code { background: none; padding: 0; border: none; }

/* 行内代码 */
code {
  font-family: var(--font-mono);
  font-size: 0.9em;
  background: var(--bg-code);
  padding: 0.15em 0.4em;
  border-radius: 4px;
}
```

### 4.3 带标注的代码块

关键代码片段带行号标注和高亮标记（入口行、关键调用行）：

```html
<div class="code-annotated">
  <div class="code-label">src/main.py — 入口文件</div>
  <pre><code class="language-python">def main():           # ← 入口
    config = load()     # ← 第一层：配置
    db = connect()      # ← 第一层：数据库
    server = create()   # ← 第一层：服务器
    server.listen()     # ← 启动</code></pre>
  <div class="code-note">高亮行 = 从入口直接调用的第一层模块</div>
</div>
```

```css
.code-annotated {
  margin: var(--space-lg) 0;
}
.code-label {
  font-family: var(--font-heading);
  font-weight: 700;
  font-size: 0.85rem;
  color: var(--color-entry);
  margin-bottom: var(--space-sm);
  padding: var(--space-xs) var(--space-sm);
  background: var(--color-entry-bg);
  border-radius: 4px 4px 0 0;
  display: inline-block;
}
.code-note {
  font-size: 0.85rem;
  color: var(--text-muted);
  margin-top: var(--space-sm);
}
```

---

## 5. Callout 系统（四种语义色）

### 5.1 入口/核心骨架（紫色）

```html
<div class="callout callout-entry">
  <div class="callout-title">入口</div>
  <div class="callout-body">
    <p>这是整个项目的第 0 层——最小核心骨架。</p>
  </div>
</div>
```

### 5.2 依赖/外部调用（蓝色）

```html
<div class="callout callout-dependency">
  <div class="callout-title">外部依赖</div>
  <div class="callout-body">
    <p>这个模块依赖了数据库连接池和 Redis 缓存。</p>
  </div>
</div>
```

### 5.3 警告/待深入（红色）

```html
<div class="callout callout-warning">
  <div class="callout-title">待深入</div>
  <div class="callout-body">
    <p>这个模块的内部逻辑尚未展开，可以用 <code>/code-viewer deep xxx</code> 深入。</p>
  </div>
</div>
```

### 5.4 提示/推荐（橙色）

```html
<div class="callout callout-tip">
  <div class="callout-title">推荐下一步</div>
  <div class="callout-body">
    <p>最值得先深入的模块是 <code>router.py</code>，因为它是数据流入的第一站。</p>
  </div>
</div>
```

### Callout CSS

```css
.callout {
  border-left: 4px solid;
  border-radius: 0 8px 8px 0;
  padding: var(--space-md) var(--space-lg);
  margin: var(--space-lg) 0;
}
.callout-title {
  font-family: var(--font-heading);
  font-weight: 700;
  font-size: 0.9rem;
  margin-bottom: var(--space-sm);
}
.callout-entry { border-color: var(--color-entry); background: var(--color-entry-bg); }
.callout-entry .callout-title { color: var(--color-entry); }

.callout-dependency { border-color: var(--color-dependency); background: var(--color-dependency-bg); }
.callout-dependency .callout-title { color: var(--color-dependency); }

.callout-warning { border-color: var(--color-warning); background: var(--color-warning-bg); }
.callout-warning .callout-title { color: var(--color-warning); }

.callout-tip { border-color: var(--color-tip); background: var(--color-tip-bg); }
.callout-tip .callout-title { color: var(--color-tip); }
```

---

## 6. 深度层级（洋葱模型）

用 CSS 卡片区分洋葱模型的各层：

```css
/* L0 — 入口/核心骨架 */
.depth-l0 {
  background: var(--bg-card);
  border: 2px solid var(--color-entry);
  border-radius: 10px;
  padding: var(--space-lg);
  box-shadow: 0 4px 16px rgba(124, 58, 237, 0.10), 0 1px 4px rgba(0,0,0,0.06);
}

/* L1 — 核心数据流 */
.depth-l1 {
  background: var(--bg-card);
  border: 1px solid var(--color-dependency);
  border-radius: 8px;
  padding: var(--space-md) var(--space-lg);
  box-shadow: 0 2px 8px rgba(37, 99, 235, 0.06);
}

/* L2 — 业务逻辑模块 */
.depth-l2 {
  background: var(--bg-card);
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  padding: var(--space-md) var(--space-lg);
  box-shadow: 0 2px 8px rgba(0,0,0,0.06);
}

/* L3 — 工具层 */
.depth-l3 {
  background: var(--bg-code);
  border: 1px solid #e8e8e8;
  border-radius: 6px;
  padding: var(--space-md) var(--space-lg);
  box-shadow: inset 0 1px 3px rgba(0,0,0,0.04);
}
```

**用法**：
- `depth-l0`：入口文件卡片、最小核心骨架展示
- `depth-l1`：第一层模块卡片（从入口直接调用的模块）
- `depth-l2`：第二层业务逻辑模块卡片
- `depth-l3`：工具函数、配置等底层模块

### 6.1 层级标签（Layer Badge）

scan 报告中用于标记子系统的层级：

```html
<span class="layer-badge layer-l0">L0</span>
<span class="layer-badge layer-l1">L1</span>
<span class="layer-badge layer-l2">L2</span>
<span class="layer-badge layer-l3">L3</span>
```

```css
.layer-badge {
  display: inline-block;
  font-family: var(--font-heading);
  font-size: 0.7rem;
  font-weight: 700;
  padding: 0.15em 0.6em;
  border-radius: 4px;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  vertical-align: middle;
  margin-right: 0.5em;
}
.layer-l0 { background: var(--color-entry-bg); color: var(--color-entry); border: 1px solid var(--color-entry); }
.layer-l1 { background: var(--color-dependency-bg); color: var(--color-dependency); border: 1px solid var(--color-dependency); }
.layer-l2 { background: var(--color-tip-bg); color: var(--color-tip); border: 1px solid var(--color-tip); }
.layer-l3 { background: var(--bg-code); color: var(--text-muted); border: 1px solid var(--color-card-border); }
```

### 6.2 子系统卡片

scan 报告中每个子系统的概要信息卡片：

```html
<div class="subsystem-card">
  <div class="subsystem-header">
    <span class="layer-badge layer-l0">L0</span>
    <h3>子系统名称</h3>
  </div>
  <p><strong>职责</strong>：xxx</p>
</div>
```

```css
.subsystem-card {
  background: var(--bg-card);
  border: 1px solid var(--color-card-border);
  border-radius: var(--border-radius);
  padding: var(--space-lg);
  margin: var(--space-lg) 0;
  box-shadow: 0 2px 8px var(--color-card-shadow);
  transition: box-shadow 0.2s;
}
.subsystem-card:hover { box-shadow: 0 4px 16px var(--color-card-hover-shadow); }
.subsystem-card h3 { margin-top: 0; }
.subsystem-header {
  display: flex;
  align-items: center;
  gap: var(--space-sm);
  margin-bottom: var(--space-md);
}
.subsystem-header h3 { margin: 0; flex: 1; }
```

---

## 7. 模块表格

scan 报告的核心组件——模块列表。用语义色区分行：

```css
table {
  width: 100%;
  border-collapse: collapse;
  margin: var(--space-lg) 0;
  font-size: 0.95rem;
}
th {
  font-family: var(--font-heading);
  font-weight: 700;
  text-align: left;
  padding: var(--space-sm) var(--space-md);
  border-bottom: 2px solid var(--color-entry);
  color: var(--color-entry);
  font-size: 0.85rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}
td {
  padding: var(--space-sm) var(--space-md);
  border-bottom: 1px solid #e8e8e8;
  vertical-align: top;
}
tr:hover { background: var(--color-entry-bg); }

/* 层级行着色 */
tr.row-l0 { border-left: 3px solid var(--color-entry); }
tr.row-l1 { border-left: 3px solid var(--color-dependency); }
tr.row-l2 { border-left: 3px solid var(--text-muted); }
tr.row-l3 { border-left: 3px solid #d0d0d0; }
```

---

## 8. SVG 规范

所有图表用内联 SVG。详见 `renderers/callgraph.md`。

**核心原则：文字不叠在图形上。** rect 只做色块标识，模块名放在 rect 下方，连线标注带背景衬底。

简要约定：
- 入口节点：`fill="var(--svg-entry-fill)" stroke="var(--svg-entry)" stroke-width="2.5" rx="6"`
- 普通模块节点：`fill="var(--svg-node-fill)" stroke="var(--svg-node-stroke)" stroke-width="1.5" rx="6"`
- 依赖节点：`fill="var(--svg-dependency-fill)" stroke="var(--svg-dependency)" stroke-width="1.5" rx="6"`
- 节点名称：`<text>` 放在 rect 正下方（`y = rect.y + rect.height + 16`），`text-anchor="middle"` 居中
- 连线标注：`<rect fill="var(--bg-page)">` 衬底 + `<text>` 放在线侧面，不压线
- 同层节点间距 ≥ 40px（水平），≥ 60px（垂直）

SVG 的 fill/stroke 引用 CSS 变量，自动跟随亮色/暗色模式。

---

## 9. 导航（sticky 目录）

报告长时需要导航。用 CSS sticky 实现页内目录：

```html
<nav class="toc" aria-label="目录">
  <div class="toc-title">报告目录</div>
  <a href="#overview">项目概览</a>
  <a href="#entry">最小核心骨架</a>
  <a href="#modules">模块列表</a>
  <a href="#callgraph">调用关系图</a>
  <a href="#next">推荐下一步</a>
</nav>
```

```css
.toc {
  position: sticky;
  top: var(--space-lg);
  float: right;
  width: var(--sidebar-width);
  margin-left: var(--space-lg);
  padding: var(--space-md);
  background: var(--bg-card);
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  font-family: var(--font-heading);
  font-size: 0.85rem;
  line-height: 2;
}
.toc-title {
  font-weight: 700;
  margin-bottom: var(--space-sm);
  color: var(--text-secondary);
  font-size: 0.8rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}
.toc a {
  display: block;
  color: var(--text-secondary);
  text-decoration: none;
}
.toc a:hover { color: var(--color-accent); }

@media (max-width: 72rem) {
  .toc { display: none; }
}
```

---

## 10. 响应式

```css
@media (max-width: 48rem) {
  body { padding: var(--space-md); }
  h1 { font-size: 1.5rem; }
  h2 { font-size: 1.25rem; }
  svg { max-width: 100%; height: auto; }
  pre { font-size: 0.8rem; }
}
```

SVG 都设 `width="100%" height="auto"` 或写 `preserveAspectRatio="xMidYMid meet"`。

---

## 11. 打印优化

```css
@media print {
  .toc { display: none; }
  body { max-width: 100%; padding: 1cm; }
  .callout { break-inside: avoid; }
  pre { white-space: pre-wrap; word-break: break-all; }
}
```

---

## 12. 交错动画（逐步展开）

用 CSS `animation-delay` + `--i` 变量实现列表项/模块卡片依次出现：

```css
@keyframes fadeUp {
  from { opacity: 0; transform: translateY(12px); }
  to   { opacity: 1; transform: translateY(0); }
}
.stagger > * {
  animation: fadeUp 0.4s ease both;
  animation-delay: calc(var(--i, 0) * 80ms);
}
```

**用法**：在容器上加 `class="stagger"`，每个子元素设 `style="--i: 0"` 等。

```html
<ul class="stagger">
  <li style="--i: 0">moduleA — 路由注册</li>
  <li style="--i: 1">moduleB — 数据库连接</li>
  <li style="--i: 2">moduleC — 服务器启动</li>
</ul>
```

**注意事项**：
- 尊重 `prefers-reduced-motion`：加 `@media (prefers-reduced-motion: reduce) { .stagger > * { animation: none; } }`
- 只对模块列表、调用链步骤使用，不对代码块加动画
- 延迟间隔 80ms
