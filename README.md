# 镜界拉片插件

版本：0.3.4。以 video-shot-study 为基础拉片主流程，video-prompt-alignment 为用户明确选择后才运行的增强技能。默认先完成拉片及已授权的 MCP 提交，核实成功后再询问是否深入画面与文字的对应。插件包含镜界 MCP 连接配置，使用镜界现有图标和主题色 `#fcd535`。

## 基础拉片与增强能力

| 技能 | 在拉片中的职责 | 分析内容 |
| --- | --- | --- |
| `video-shot-study` · 视频逐镜拉片 | 基础方法与主流程，建立真实画面和镜头依据 | 镜头记录、镜头语言分析、原文来源与作者时间标注、按授权保存草稿 |
| `video-prompt-alignment` · 拉片增强：提示词与画面关系 | 经用户明确同意后，承接基础拉片继续深入；关闭隐式调用 | 全文语义梳理、主体与时空关系、持续设定与阶段变化、具体呈现、差异及判断限制 |

只给视频链接或说“拉片”时，仅运行 video-shot-study。取得提示词不自动启动增强。完成基础拉片、已授权的 MCP 写入及回读核对后，先报告提交结果，再询问“要不要继续深入分析画面与文字的对应关系？”用户明确同意后，才读取和执行 video-prompt-alignment。

增强获同意后复用已有素材、来源、镜头和证据，只补所需观察。未答复或拒绝都不启动；未提交、提交或回读失败时，不提前发起默认深入邀请。用户已明确主动要求执行深入分析时沿用授权，不重复问，也不为此强制提交 MCP。提交、翻译与深入分析分别授权；仅提取、翻译、保存文本或开发界面接口不自动扩大为增强分析。缺少比较文本时说明缺口，不反写要求证明匹配。

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

本地安装时保持该目录可用。安装后重新打开会话或按客户端提示重新加载插件，确认基础技能、可显式调用的增强技能和镜界工具可用。video-prompt-alignment 设置 `allow_implicit_invocation: false`，普通拉片不隐式调用它；用户同意后，基础技能按链接读取增强方法。先调用只读的 `jingjie_get_taxonomy` 检查连接。

如果电脑上已有单独安装的同名 skill 或 MCP，先验证插件中的入口，再按需停用旧入口，避免重复加载。本包不会自动改动原有全局配置。

## 使用

```text
使用镜界拉片，对这个视频做有画面证据的逐镜分析，并核对完整作者提示词及来源：<视频链接>
```

用户同意后，在已有拉片上继续深化；也可由用户直接提出执行请求：

```text
在之前的拉片基础上，用 video-prompt-alignment 深化提示词与画面的关系：复用已有镜头和观察，解释各项描述怎样体现、涉及哪些主体和阶段、彼此如何配合，以及差异和判断限制。
```

增强技能沉淀分析方法，不规定字段名、数据结构、固定报告格式或产品功能。时间、对象与空间关系在有依据时作为分析证据保留；前后端按实际项目需要设计存储、接口与展示。获授权后的增强发现承接原拉片成果，既解释表达与关系，也指出有依据的偏差，不默认另做审核报告或评分。同意深入本身不自动授权再次写入已提交的草稿。

完成拉片后会主动询问是否提交到镜界草稿；明确要求提交时直接执行。基础拉片提交及回读成功后，再按上述流程询问是否深入。只做分析或修改 skill 不会创建镜界案例。保存和修改操作只影响镜界编辑稿，发布仍在镜界网站操作。

提示词包含非中文正文时，已要求翻译就直接完成，尚未决定时才在其他已授权任务完成后询问，明确拒绝后不再追问。翻译覆盖全文、前文、时间段和后文，保留原有时间与原文；仅在用户授权保存时，通过 MCP 独立保存译文并回读核对。

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
    skills/video-shot-study/           基础拉片主流程、来源与镜界保存规则
    skills/video-prompt-alignment/     用户同意后运行的提示词与画面关系增强
```

两份 MCP 配置连接同一个地址；通用格式使用 `streamable-http`，当前 Codex 兼容格式使用 `http`。插件清单的名称、版本、展示信息和地址保持同步。
