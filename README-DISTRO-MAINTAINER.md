# TermiAI 构建与分发说明

本文件原为上游 WezTerm 面向 Linux 发行版打包者的说明。TermiAI 的目标平台是 Windows 10 / 11，不再提供 Linux / macOS 发行版包，原 distro 打包流程不适用。这里改为记录本项目的构建与分发约定。

## 目标平台

- Windows 10 1809（10.0.17763）及以上 / Windows 11，x86_64
- Linux 与 macOS 的源码继承自上游，但不在维护范围内，不保证构建与运行

## 构建产物

| 产物 | 说明 | 状态 |
| --- | --- | --- |
| `wezterm.exe` | CLI 与启动器 | 继承上游，品牌化待定 |
| `wezterm-gui.exe` | GUI 终端进程 | 继承上游，WinUI 3 宿主接入后替换 |
| `wezterm-mux-server.exe` | 多路复用服务端 | 继承上游 |
| `strip-ansi-escapes.exe` | 去除 ANSI 转义的工具 | 继承上游 |

品牌化（可执行文件名、图标、关于信息）尚未完成，当前产物名称沿用上游。

## 构建

环境要求与源码构建步骤见 [README.md](README.md) 与 [CONTRIBUTING.md](CONTRIBUTING.md)。

```powershell
$env:PATH = "C:\Strawberry\perl\bin;$env:PATH"
cargo build --release
```

构建产物位于 `target\release\`。如只需 GUI：

```powershell
cargo build --release --bin wezterm-gui
```

## 版本号

当前沿用上游由 git 提交时间生成的版本号机制（`wezterm-version` crate）。TermiAI 的独立版本号方案尚未确定，确定后再更新本文件。

## 分发

- 目前尚无官方安装包，仅支持从源码构建。
- 后续计划：完成品牌化资源替换，产出带签名的 Windows 安装包，并建立独立发布通道。
- 在品牌化完成前，`assets/shell-integration`、`assets/shell-completion` 等资源继续沿用上游。

## 许可证

TermiAI 以 MIT 协议开源，保留上游 WezTerm 的版权与许可声明，详见 [LICENSE.md](LICENSE.md)。
