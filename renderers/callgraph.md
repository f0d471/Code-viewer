# 调用关系图 — SVG 渲染规范

所有调用关系图用内联 SVG，嵌入 HTML 报告。

---

## 节点样式

| 类型 | fill | stroke | stroke-width | rx |
|---|---|---|---|---|
| 入口节点 | `var(--svg-entry-fill)` | `var(--svg-entry)` | 2.5 | 6 |
| 普通模块 | `var(--svg-node-fill)` | `var(--svg-node-stroke)` | 1.5 | 6 |
| 依赖/外部 | `var(--svg-dependency-fill)` | `var(--svg-dependency)` | 1.5 | 6 |
| 待深入节点 | `var(--color-warning-bg)` | `var(--color-warning)` | 1.5 | 6 |

fill/stroke 引用 CSS 变量，自动跟随亮色/暗色模式。

---

## 箭头定义（每个 SVG 内复制一次）

```html
<defs>
  <marker id="arrow" viewBox="0 0 10 7" refX="10" refY="3.5"
    markerWidth="8" markerHeight="6" orient="auto-start-reverse">
    <path d="M 0 0 L 10 3.5 L 0 7 z" fill="var(--svg-arrow)"/>
  </marker>
  <marker id="arrow-entry" viewBox="0 0 10 7" refX="10" refY="3.5"
    markerWidth="8" markerHeight="6" orient="auto-start-reverse">
    <path d="M 0 0 L 10 3.5 L 0 7 z" fill="var(--svg-entry)"/>
  </marker>
</defs>
```

---

## 常见图类型

### 纵向调用树（scan 报告 / deep 报告）

适合展示从入口到各层模块的调用关系。从上到下布局。

```
入口 main()
  ├── config.load()
  ├── db.connect()
  ├── router.register()
  │     ├── GET /users → handler.users()
  │     │       └── service.getUsers()
  │     │             └── db.query()
  │     └── POST /login → handler.login()
  │           ├── service.verifyPassword()
  │           └── session.create()
  └── server.listen()
```

SVG 示例骨架：

```html
<svg viewBox="0 0 800 600" xmlns="http://www.w3.org/2000/svg"
     style="font-family: 'Noto Sans SC', system-ui, sans-serif;">
  <defs><!-- 箭头 --></defs>
  <!-- 入口节点在顶部居中 -->
  <!-- 第一层模块在入口下方，水平分布 -->
  <!-- 第二层在对应第一层下方 -->
  <!-- 连线用 path + marker-end="url(#arrow)" -->
</svg>
```

布局规则：
- 入口节点在顶部居中
- 第一层模块水平分布在入口下方（间距 ≥ 120px）
- 第二层在对应第一层下方（缩进 40px）
- 同一层的节点 y 坐标相同

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
