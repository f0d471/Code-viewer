# 各语言/平台入口点速查

找入口 = 找理解项目的支点。每种语言/平台的入口特征不同，按以下表快速定位。

---

## 1. 系统语言

| 语言 | 入口点 | 怎么找 | 执行流 |
|---|---|---|---|
| C / C++ | `int main(int argc, char *argv[])` | 搜 `main`，或看 Makefile/CMakeLists 的 target | main → 初始化 → 主循环/调度 → 退出 |
| Rust | `fn main()` | 看 `src/main.rs`，或搜 `fn main` | main → 初始化 → run |
| Go | `func main()` | 看 `cmd/` 目录，或搜 `func main` | main → 初始化 → 启动服务 |
| Zig | `pub fn main()` | 看 `src/main.zig` | main → 初始化 → 运行 |
| Haskell | `main :: IO ()` | 搜 `main ::`，或看 `app/Main.hs` | main → IO action 链 |
| OCaml | `let () = ...` | 看顶层 `.ml` 文件的最后表达式 | 最后表达式就是入口 |
| Elixir / Erlang | Application 模块 `start/2` | 看 `mix.exs` 的 `mod` 配置，或 `app.src` 的 `mod` | Application.start → Supervisor 树 |
| Assembly | `_start` 或 `main` | 看链接脚本的 `ENTRY`，或搜 `_start` | _start → 设置栈 → 调 main → syscall exit |
| 嵌入式 C | `void Reset_Handler(void)` 或 `main()` | 看启动文件 `startup_*.s` 或链接脚本 | Reset_Handler → 初始化 .bss/.data → main |

---

## 2. 脚本语言

| 语言 | 入口点 | 怎么找 | 执行流 |
|---|---|---|---|
| Python | `if __name__ == "__main__":` 或顶层代码 | 看 `__main__.py`、`setup.py` 的 console_scripts、或顶层代码 | 顶层代码从上到下执行 |
| Ruby | 顶层代码或 `bin/` 脚本 | 看 `bin/` 目录、`Gemfile` 的 executables | 顶层代码从上到下执行 |
| Perl | 顶层代码（无显式 main） | 看 `.pl` / `.pm` 文件的首行逻辑 | 顶层代码从上到下执行 |
| Lua | 顶层代码（无显式 main） | 看 `main.lua` 或入口 `.lua` | 顶层代码从上到下执行 |
| Shell (bash/zsh) | 第一行非注释命令 | 看脚本文件的首行逻辑 | 从上到下执行，除非 source 其他脚本 |
| PHP | 顶层代码或 `index.php` | 看 `composer.json` 的 autoload、或 web 入口 `index.php` | 顶层代码从上到下执行 |

---

## 3. Web 后端

| 框架 | 入口点 | 怎么找 | 执行流 |
|---|---|---|---|
| Express (Node) | `app.listen()` + 路由注册 | 看 `index.js` / `app.js` / `server.js` | 创建 app → 注册中间件/路由 → listen |
| Flask (Python) | `app = Flask(__name__)` + 路由装饰器 | 搜 `Flask(__name__)` 或 `app.route` | 创建 app → 注册路由 → app.run |
| Django (Python) | `urls.py` + `wsgi.py` / `asgi.py` | 看项目名下的 `urls.py`、`settings.py` 的 ROOT_URLCONF | wsgi.py → 加载 urls → 路由分发 |
| FastAPI (Python) | `app = FastAPI()` + 路由 | 搜 `FastAPI(` | 创建 app → 注册路由 → uvicorn 启动 |
| Spring Boot (Java) | `@SpringBootApplication` 类的 main | 搜 `SpringBootApplication` | main → 启动 Spring 容器 → 扫描组件 |
| Gin (Go) | `gin.Default()` + 路由 | 搜 `gin.Default` 或 `gin.New` | 创建 engine → 注册路由 → Run |
| Actix (Rust) | `HttpServer::new` | 搜 `HttpServer` | 创建 server → 注册 service → run |
| Rails (Ruby) | `config/routes.rb` + `config.ru` | 看 `config/routes.rb` | Rack 启动 → 路由分发 → Controller |
| Laravel (PHP) | `routes/web.php` + `public/index.php` | 看 `routes/` 目录 | index.php → Kernel → 路由分发 |
| Phoenix (Elixir) | `MyAppWeb.Endpoint` | 看 `lib/my_app_web/endpoint.ex` | Endpoint → Router → Controller |
| ASP.NET (C#) | `Program.cs` 的 `Main` 或 `Startup.cs` | 看 `Program.cs` | Main → 创建 Host → 配置中间件 |
| NestJS (Node) | `main.ts` 的 `NestFactory.create` | 看 `src/main.ts` | 创建 app → 注册模块 → listen |
| Spring (Java, 非 Boot) | `web.xml` 或 `@Configuration` 类 | 看 `web.xml` 的 `<servlet>` 配置 | Servlet 容器启动 → DispatcherServlet |

---

## 4. Web 前端

| 框架 | 入口点 | 怎么找 | 执行流 |
|---|---|---|---|
| React | `App.jsx` / `index.tsx` 的 `ReactDOM.render` / `createRoot` | 看 `src/App` 或 `src/index` | index → render App → 组件树展开 |
| Vue | `main.ts` / `main.js` 的 `createApp` | 看 `src/main.ts` | createApp(App).mount → 组件树展开 |
| Angular | `main.ts` 的 `bootstrapModule` | 看 `src/main.ts` | bootstrap → AppModule → 路由 |
| Svelte | `main.js` 的 `new App({ target })` | 看 `src/main.js` | 实例化 App → 挂载 DOM |
| Next.js | `pages/` 或 `app/` 目录的路由文件 | 看 `pages/index.tsx` 或 `app/page.tsx` | 框架自动路由 → 页面组件 |
| Nuxt | `app.vue` + `pages/` 目录 | 看 `app.vue` 或 `nuxt.config.ts` | 框架自动路由 → 页面组件 |
| SvelteKit | `+page.svelte` / `+layout.svelte` | 看 `src/routes/` 目录 | 框架自动路由 → 页面组件 |
| Astro | `src/pages/` 目录的 `.astro` 文件 | 看 `src/pages/index.astro` | 框架自动路由 → 页面组件 |
| SolidJS | `index.tsx` 的 `render` | 看 `src/index.tsx` | render(App) → 组件树 |
| Remix | `root.tsx` + `app/` 目录 | 看 `app/root.tsx` | 框架路由 → 页面组件 |

---

## 5. 移动端

| 框架 | 入口点 | 怎么找 | 执行流 |
|---|---|---|---|
| Flutter | `main()` 中的 `runApp(MyApp())` | 看 `lib/main.dart` | runApp → Widget 树展开 |
| React Native | `index.js` 的 `AppRegistry.registerComponent` | 看 `index.js` 或 `App.tsx` | registerComponent → 组件树 |
| SwiftUI | `@main struct App: App { var body }` | 看标记了 `@main` 的 struct | body → WindowGroup → View 树 |
| UIKit (iOS) | `AppDelegate` 或 `SceneDelegate` | 看 `AppDelegate.swift` | didFinishLaunching → 创建 UIWindow |
| Android (Kotlin) | `MainActivity` 的 `onCreate` | 看 `AndroidManifest.xml` 的 launcher Activity | onCreate → setContentView → View 树 |
| Jetpack Compose | `setContent { }` in Activity.onCreate | 看 `MainActivity.kt` | setContent → Composable 树 |

---

## 6. 构建配置入口

当代码入口不好猜时，看构建配置：

| 工具 | 入口字段 | 说明 |
|---|---|---|
| package.json | `main` / `scripts.start` / `bin` | `bin` = CLI 工具入口 |
| Cargo.toml | `[package]` 或 `[[bin]]` | 多 bin 项目看 `[[bin]]` 的 `path` |
| go.mod | 看 `cmd/` 目录结构 | Go 约定：`cmd/appname/main.go` |
| CMakeLists.txt | `add_executable` / `add_library` | 第一个 executable 就是入口 |
| Makefile | 第一个 target（通常是 `all`） | 追踪 `all` 依赖的第一个 target |
| Dockerfile | `CMD` / `ENTRYPOINT` | 容器启动时执行的命令 |
| pyproject.toml | `[project.scripts]` / `[tool.setuptools]` | console_scripts 定义 CLI 入口 |
| webpack.config.js | `entry` | 打包入口文件 |
| vite.config.js | 看 `index.html` 的 script 标签 | Vite 以 HTML 为入口 |
| build.gradle | `mainClass` 或 `application` plugin | application 插件指定 main class |
| meson.build | `executable()` | 第一个 executable 是入口 |
| BUILD.bazel (Bazel) | `cc_binary` / `rust_binary` 等 | binary rule 就是入口 |
| Podfile / Gemfile | 不直接指定，看 `.xcodeproj` 或 `config.ru` | iOS/Ruby 项目需要其他线索 |

---

## 7. 硬件描述语言

硬件项目的入口不是"main 函数"，而是**顶层模块 + 仿真脚本**。

### 7.1 Verilog / SystemVerilog

| 场景 | 入口点 | 怎么找 | 执行流 |
|---|---|---|---|
| 设计顶层 | `module top` 或 `module <项目名>` | 搜 `module top`，看 testbench 实例化的模块，看仿真脚本的 `TOP_MODULE` | 顶层模块 → 子模块实例化 → 端口连线 |
| 仿真入口 | `module tb_top` 或 `module tb_<模块名>` | 看 `tb/` 目录，搜 `module tb_`，看仿真 Makefile 的 target | tb 初始化 → 实例化 DUT → 时钟/复位 → $finish |
| 综合入口 | TOP_MODULE 约束 | 看综合脚本（`.tcl`）的 `set_top` 或 Makefile 的 TOP_MODULE 变量 | 综合工具从 TOP_MODULE 开始展平 |

**关键区别**：硬件是**并行执行**——所有 `always` 块同时运行，不是顺序调用。理解执行流要看时钟沿和信号传播，不是函数调用栈。

### 7.2 Chisel / Scala

| 场景 | 入口点 | 怎么找 | 执行流 |
|---|---|---|---|
| 生成 RTL | `object Elaborate extends App` | 搜 `Elaborate` 或 `ChiselStage.emitSystemVerilog` | Elaborate → 实例化顶层 Module → emitSystemVerilog |
| 顶层 Module | `class XxxTop extends Module` | 看 Elaborate 里 `new` 的那个类 | Module 定义 IO + 内部 LazyModule 拓扑 |
| Diplomacy 拓扑 | `class Xxx extends LazyModule` | 看 Top Module 里 `LazyModule(...)` 实例化的类 | LazyModule 声明节点 → 连接 `:=` → module 实现 |

**Chisel 特殊点**：Chisel 代码不是在运行时执行的——它是在**编译期**生成 RTL 的。`Elaborate` 对象的 `main` 函数只在"生成 Verilog"时运行一次。真正的硬件行为在生成的 Verilog 里。

### 7.3 SpinalHDL

| 场景 | 入口点 | 怎么找 | 执行流 |
|---|---|---|---|
| 生成 RTL | `object Gen extends App` | 搜 `SpinalVerilog(new Xxx)` | App.main → 实例化 Component → 生成 Verilog |
| 顶层 Component | `class Xxx extends Component` | 看 Gen 里 `new` 的那个类 | Component 定义 io + 内部逻辑 |

### 7.4 VHDL

| 场景 | 入口点 | 怎么找 | 执行流 |
|---|---|---|---|
| 设计顶层 | `entity <name> is ... architecture` | 看仿真脚本的顶层配置，搜 `entity` + `port` | entity 声明端口 → architecture 实现逻辑 |
| 仿真入口 | testbench entity（无端口） | 看 `tb/` 目录，搜 `entity tb_` | tb 初始化 → 实例化 DUT → stimulus |

---

## 8. 仿真与验证平台

仿真项目有两层入口：仿真器入口 + 被测设计入口。

| 工具 | 入口点 | 怎么找 | 说明 |
|---|---|---|---|
| Verilator | `main.cpp` / `sim_main.cpp` | 看 `csrc/` 或 `sim/` 目录的 cpp 文件 | C++ main → 创建 Vtop 实例 → 调用 eval() 循环 |
| VCS | `module tb_top` | 看 Makefile 的 `-top` 参数 | VCS 编译 tb_top 为可执行文件 |
| ModelSim / Questa | `vsim` 命令的 `-top` 参数 | 看仿真脚本 `.do` 的 `vsim` 命令 | vsim 加载顶层模块 |
| cocotb (Python) | `test_*.py` 的协程 | 看 `tests/` 目录，搜 `@cocotb.test` | cocotb 驱动 DUT 的输入，检查输出 |
| iverilog | `module tb_top` | 看 Makefile 的仿真 target | iverilog 编译 tb → vvp 执行 |

### Verilator 特别说明

Verilator 把 Verilog 编译成 C++ 类（`Vtop`），仿真入口是 C++ 的 `main()`：

```
Verilog: module top → 编译后变成 → C++: class Vtop
                                    ↑
C++: main() → Vtop* top = new Vtop → while(!done) { top->clk = !clk; top->eval(); }
```

找入口的两步：
1. 找 Verilog 顶层：看 Makefile 的 `TOP_MODULE` 或 testbench 实例化的模块
2. 找 C++ 入口：搜 `Vtop` 的实例化，通常在 `csrc/sim_main.cpp` 或 `npc/csrc/`

---

## 9. 其他

| 平台 | 入口点 | 怎么找 |
|---|---|---|
| Jupyter Notebook | 第一个 code cell | 按 cell 顺序阅读 |
| LabVIEW | `Main.vi` | 看项目文件 `.lvproj` 的启动 VI |
| MATLAB | 脚本文件的第一行 / 函数文件的 function 定义 | 看 `*.m` 文件 |
| R | 顶层代码或 `main.R` | 看 R 脚本的第一行逻辑 |
| Scala (非 Chisel) | `object Main extends App` 或 `def main(args: Array[String])` | 搜 `extends App` 或 `def main` |
| Kotlin (非 Android) | `fun main()` | 搜 `fun main` |
| Dart (非 Flutter) | `void main()` | 看 `bin/` 目录 |
| Swift (非 iOS) | 顶层代码（无显式 main）或 `@main` struct | 看 `Sources/` 目录 |

---

## 使用指南

1. **先看项目根目录的文件**：`package.json`、`Cargo.toml`、`Makefile`、`CMakeLists.txt`、`build.gradle` 等构建配置通常能直接告诉你入口在哪
2. **搜关键词**：`main`、`app`、`server`、`start`、`elaborate`、`top`、`TOP_MODULE`
3. **看 README**：启动说明通常在 README 的"Getting Started"或"Usage"部分
4. **看 Dockerfile**：`CMD` 和 `ENTRYPOINT` 暴露了真正的启动命令
5. **硬件项目特别**：不要只找 module，还要找仿真/综合脚本里的 TOP_MODULE 配置
