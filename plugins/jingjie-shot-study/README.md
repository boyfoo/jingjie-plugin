# 镜界拉片

将有画面证据的逐镜分析、完整作者提示词核对及镜界草稿读写放进同一个插件。

- `skills/video-shot-study/`：现有拉片 skill 的完整副本，包含观察方法和镜界保存规则。
- `mcp.json`：连接 `http://127.0.0.1:5174/mcp`，需要使用者在自己的电脑上运行镜界服务。
- `.codex-plugin/plugin.json` 与 `.mcp.json`：当前 Codex 客户端兼容配置。
- `assets/`：复用镜界现有品牌图标，主题色为 `#fcd535`。

本包不包含镜界服务器或视频处理工具，也不负责启动它们。没有运行镜界服务时，仍可按 skill 做本地拉片；镜界工具无法连接，需要先启动服务。

使用示例：

```text
使用镜界拉片，对这个视频做有画面证据的拉片，核对完整提示词来源：<视频链接>
```

拉片完成后主动询问是否提交；用户明确同意或已经授权时才写入镜界。镜界 MCP 保存的是编辑稿，发布由用户在网站操作。

本插件源码同时支持通用插件格式及 Codex 插件市场。在 Codex 终端执行：

```powershell
codex plugin marketplace add https://github.com/boyfoo/jingjie-plugin.git
codex plugin add jingjie-shot-study@jingjie-local
```

也可使用配套的 `jingjie-local-marketplace-0.1.0.zip`，解压后按根目录 README 操作。
