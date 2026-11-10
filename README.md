# 镜界拉片插件

完整拉片包含核实完整提示词、逐镜分析、逐项核对和保存镜界草稿。用户直接在镜界查看画面和分析依据。

插件附带镜界 MCP 连接配置，使用镜界现有图标和主题色 `#fcd535`。

## 语义拆分与遗漏检查

**要求要拆开核对，引用也要对应到各项的具体文字。** 拆成很多条，不代表已经分析完整；每条都引用整段，也不算准确对应。

- 属性、动作、结果和关系可能分别成立或失败时，分别核对。保留它们的主体、条件和适用范围，不把同一属性的强弱重复拆成多项。
- 结合每项的实际画面、支持证据、反例和缺失信息作判断，不直接套用邻项、整镜或前半段的结论，也不追求各种状态的数量均衡。
- 先拆开独立要求，再按成片实际呈现逐项判断：符合要求判已实现，不符或未呈现判失配；无法判断仅用于声音相关项。写明判断依据，取证尚未完成时先补查。
- 分析后从完整原文检查有没有漏项，再逐项看实际引用是否准确、证据是否对应。判断和引用范围由 AI 结合上下文理解；MCP 校验通过、译文齐全或保存成功，都不能代替这一步。
- 方法和表达可以灵活选择，已有有效证据直接复用。若修改了要求拆分，还要核对已授权目标语言的对应。

详见[分析方法与完成检查](plugins/jingjie-shot-study/skills/video-shot-study/references/judgment-and-coverage.md)和[拆分与查漏示例](plugins/jingjie-shot-study/skills/video-shot-study/references/segmentation-examples.md)。

## 完整拉片

| 技能 | 输入 | 主要交付 |
| --- | --- | --- |
| `video-shot-study` · 视频逐镜拉片 | 真实视频及已核实的完整提示词原文 | 镜界草稿链接，内含逐镜分析、来源、提示词与画面的对应、差异及证据 |

**拉片前必须取得并核实与视频对应的完整提示词。** 缺失或无法确认完整性时，说明缺口，等待用户补充，不开始画面分析，也不新建案例、上传或保存拉片资料。剧情或分镜脚本等资料可以补充说明，不能替代提示词。

完整拉片开始时读取镜界提交指南、可选标签和字段说明，先弄清要保存什么、怎么填写。核实完整提示词后，再逐镜观察并核对画面。分析时一起整理要保存的资料，只对缺失或有疑问的证据补查。

局部镜头分析、文字对照、提取和保存文本按用户指定范围处理。翻译已有案例时，按选定语言翻译整个案例。

## 使用前提

- 使用支持本地插件的 Codex，并可在终端调用 `codex` 命令。
- 连接镜界时，需要先在本机运行镜界服务，地址与插件内 `mcp.json`、`.mcp.json` 的配置一致。本地拉片不要求启动镜界服务。
- 本包包含 skill、MCP 配置和品牌素材；镜界服务源码、启动器和视频处理工具需另行准备。媒体信息探测和抽帧需要可用工具，例如 ffprobe、ffmpeg。

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

本地安装后请保留该目录。重新打开会话，或按客户端提示重新加载插件，确认 `video-shot-study` 和所需镜界工具可用。需要使用镜界服务时，可先调用只读工具 `jingjie_get_taxonomy` 检查连接。

## 使用

```text
使用镜界拉片，对这个视频做完整拉片，保存到镜界草稿并返回链接：<视频链接>
```

也可只核对已有提示词与画面的对应：

```text
使用 video-shot-study 分析这份提示词与视频画面的对应：复用已有镜头和观察，逐项说明实际表现、差异、判断和证据时间。
```

分析会说明哪些要求实现了、哪些存在偏差、哪些证据还不足，并结合镜头解释原因。不默认增加评分或固定格式的审核报告。

完整拉片直接保存到同一镜界案例，重新读取并确认内容正确后交付草稿链接，无需再问是否提交。完整分析在镜界浏览，本地保留分析、素材和证据，无需另做本地浏览报告。

语言按[原语言内容与翻译流程](plugins/jingjie-shot-study/skills/video-shot-study/references/languages.md)处理。分析说明使用提示词原文的语言，交流用用户指定的语言。先交付原语言的草稿链接，再由用户选择一个或多个翻译语言，也可以跳过。每选一种语言，都翻译整个案例，包括完整提示词、所有镜头和深入分析。

保存译文到镜界时，先调用 `jingjie_get_translation_context` 读取原文和填写要求，再用 `jingjie_save_translation` 保存到同一案例，原语言内容保留。保存后核对原文和译文的全文、引用的文字及其位置，再交付对应语言的链接。已经核实的素材、时间和分析结论直接使用；发现问题时，只补查或修改用户允许处理的部分。翻译不包含发布授权；只在本地翻译时，不创建案例或上传。

已选标签、标签分组和对应的视频时间各语言共用，不放进案例译文。

## 包结构

```text
jingjie-plugin/
  .agents/plugins/marketplace.json       本地插件市场入口
  plugins/jingjie-shot-study/
    plugin.json                        通用插件清单
    mcp.json                           通用 MCP 配置
    .codex-plugin/plugin.json           Codex 插件清单
    .mcp.json                          Codex MCP 配置
    assets/                            镜界原有图标
    skills/video-shot-study/            拉片、提示词对照、来源与保存规则
```

两份 MCP 配置连接同一个地址；通用格式使用 `streamable-http`，Codex 格式使用 `http`。插件清单中的名称、版本、展示信息和地址保持同步。
