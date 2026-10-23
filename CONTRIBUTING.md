# 参与 TermiAI 开发

感谢你愿意为 TermiAI 贡献时间与代码。本文面向在本仓库工作的开发者，说明开发环境、代码结构与协作约定。

> TermiAI 是 [WezTerm](https://github.com/wezterm/wezterm) 的二次开发分支。与 TermiAI 产品形态无关的通用改进，建议直接提交到上游仓库；与本项目相关的功能、界面与 AI 能力开发在本仓库进行。

## 一、开发环境

### 目标平台

TermiAI 只面向 Windows 10 1809+ / Windows 11（x86_64）。Linux 与 macOS 代码虽继承自上游，但不在维护范围内，不保证可构建或可运行。

### 依赖

- [Rust](https://rustup.rs/) 稳定版工具链，必须使用 **MSVC** 版本
- Visual Studio Build Tools（含“使用 C++ 的桌面开发”工作负载）
- [Strawberry Perl](https://strawberryperl.com/)：编译 OpenSSL 所需，需保证其 `perl.exe` 在 `PATH` 中优先于其他 Perl
- Git（含子模块）

### 首次构建

```powershell
git submodule update --init --recursive
$env:PATH = "C:\Strawberry\perl\bin;$env:PATH"

cargo check                                  # 快速类型检查
cargo build --release --bin wezterm-gui      # 构建 GUI
cargo run --release --bin wezterm -- start   # 构建并启动
```

## 二、代码结构

| 目录 | 职责 |
| --- | --- |
| `term/` | 终端模型，与窗口系统无关；转义序列、终端行为相关改动通常在此 |
| `wezterm-gui/` | 当前 GUI 渲染与交互实现，继承自上游 |
| `window/` | 窗口、输入与 GPU 上下文抽象 |
| `mux/`、`wezterm-client/`、`wezterm-mux-server/` | 多路复用与远程会话 |
| `wezterm-ssh/` | SSH 与 SFTP 底层封装 |
| `config/`、`lua-api-crates/` | 配置系统与 Lua API |
| `wezterm/` | CLI 入口与启动器 |
| `TermiAI-docs/` | 产品需求与设计文档 |

TermiAI 新增能力（AI 接入、WinUI 3 宿主、SFTP 界面等）应尽量放在独立 crate 或边界清晰的模块中，避免对上游核心做大范围改写，以便持续合并上游更新。

## 三、开发约定

### 1. 与上游同步

- 不随意重命名、移动上游 crate 或公开 API；确需修改时，在 PR 中说明其对后续合并上游的影响。
- 新增依赖优先复用 `Cargo.toml` 中 workspace 已锁定的版本。
- 定期合并上游 `main`，冲突处理保持最小 diff；纯格式化改动不要与功能改动混在同一提交。

### 2. 安全红线（AI 与凭据相关）

- AI API Key 必须通过 Windows DPAPI 加密后落盘，禁止以明文写入配置文件、日志或崩溃报告。
- 未经用户明确触发，不得向任何外部服务发送终端内容、文件内容或会话元数据；项目不内置遥测。
- AI 生成的高危命令（如 `rm -rf`、`drop database`）必须经过二次人工确认，禁止默认自动执行。
- 发送给模型服务的上下文应最小化并按需截断；日志输出需脱敏。

### 3. 代码质量

- 提交前运行：

  ```powershell
  cargo fmt --all
  cargo test --all
  ```

- 终端行为变更需补充测试，可参考 `term/src/test/` 中已有的测试辅助代码。
- 新增代码的注释与用户文档使用中文；修改上游代码时保留原有英文注释。
- 新增或改变用户可见行为时，同步更新 `TermiAI-docs/` 中的相关文档。

## 四、提交流程

1. 从 `main` 拉出功能分支，保持提交聚焦、信息清晰。
2. PR 描述中写清动机、行为变化与验证方式；涉及界面变更请附截图或录屏。
3. 不要在 PR 中提交密钥、证书、个人配置或本地构建产物。
4. 通过 CI 与代码评审后合并。

## 五、文档

- `TermiAI-docs/` 是产品文档目录，PRD 与设计说明以这里为准。
- `docs/` 是继承自上游 WezTerm 的 mkdocs 站点源码，当前不作为 TermiAI 官方文档对外发布。如需本地预览，可按上游方式执行 `ci/build-docs.sh serve`（需要类 Unix 环境，如 WSL 或 Git Bash）。
