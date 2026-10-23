# TermiAI 产品需求文档 (PRD)

- **项目名称**：TermiAI
- **底座技术**：WezTerm (Rust 渲染核心) + WinUI 3 (Windows 现代化 UI 宿主)
- **目标平台**：Windows 10 / 11 (专为 Windows 深度优化)
- **核心定位**：极速、优雅、AI 原生的现代化多端服务器工作台。

## 一、 核心功能模块设计

### 1. AI 接入与双协议支持 (OpenAI & Anthropic)

- **支持格式**：
  - **OpenAI 格式**：支持官方及所有兼容 OpenAI 聊天补全接口（`/v1/chat/completions`）的第三方模型（如 DeepSeek、Llama 3 本地代理等）。
  - **Anthropic 格式**：支持 Claude 3.5 Sonnet 等长文本推理能力卓越的 Anthropic API 格式。
- **配置特性**：支持自定义 Base URL、API Key、请求超时、默认 Model 名称以及 System Prompt（可预设运维助手、代码审计员、Linux 调试专家等角色）。
- **数据安全**：API Key 采用 Windows DPAPI（Data Protection API）进行本地加密存储，绝不上传云端。

### 2. WinUI 3 现代化视觉与动效系统

- **视觉风格**：
  - 采用 **Mica（云母）** 与 **Acrylic（亚克力）** 材质，窗口背景自动跟随系统亮/暗模式及壁纸透出高级质感。
  - 极简无边框设计，自定义圆角窗口（Window Corner Radius）及阴影效果。
- **动效与交互**：
  - **流畅过渡**：标签页切换、侧边栏展开/收起采用 Fluent Design 物理弹簧阻尼动画（Spring Animations）。
  - **Command Palette (命令面板)**：参考 Modern IDE 风格（`Ctrl + Shift + P`），支持模糊搜索所有快捷键、AI 指令、已保存的 SSH 会话和 SFTP 快捷路径。

### 3. 多远程服务器跨实例 AI 协同

- **场景痛点**：集群运维时，传统终端需要手动开多个窗口逐个排查。
- **AI 协同机制**：
  - **Multi-Session Context Pool（多会话上下文池）**：允许用户将多个 SSH 终端分屏或 Tab 组合为一个“服务器集群组”（例如：`Production-Cluster`）。
  - **批量执行与 AI 验证**：用户可向 AI 下达自然语言指令（例如：“检查这三台服务器的 Nginx 错误日志，找出高频 502 的根源并修复”）。
  - **安全沙箱**：AI 生成的高危命令（如 `rm -rf`、`drop database`）触发**强制二次人工确认卡片**（Card-based Confirmation），防止 AI 幻觉造成生产事故。

### 4. 内置现代化 SFTP 数据传输

- **双模态界面**：支持“独立 SFTP 标签页”与“终端侧边栏文件树”两种形态。
- **核心功能**：
  - 完美复用当前的 SSH 认证信息（密码、私钥、跳板机配置），免去重复登录。
  - 支持本地与远程之间的**拖拽上传/下载**，提供现代化的多任务并发进度条与速度监控。
  - 内置轻量级文本查看/编辑器（带语法高亮），可直接在线修改远程配置文件（如 `.env`、`nginx.conf`）并自动保存。

## 二、 交互设计方案 (UX/UI Design)

为了保持界面的“简单、优雅、现代化”，TermiAI 采用“沉浸式终端 + 浮动式智能侧边栏”的交互布局。

### 1. AI 配置交互设计

- **入口**：点击右上角齿轮图标进入 `Settings -> AI Assistant`。
- **配置界面**：
  - 采用卡片式表单，分步引导用户选择 Provider（OpenAI / Anthropic）。
  - 提供 **“Test Connection (测试连通性)”** 按钮，实时返回延迟与模型列表，配置成功后有微动效反馈。

### 2. AI 如何参与终端操作 (Copilot 交互闭环)

TermiAI 设计了三种轻量、无侵入的 AI 介入形态：

- **形态 A：Inline Command Suggestion (内联行间补全)**
  - 当用户在终端输入不熟悉的命令行时（如复杂复杂的 `awk` 或 `ffmpeg`），行尾会以浅灰色斜体字实时渲染 AI 预测补全，按 `Tab` 即可采纳。
- **形态 B：Smart Debugger (智能报错捕获 - 自动触发)**
  - 当终端输出检测到非零退出码（Exit Code != 0，如编译报错、Python Stack Trace、数据库连接失败），输入框上方会优雅地浮现一个微型胶囊按钮：`✨ Ask AI to Fix`。
  - 点击后，右侧自动滑出 AI 面板，并**自动截取报错上下文与前后 50 行日志**，直接给出诊断原因和一键执行修复命令的卡片。
- **形态 C：AI Copilot Sidebar (侧边栏多功能对话)**
  - 按 `Ctrl + Space` 或点击侧边栏图标呼出 AI 聊天抽屉。
  - 用户可以在这里直接提问，支持通过 `@` 符号选择特定的服务器会话作为上下文（例如：`@web-server-01 帮我分析一下当前的内存占用瓶颈`）。

### 3. SFTP 与终端的联动交互

- 在终端输入 `sftp://` 或通过命令面板打开 SFTP 视图时，界面无缝平移拆分为**左侧终端、右侧文件树**。
- 当用户在 SFTP 中右键某个脚本文件，可直接选择 **“Run in Terminal (在终端中运行)”**，系统会自动在当前激活的 Tab 路径下执行该脚本，实现数据传输与命令操作的完美闭环。