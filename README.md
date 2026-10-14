# 镜界拉片插件

版本：0.1.0。把现有 `video-shot-study` skill 与镜界 MCP 连接配置放进一个插件，使用镜界现有图标和主题色 `#fcd535`。

## 使用前提

- 使用支持本地插件的 Codex，并可在终端调用 `codex` 命令。
- 使用者需要在自己的电脑上运行镜界服务，服务地址须与插件内 `mcp.json` 和 `.mcp.json` 的配置一致。
- 本包包含 skill、MCP 配置和品牌素材；镜界服务源码、服务启动器和视频处理工具需要另行准备。做视频拉片需要可用的媒体探测及抽帧工具，例如 ffprobe、ffmpeg。

## 通过 GitHub 安装

在终端执行：

```powershell
codex plugin marketplace add https://github.com/boyfoo/jingjie-plugin.git
codex plugin add jingjie-shot-study@jingjie-local
codex plugin list --marketplace jingjie-local --json
```

仓库地址：[boyfoo/jingjie-plugin](https://github.com/boyfoo/jingjie-plugin)。

## 从本地目录安装

克隆本仓库，或下载仓库 ZIP 并解压。保留 `.agents`、`.codex-plugin` 和 `.mcp.json` 等隐藏文件。

进入包含 `README.md` 和 `.agents` 的仓库根目录，在终端执行：

```powershell
codex plugin marketplace add .
codex plugin add jingjie-shot-study@jingjie-local
codex plugin list --marketplace jingjie-local --json
```

本地安装时保持该目录可用。安装后重新打开会话或按客户端提示重新加载插件，确认可以发现 `video-shot-study` 和镜界工具。先调用只读的 `jingjie_get_taxonomy` 检查连接。

如果电脑上已有单独安装的同名 skill 或 MCP，先验证插件中的入口，再按需停用旧入口，避免重复加载。本包不会自动改动原有全局配置。

## 使用

```text
使用镜界拉片，对这个视频做有画面证据的逐镜分析，并核对完整作者提示词及来源：<视频链接>
```

完成拉片后会主动询问是否提交到镜界草稿。明确要求提交时直接执行；只做分析或修改 skill 不会创建镜界案例。保存和修改操作只影响镜界编辑稿，发布仍在镜界网站操作。

## 包结构

```text
jingjie-plugin/
  .agents/plugins/marketplace.json       本地插件市场入口
  plugins/jingjie-shot-study/
    plugin.json                        通用插件清单
    mcp.json                           通用 MCP 配置
    .codex-plugin/plugin.json          Codex 兼容清单
    .mcp.json                          Codex MCP 配置
    assets/                            镜界原有图标
    skills/video-shot-study/           当前 skill 及参考文件
```

两份 MCP 配置连接同一个地址；通用格式使用 `streamable-http`，当前 Codex 兼容格式使用 `http`。插件清单的名称、版本、展示信息和地址保持同步。
