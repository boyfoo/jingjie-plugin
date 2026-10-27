# 镜界拉片插件

版本：0.4.3。分别提供视频拉片、画面与文字对应分析、视频反推提示词三个技能。video-shot-study 分析已有视频如何表达；video-prompt-alignment 核对已有文字与画面的关系；video-to-director-prompt 根据视频产出可直接使用的AI生成提示词。插件包含镜界 MCP 连接配置，使用镜界现有图标和主题色 `#fcd535`。

## 拉片分析、对应分析与提示词反推

| 技能 | 任务与输入 | 主要交付 |
| --- | --- | --- |
| `video-shot-study` · 视频逐镜拉片 | 分析已有视频如何拍摄、剪辑与表达 | 镜头记录、视听分析、按需核验作者原文与保存草稿 |
| `video-prompt-alignment` · 拉片增强：提示词与画面关系 | 经用户明确同意后，承接基础拉片继续深入；关闭隐式调用 | 全文语义梳理、主体与时空关系、持续设定与阶段变化、具体呈现、差异及判断限制 |
| `video-to-director-prompt` · 视频反推提示词 | 参考视频＋用户意图，或已有反推任务的新试片 | 完整AI生成提示词、必要的参考图方案；结合导演判断做合理改编及提示词修正 |

只给视频链接或说“拉片”时，仅运行 video-shot-study。取得提示词不自动启动增强。完成基础拉片、已授权的 MCP 写入及回读核对后，先报告提交结果，再询问“要不要继续深入分析画面与文字的对应关系？”用户明确同意后，才读取和执行 video-prompt-alignment。

增强获同意后复用已有素材、来源、镜头和证据，只补所需观察。未答复或拒绝都不启动；未提交、提交或回读失败时，不提前发起默认深入邀请。用户已明确主动要求执行深入分析时沿用授权，不重复问，也不为此强制提交 MCP。提交、翻译与深入分析分别授权；仅提取、翻译、保存文本或开发界面接口不自动扩大为增强分析。缺少比较文本时说明缺口，不反写要求证明匹配。

视频反推提示词是独立入口，不是普通拉片默认附加的后续步骤。用户要求“根据这个视频反推可用的AI生成提示词”或在已有反推任务中要求“根据试片修正提示词”时，直接使用 video-to-director-prompt：真实观察 → 理解创作目的与情绪 → 转译动作、摄影、光影等生成要求 → 完整提示词与必要的参考图方案。导演思维用于决定如何表达和合理改编，最终产出仍是制作输入；仅交分析或分镜不算完成反推。无需作者原文，也不默认一比一复刻；新设计不能反过来充当旧试片的原有要求。普通拉片、只分析拍摄手法或只核对文字实现情况，各自沿用对应技能。

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

本地安装时保持该目录可用。安装后重新打开会话或按客户端提示重新加载插件，确认三个技能和所需镜界工具可用。video-prompt-alignment 设置 `allow_implicit_invocation: false`，普通拉片不隐式调用它；用户同意后，基础技能按链接读取增强方法。video-to-director-prompt 保持默认按请求选择。需要使用镜界服务时，先调用只读的 `jingjie_get_taxonomy` 检查连接。

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

从参考视频反推可用的生成提示词：

```text
使用镜界插件里的 video-to-director-prompt，根据这个视频反推完整可用的AI视频生成提示词，结合我的想法做合理改编，并说明需要哪些参考图及各自用途：<视频链接>
```

```text
用 video-to-director-prompt 根据这次试片修正生成提示词：核对实际提示词和上传图片，保留我认可的段落，区分描述遗漏与已写但未实现，只修改需要变化的部分。
```

反推技能区分原片观察、分析判断和主动改编。参考图按职责和阶段使用，文件名与 `@图片N` 对应；人物身份与特写需要分别控制时拆分身体和头部设定。需要交付制作包时，提供完整提示词、参考图及版本一致的HTML/TXT；反推过程中的局部咨询不自动重做全部文件或提交镜界。

完成拉片后会主动询问是否提交到镜界草稿；明确要求提交时直接执行。基础拉片提交及回读成功后，再按上述流程询问是否深入。只做分析或修改 skill 不会创建镜界案例。保存和修改操作只影响镜界编辑稿，发布仍在镜界网站操作。

三个技能都将中文交流与内容语言分开。先依据已核实的作者提示词建议 `sourceLanguage`，让用户确认一次；已有明确选择沿用，源语言未知则先确认并设置。没有作者原文的反推任务依据用户的制作要求建议，不虚构作者语言。确认后，标题、简介、逐镜分析、人物说明、深入对应中的全部新写说明及反推提示词使用源语言，作者引文原样保留。

只完成本次已授权模块，先把源版保存到同一案例并逐模块回读。全部完成后，再展示服务端返回且排除源语言的 `targetLanguages`，询问是否翻译，允许多选或跳过；当前可用语言为 `en` 与 `zh-CN`，以后按服务端列表扩展，不固定两种语言，也不默认同意。已明确选择目标语言时沿用，仍在源版完成后执行。

用户选择后，按目标语言和模块先读取 `jingjie_get_translation_context`，再通过 `jingjie_save_translation` 保存并回读。支持当前契约中的 `case_info`、`prompt`、`shots`、`cast`、`prompt_alignment`；不为翻译新增未授权模块。译文留在同一案例，不改原文、媒体、时间和 graph，不自动发布。仅本地翻译不自动创建案例或上传。完整参数、源版本校验及恢复规则见[源版语言与可选翻译](plugins/jingjie-shot-study/skills/video-shot-study/references/languages.md)。

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
    skills/video-to-director-prompt/   视频反推提示词、参考图方案、按试片修正制作输入
```

两份 MCP 配置连接同一个地址；通用格式使用 `streamable-http`，当前 Codex 兼容格式使用 `http`。插件清单的名称、版本、展示信息和地址保持同步。
