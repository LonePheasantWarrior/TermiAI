# TermiAI

> 极速、优雅、AI 原生的现代化多端服务器工作台，专为 Windows 深度优化。

TermiAI 基于开源项目 [WezTerm](https://github.com/wezterm/wezterm) 二次开发：沿用其经过验证的 Rust GPU 渲染核心、终端模型与多路复用能力，并在 Windows 侧逐步引入 WinUI 3 现代化视觉宿主与 AI 原生协作能力。

> **项目状态**：二开早期阶段。终端渲染、多路复用、SSH 与 SFTP 底层等能力继承自上游 WezTerm，当前源码即可使用；AI 协同、WinUI 3 视觉层、AI 增强 SFTP 界面等属于开发中或规划中特性，尚未随当前源码提供。产品目标的唯一事实来源为 [TermiAI 产品需求文档（PRD）](<TermiAI-docs/TermiAI 产品需求文档 (PRD).md>)。

## 与 WezTerm 的关系

- 本仓库 fork 自 [wezterm/wezterm](https://github.com/wezterm/wezterm)，当前代码与上游保持同步，尚未进行品牌化替换。
- 终端核心、窗口抽象、mux、SSH/SFTP 等底层继续复用上游实现；TermiAI 的增量开发应尽量以独立 crate 或明确隔离的模块叠加，降低后续合并上游的成本。
- 上游文档与官网（<https://wezterm.org/>）仍适用于继承下来的终端能力，但不代表 TermiAI 的最终产品形态。
- 项目遵循 MIT 协议并保留上游版权声明，详见 [LICENSE.md](LICENSE.md)。

## 目标能力

| 能力 | 说明 | 状态 |
| --- | --- | --- |
| GPU 终端内核 | 继承 WezTerm 的 Rust 渲染核心、字体与终端仿真能力 | 可用（继承） |
| 多路复用与 SSH | 标签/分屏、SSH 远程会话、跳板机等 | 可用（继承） |
| 内置 SFTP | 复用 SSH 认证，提供终端侧文件树、拖拽传输与远程文本编辑 | 底层可用（继承），界面规划中 |
| AI 双协议接入 | 同时支持 OpenAI 与 Anthropic API 格式，可自定义 Base URL、Model、System Prompt | 规划中 |
| AI 终端协同 | 内联命令补全、报错自动捕获、Copilot 侧边栏与多会话上下文池 | 规划中 |
| WinUI 3 视觉层 | Mica / Acrylic 材质、无边框圆角窗口、Fluent 动效与命令面板 | 规划中 |
| 安全护栏 | 高危命令二次人工确认、API Key 本地 DPAPI 加密 | 规划中 |

> 状态说明：**可用（继承）** 表示当前源码即可使用；**开发中 / 规划中** 表示已纳入 PRD、尚未完成，请勿将目标形态当作现状。

## 快速开始

### 环境要求（Windows）

- Windows 10 1809（10.0.17763）及以上，推荐 Windows 11
- [Rust](https://rustup.rs/) 稳定版工具链，必须使用 **MSVC** 版本
- Visual Studio Build Tools（含“使用 C++ 的桌面开发”工作负载）
- [Strawberry Perl](https://strawberryperl.com/)：编译 OpenSSL 所需，且需确保其 `perl.exe` 位于 `PATH` 最前
- Git（含子模块初始化）

### 构建

```powershell
git clone https://github.com/LonePheasantWarrior/TermiAI.git
cd TermiAI
git submodule update --init --recursive

$env:PATH = "C:\Strawberry\perl\bin;$env:PATH"
cargo build --release --bin wezterm-gui
```

### 运行

```powershell
.\target\release\wezterm-gui.exe
```

也可以通过 `cargo run --release --bin wezterm -- start` 启动。

> 当前窗口与交互仍由上游 WezTerm 的 GUI 实现提供，WinUI 3 宿主尚未接入；可执行文件名与图标等品牌化工作也尚未完成。

### 常用开发命令

```powershell
cargo check          # 快速类型检查
cargo test --all     # 运行全部测试
cargo fmt --all      # 统一代码格式
```

## 文档

- [TermiAI 产品需求文档（PRD）](<TermiAI-docs/TermiAI 产品需求文档 (PRD).md>)：产品定位、功能范围与交互设计
- [CONTRIBUTING.md](CONTRIBUTING.md)：开发环境、代码结构与协作约定
- [PRIVACY.md](PRIVACY.md)：本地数据、AI 调用与第三方服务的隐私说明
- [README-DISTRO-MAINTAINER.md](README-DISTRO-MAINTAINER.md)：构建产物与分发说明
- `docs/`：继承自上游 WezTerm 的 mkdocs 文档源码，目前不作为 TermiAI 官方文档发布

## 许可证

TermiAI 以 MIT 协议开源，基于 WezTerm（Copyright (c) 2018-Present Wez Furlong）二次开发。再分发时请保留 [LICENSE.md](LICENSE.md) 中的版权与许可声明。
