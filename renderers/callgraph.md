# 调用关系图 — SVG 渲染规范

所有调用关系图用内联 SVG，嵌入 HTML 报告。

---

## 核心原则：文字不叠在图形上

**禁止**：把模块名、描述文字写在 `<rect>` 内部或紧贴矩形边框上。

**正确做法**：
- 模块名放在矩形**下方**（rect 只做色块标识，文字在 rect 外）
- 连线标注放在连线**侧面**（用背景 `<rect>` 衬底），不压在线上
- 节点间留足间距，文字不与其他节点重叠

```
错误（文字叠在图形上）：          正确（文字在图形外）：

 ┌─────────────┐                ┌─────────────┐
 │  main.cpp   │  ← 不要这样    │             │  ← rect 只做色块
 └─────────────┘                └─────────────┘
                                   main.cpp     ← 名称在下方
```

---

## 节点样式

| 类型 | fill | stroke | stroke-width | rx |
|---|---|---|---|---|
| 入口节点 | `var(--svg-entry-fill)` | `var(--svg-entry)` | 2.5 | 6 |
| 普通模块 | `var(--svg-node-fill)` | `var(--svg-node-stroke)` | 1.5 | 6 |
| 依赖/外部 | `var(--svg-dependency-fill)` | `var(--svg-dependency)` | 1.5 | 6 |
| 待深入节点 | `var(--color-warning-bg)` | `var(--color-warning)` | 1.5 | 6 |

fill/stroke 引用 CSS 变量，自动跟随亮色/暗色模式。

**rect 尺寸规范**：
- 最小宽度：120px
- 最小高度：40px
- rect 下方预留 16px 给文字
- 同层节点间距 ≥ 40px（水平）/ ≥ 60px（垂直）

---

## 文字位置规范

### 节点名称

```html
<!-- rect 做色块标识 -->
<rect x="100" y="40" width="140" height="40" rx="6"
      fill="var(--svg-entry-fill)" stroke="var(--svg-entry)" stroke-width="2"/>
<!-- 名称放在 rect 正下方，居中 -->
<text x="170" y="96" text-anchor="middle"
      font-weight="700" font-size="13" fill="var(--text-primary)">main.cpp</text>
```

### 节点描述（可选，放名称下方）

```html
<text x="170" y="112" text-anchor="middle"
      font-size="10" fill="var(--text-secondary)">Verilator 驱动入口</text>
```

### 连线标注

连线标注必须用**背景衬底**，不直接写在连线上：

```html
<!-- 连线 -->
<line x1="240" y1="60" x2="360" y2="60"
      stroke="var(--svg-arrow)" stroke-width="1.5" marker-end="url(#arrow)"/>
<!-- 标注：在线上方，带背景 rect 衬底 -->
<rect x="270" y="44" width="60" height="18" rx="3" fill="var(--bg-page)"/>
<text x="300" y="57" text-anchor="middle"
      font-size="10" fill="var(--text-muted)">DPI-C</text>
```

### 层级标签（如 L0/L1/L2）

放在该层最左侧，不与节点重叠：

```html
<text x="10" y="60" font-size="12" font-weight="700" fill="var(--color-entry)">L0 — 硬件核心</text>
```

---

## 箭头定义（每个 SVG 内复制一次）

```html
<defs>
  <marker id="arrow" viewBox="0 0 10 7" refX="10" refY="3.5"
    markerWidth="8" markerHeight="6" orient="auto-start-reverse">
    <path d="M 0 0 L 10 3.5 L 0 7 z" fill="var(--svg-arrow)"/>
  </marker>
</defs>
```

---

## 常见图类型

### 纵向调用树（scan 报告 / deep 报告）

适合展示从入口到各层模块的调用关系。从上到下布局。

SVG 示例骨架：

```html
<svg viewBox="0 0 800 600" xmlns="http://www.w3.org/2000/svg"
     style="font-family: 'Noto Sans SC', system-ui, sans-serif;">
  <defs><!-- 箭头 --></defs>

  <!-- L0 层级标签 -->
  <text x="10" y="60" font-size="12" font-weight="700" fill="var(--color-entry)">L0 — 入口</text>

  <!-- 入口节点：rect 做色块，文字在下方 -->
  <rect x="330" y="30" width="140" height="40" rx="6"
        fill="var(--svg-entry-fill)" stroke="var(--svg-entry)" stroke-width="2.5"/>
  <text x="400" y="86" text-anchor="middle" font-weight="700" font-size="13"
        fill="var(--text-primary)">main.py</text>

  <!-- L1 节点：水平分布，间距 ≥ 120px -->
  <rect x="100" y="140" width="120" height="40" rx="6"
        fill="var(--svg-node-fill)" stroke="var(--svg-node-stroke)" stroke-width="1.5"/>
  <text x="160" y="196" text-anchor="middle" font-weight="600" font-size="12"
        fill="var(--text-primary)">config.py</text>

  <!-- 连线：从父 rect 底部到子 rect 顶部 -->
  <line x1="380" y1="70" x2="160" y2="140"
        stroke="var(--svg-arrow)" stroke-width="1.5" marker-end="url(#arrow)"/>
</svg>
```

布局规则：
- 入口节点在顶部居中
- 第一层模块水平分布在入口下方（间距 ≥ 120px）
- 第二层在对应第一层下方（缩进 40px）
- 同一层的节点 y 坐标相同
- **每层节点的 rect 和文字不与相邻节点重叠**

### 横向依赖表（deep 报告补充）

不适合 SVG 的场景用 HTML 表格代替：

| 模块 | 被谁调用 | 调用了谁 |
|---|---|---|
| `service.user` | `handler.user` | `db.query`, `cache.get` |
| `db.query` | `service.*` | — (叶子节点) |

---

## 标记约定

- `← 入口`：入口点
- `→ 待深入`：未展开的模块（用红色虚线边框）
- `★ 关键`：核心业务逻辑（用紫色高亮）
- `— (叶子节点)`：没有向下依赖的模块

---

## 何时用 SVG、何时用表格

| 场景 | 渲染方式 | 原因 |
|---|---|---|
| 调用链 ≤ 15 个节点 | **SVG** | 自动布局清晰 |
| 调用链 > 15 个节点 | **SVG + 截断** | 只画核心路径，其余标"省略" |
| 模块级依赖概览 | **HTML 表格** | 用 `table` + `row-l0~l3` 着色 |
| 数据流追踪 | **flow-container + SVG** | 水平步骤流 + 完整 SVG 图 |

---

## 自检清单

输出 SVG 前逐条检查：

1. 所有文字是否在 rect 外部？（文字的 y 坐标 > rect 的 y + height）
2. 连线标注是否有背景衬底？（不直接写在连线上）
3. 同层节点间距是否 ≥ 40px？
4. 相邻文字是否重叠？（检查 bounding box）
5. viewBox 是否足够大，不裁剪任何元素？
