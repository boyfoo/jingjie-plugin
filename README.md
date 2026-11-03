# 镜界拉片插件

版本：0.5.3。提供完整视频拉片与视频反推提示词两个技能。video-shot-study 分析已有视频的视听表达及既有文字与画面的对应；video-to-director-prompt 根据视频产出可直接使用的AI生成提示词。插件包含镜界 MCP 连接配置，使用镜界现有图标和主题色 `#fcd535`。

## 完整拉片与提示词反推

| 技能 | 任务与输入 | 主要交付 |
| --- | --- | --- |
| `video-shot-study` · 视频逐镜拉片 | 真实视频及可取得的提示词或脚本 | 镜头记录、视听分析、原文来源核验、文字与画面的深入对应、差异及真实时间证据；按授权保存草稿 |
| `video-to-director-prompt` · 视频反推提示词 | 参考视频＋用户意图，或已有反推任务的新试片 | 完整AI生成提示词、必要的参考图方案；结合导演判断做合理改编及提示词修正 |

说“拉片”时，video-shot-study 完成逐镜分析和提示词与画面的深入对应，无需另行询问是否深入，也不以镜界提交为前提。复用已有素材、来源、镜头和证据，只补所需观察；缺少可用比较文本时说明缺口，不从成片反写要求证明匹配。

用户只要求文字对应、某个镜头或某项手法时，处理选定范围，不强制重跑全片。仅提取、翻译、保存文本或开发界面接口不扩成完整拉片。镜界写入和可选翻译按各自授权执行。

视频反推提示词是独立入口，不是普通拉片默认附加的后续步骤。用户要求“根据这个视频反推可用的AI生成提示词”或在已有反推任务中要求“根据试片修正提示词”时，直接使用 video-to-director-prompt：真实观察 → 理解创作目的与情绪 → 转译动作、摄影、光影等生成要求 → 完整提示词与必要的参考图方案。导演思维用于决定如何表达和合理改编，最终产出仍是制作输入；仅交分析或分镜不算完成反推。无需作者原文，也不默认一比一复刻；新设计不能反过来充当旧试片的原有要求。逐镜分析、拍摄手法与既有文字的画面对照统一使用 video-shot-study。

## 使用前提

- 使用支持本地插件的 Codex，并可在终端调用 `codex` 命令。
- 调用镜界 MCP 时，需要在自己的电脑上运行镜界服务，服务地址须与插件内 `mcp.json` 和 `.mcp.json` 的配置一致；本地拉片、反推与改编不要求先启动服务。
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

本地安装时保持该目录可用。安装后重新打开会话或按客户端提示重新加载插件，确认 video-shot-study、video-to-director-prompt 和所需镜界工具可用，两个技能按请求选择。需要使用镜界服务时，先调用只读的 `jingjie_get_taxonomy` 检查连接。

## 使用

```text
使用镜界拉片，对这个视频做有画面证据的逐镜分析，并核对完整作者提示词及来源：<视频链接>
```

也可只处理已有文字与画面的对应：

```text
使用 video-shot-study 分析这份提示词与视频画面的对应：复用已有镜头和观察，解释各项要求的实际呈现、主体与时空关系、差异、证据时间及判断限制。
```

深入对应复用逐镜证据，覆盖实质文字要求，既解释有效呈现，也说明有依据的偏差和未核部分，不默认另做评分或固定审核报告。分析方法不规定产品数据结构；已授权保存时，按当前 MCP 契约写入同一案例并回读。

从参考视频反推可用的生成提示词：

```text
使用镜界插件里的 video-to-director-prompt，根据这个视频反推完整可用的AI视频生成提示词，结合我的想法做合理改编，并说明需要哪些参考图及各自用途：<视频链接>
```

```text
用 video-to-director-prompt 根据这次试片修正生成提示词：核对实际提示词和上传图片，保留我认可的段落，区分描述遗漏与已写但未实现，只修改需要变化的部分。
```

反推技能区分原片观察、分析判断和主动改编。参考图按职责和阶段使用，文件名与 `@图片N` 对应；人物身份与特写需要分别控制时拆分身体和头部设定。需要交付制作包时，提供完整提示词、参考图及版本一致的HTML/TXT；反推过程中的局部咨询不自动重做全部文件或提交镜界。

完整拉片及可完成的深入对应交付后，会在用户尚未决定时询问是否提交到镜界草稿；已有保存授权时，直接保存本次范围内的拉片与深入分析并逐模块回读，不再单独确认深入模块。只做分析或修改 skill 不会创建镜界案例。保存和修改操作只影响镜界编辑稿，发布仍在镜界网站操作。

两个技能按[源版语言与可选翻译流程](plugins/jingjie-shot-study/skills/video-shot-study/references/languages.md)执行：先取得实际提示词，按真实原语言保存源版并回读，再询问是否翻译。中文交流不改变作者原文语言；已有明确语言选择沿用，不重复问，已有案例不擅自改 `sourceLanguage`。无作者原文的反推任务按用户制作要求确定语言。

需要翻译时，先读 `jingjie_get_translation_context`，再用 `jingjie_save_translation` 保存到同一案例的对应目标语言，保留源版。保存后回读源版和译文全文，不只看 `current`；交付使用用户所需语言的入口。深入对应与文字锚点沿用共用流程，不补写未授权模块，不改媒体、时间或分析结论，不自动发布。仅本地翻译不自动创建案例或上传。

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
    skills/video-to-director-prompt/   视频反推提示词、参考图方案、按试片修正制作输入
```

两份 MCP 配置连接同一个地址；通用格式使用 `streamable-http`，Codex 格式使用 `http`。插件清单的名称、版本、展示信息和地址保持同步。
