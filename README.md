# 镜界拉片插件

版本：0.5.6。提供完整视频拉片技能 video-shot-study，将提示词中可独立核验的要求逐项拆分，对照真实视频判断符合情况、差异及证据。插件包含镜界 MCP 连接配置，使用镜界现有图标和主题色 `#fcd535`。

全部任务由当前主代理独立完成，不使用子代理；长任务由主代理分段处理并统一核验。

## 语义拆分与遗漏检查

- 先明确核验的是成片表现、可测参数还是原文明示的制作事实及其判断尺度。能独立成败的属性、动作、结果和关系分别核验；同一属性的强弱不重复拆项，主体、语境和适用范围完整保留。
- 汇总本项支持、反例和关键缺口后独立选状态，不继承父句、邻项、整镜或半片结论；不额外追索原文未要求的证明。同状态集中有依据即可，不追求比例均衡。
- 决定性反例判失配；尚不能定论且关键缺证才记待核。只有不可再拆的连续程度目标被证据确认部分实现才用 partial，明确数量、阈值或全称条件不满足不能按完成比例判定。
- 每项保留可回查的原文依据、核验含义、实际表现、证据及理由；共享证据可合并说明，关键项深入解释实现方式和偏差影响。完成前查漏拆、漏判、错连与连带判定；MCP 校验、译文齐全和保存成功不能证明原文无遗漏。
- 源版要求拆分变化后，核对所有已授权目标语言的对应；仅修改插件方法不修改已有镜界案例。

完整[方法、原因与完成检查](plugins/jingjie-shot-study/skills/video-shot-study/references/judgment-and-coverage.md)及[八组拆分与查漏示例](plugins/jingjie-shot-study/skills/video-shot-study/references/segmentation-examples.md)随插件分发。

## 完整拉片

| 技能 | 任务与输入 | 主要交付 |
| --- | --- | --- |
| `video-shot-study` · 视频逐镜拉片 | 真实视频及已取得并核实的完整提示词原文，脚本可作补充 | 镜头记录、视听分析、原文来源核验、文字与画面的深入对应、差异及真实时间证据；按授权保存草稿 |

拉片及局部画面分析必须先取得与视频对应、完整且可核实的提示词原文；未取得时说明缺口，停下来等待用户补充，不开始视听分析或新建、上传、保存拉片资料。脚本可作补充，不能替代提示词。

通过前置检查后，video-shot-study 完成逐镜分析和提示词与画面的深入对应，无需另行询问是否深入，也不以镜界提交为前提。复用已有素材、来源、镜头和证据，只补所需观察，不从成片补写要求证明匹配。

用户只要求文字对应、某个镜头或某项手法时，处理选定范围，不强制重跑全片。仅提取、翻译、保存文本或开发界面接口不扩成完整拉片。镜界写入和可选翻译按各自授权执行。

## 使用前提

- 使用支持本地插件的 Codex，并可在终端调用 `codex` 命令。
- 调用镜界 MCP 时，需要在自己的电脑上运行镜界服务，服务地址须与插件内 `mcp.json` 和 `.mcp.json` 的配置一致；本地拉片不要求先启动服务。
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

本地安装时保持该目录可用。安装后重新打开会话或按客户端提示重新加载插件，确认 video-shot-study 和所需镜界工具可用。需要使用镜界服务时，先调用只读的 `jingjie_get_taxonomy` 检查连接。

## 使用

```text
使用镜界拉片，对这个视频做有画面证据的逐镜分析，并核对完整作者提示词及来源：<视频链接>
```

也可只处理已有文字与画面的对应：

```text
使用 video-shot-study 分析这份提示词与视频画面的对应：复用已有镜头和观察，解释各项要求的实际呈现、主体与时空关系、差异、证据时间及判断限制。
```

深入对应复用逐镜证据，覆盖实质文字要求，既解释有效呈现，也说明有依据的偏差和未核部分，不默认另做评分或固定审核报告。分析方法不规定产品数据结构；已授权保存时，按当前 MCP 契约写入同一案例并回读。

完整拉片与深入对应交付后，会在用户尚未决定时询问是否提交到镜界草稿；已有保存授权时，直接保存本次范围内的拉片与深入分析并逐模块回读，不再单独确认深入模块。只做分析或修改 skill 不会创建镜界案例。保存和修改操作只影响镜界编辑稿，发布仍在镜界网站操作。

拉片按[源版语言与可选翻译流程](plugins/jingjie-shot-study/skills/video-shot-study/references/languages.md)执行：先取得完整提示词并核实真实原语言，未取得则停止并等待补充。已授权保存时先保存源版并回读，再询问是否翻译。中文交流不改变作者原文语言；已有明确语言选择沿用，不重复问，已有案例不擅自改 `sourceLanguage`。

需要翻译时，先读 `jingjie_get_translation_context`，再用 `jingjie_save_translation` 保存到同一案例的对应目标语言，保留源版。保存后回读源版和译文全文，不只看 `current`；交付使用用户所需语言的入口。深入对应与文字锚点按上述语言与翻译流程处理，不补写未授权模块，不改媒体、时间或分析结论，不自动发布。仅本地翻译不自动创建案例或上传。

可选翻译包含自由补充标签的显示名，原标签和固定分类值保持不变。

## 包结构

```text
jingjie-plugin/
  .agents/plugins/marketplace.json       本地插件市场入口
  plugins/jingjie-shot-study/
    plugin.json                        通用插件清单
    mcp.json                           通用 MCP 配置
    .codex-plugin/plugin.json          Codex 插件清单
    .mcp.json                          Codex MCP 配置
    assets/                            镜界原有图标
    skills/video-shot-study/           逐镜拉片、深入文字对应、来源与镜界保存规则
```

两份 MCP 配置连接同一个地址；通用格式使用 `streamable-http`，Codex 格式使用 `http`。插件清单的名称、版本、展示信息和地址保持同步。
