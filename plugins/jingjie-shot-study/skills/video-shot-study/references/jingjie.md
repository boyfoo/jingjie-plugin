# 镜界 MCP 分模块保存与修改

仅在用户要求上传、提交或修改镜界案例时执行。修改 skill、讨论流程或只做拉片，不授权创建或改动案例。一个视频对应一个案例；各模块直接保存编辑稿，发布由用户在网站操作。

## 1. 发现当前契约，读取分类体系

- 先发现当前工具，读取 MCP 资源 `jingjie://submission-guide`，再调用 `jingjie_get_taxonomy`。工具参数、限制和字段规则以当前返回为准，不沿用旧的整案导入或万能 patch 结构。
- 已有保存授权时，在观察与整理资料前读取类型、分组及选项、`caseFields`、`shotFields` 和 `rules`，据此区分固定选项与自由文本。若先完成拉片才收到提交要求，复用画面记录，读取体系后再做映射，不必重做观察。
- 分组选项保存 `option.value`；`option.id` 用于标识，`option.label` 用于显示，不用它们代替 value。镜头固定字段使用 `shotFields` 中对应字段的合法字符串；不要直接拿案例分组选项替代镜头字段自己的选项集。
- 分类库的 `revision` 与案例的 `revision` 含义不同，不可互换。库有分页时读完相关页；分类值被拒绝或发现库发生变化时重新读取。
- 没有分类读取工具时检查可用资源及模板。仍无法获取就明确说明缺口，保留已有值、省略无法核实的归类，不猜 ID、不用空数组代替未知项；其他已授权模块可继续。不要把未归类案例称为已完整交付。

## 2. 默认执行顺序

新案例按下表录入。已有案例先读相应区块，直接修改本次涉及的模块，不重新创建、不重传未变媒体。每步保存后只回读本步区块或对象；按接口要求使用当前案例 revision。

| 顺序 | 工具与操作 | 保存内容、依赖与回读 |
| --- | --- | --- |
| 1 | 读取流程指南与 `jingjie_get_taxonomy` | 在分析、整理前取得字段规则和当前分类体系；只修改提示词时不顺带重新归类。 |
| 2 | `jingjie_create_draft`，或复用已有 caseId | 新建传 `{requestId,kind,title?}`，kind 为 `video` 或 `image`；保留返回的稳定 caseId、revision 和入口。已有案例先 `get_draft(section:"summary")`。 |
| 3 | `jingjie_presign` → PUT → `jingjie_save_media_info` | 上传主素材，保存公开 src／image；视频保存真实 durationSeconds 及已知 metadata。带时间的提示词和镜头依赖已保存的真实视频时长。回读 `media`。 |
| 4 | `jingjie_save_prompt` | 完整原文、前文、时间行、后文四字段整组保存；回读 `prompt` 逐字核对。时间行独立于镜头，优先完成提示词模块。 |
| 5 | 逐条 `jingjie_save_shot` | 一个镜头一次：时间、首尾帧、画面资料、分析、叙事、声音、台词、转场、来源镜头提示词，以及已核实分类。回读 `shots` 并指定 itemId。人物未建时先省略 subjects。 |
| 6 | `jingjie_save_case_info` | 标题、简介；只传本次需修改字段，回读 `case_info`。 |
| 7 | `jingjie_save_analysis` | 案例整体分析，独立于逐镜分析；回读 `analysis`。不加入整篇统计、质量检查或审核字段。 |
| 8 | 逐条 `jingjie_save_cast_member` | 一个人物一次：稳定 id、名称、图片、备注；回读 `cast` 并指定 itemId。没有人物资料时不虚构人物。 |
| 9 | `jingjie_save_classification` | 按当前体系保存案例 type、tags、tagValues、emotion、lighting、movement，回读 `classification`。只选择有画面或可靠来源依据的项。 |
| 10 | 按需补充 `jingjie_save_shot` | 用同一镜头 id 补人物 subjects 或尚未完成的分类；人物 ID 必须已存在。第5步已保存且核对的分类不重复提交，也不建立第二套画面分类。 |
| 11 | `jingjie_get_draft(section:"summary")` | 核对最终版本、状态、镜头／人物数量及模块完成情况；有差异再读相应区块，不默认回读整案。无需最后一次大提交。 |

这是完整新案例的默认顺序。资料尚未取得时明确记录未完成模块，继续其他满足依赖且已授权的操作；不能为填满步骤编造资料。仅改一个模块时不机械执行整套流程。镜头和人物图片可按需要另批上传，不必在创建前上传全部图片。

## 3. 保存契约、版本与工作记录

`save_*` 使用 `{caseId,revision,data,source?}`，不能直接把本地原始 `shots.json` 当作 data。成功返回 module、savedFields、clearedFields、warnings、新 revision 等回执，不返回完整草稿。

- 同一案例的各模块共享 revision。保存应顺序执行：上一操作成功 → 保留新 revision → 回读本模块 → 以回读所得当前 revision 执行下一操作。不要并发写不同模块；独立素材 PUT 可以并发。
- `get_draft` 默认只读 summary，返回 `data` 与 revision，不再按旧响应的 `draft` 路径取值。按当前 section 的实际 data 结构读取；`shots`、`cast`、`source` 可用 itemId 限定单条，`all` 仅在确需全量资料时使用。
- 除整组提示词外，省略字段表示保留；空字符串或 [] 明确清空。metadata 单字段 null 清除参数；未知音轨情况应省略，不能写成 false。
- 镜头 facts、镜头及案例 tagValues 按键合并，其他数组整体更新。修改一个分组时带齐该组应保留的值；修改 analysis 或 subjects 时带齐该对象目标数组，不用只追加一项的数组覆盖旧内容。
- 镜头和人物按 `data.id` 新增或修改，沿用稳定 ID；`position` 为从0开始的最终位置，省略时保留原序，新条追加。保存单条不会替换其他镜头或人物。
- 本地保留案例 caseId、最新 revision、模块／对象 ID、本次目标内容及保存回执。新建 requestId 与各上传批次 requestId 分开记录；submissionId 是上传批次 ID，不是 caseId。
- 原帖地址、原始资料、完整性限制和观察依据放入对应模块的私有 `source` 或本地记录。source 不进入公开案例或 Sites 导出，也不能替代实际编辑稿字段；不把来源解释追加进作者原文。

## 4. 素材上传与恢复

先检查路径、真实字节数、类型与 MIME。每个上传批次生成并保留 requestId；files 为 `[{localName,kind,mime,size}]`，localName 是输出目录内唯一相对路径。数量和大小限制以当前指南为准，不上传无关文件。

使用返回 uploadUrl 以 PUT 发送原始文件字节，原样携带 headers（含 public-read）；不用 FormData，不传路径或 URL 文本作为正文。确认 PUT 成功后，才在媒体、镜头或人物模块引用公开 mediaUrl。uploadUrl 只用于上传，不充当永久素材地址或原始来源。

中断后先 `jingjie_get_submission_status({submissionId})`：uploaded 跳过，awaiting_upload 继续上传，unavailable 保留标识稍后查询，invalid 按原因修正素材并用新 requestId 申请，禁止覆盖原对象。地址过期时沿用原 requestId 和相同 files 调用 presign；改变清单须新批次。旧批次中的首次保存结果是历史记录，当前案例状态和 revision 仍须 get_draft 获取。

新建结果不确定时，沿用创建 requestId 与相同参数重试以恢复同一案例；不要换 ID 重建。修改或补充资料复用 caseId 和现有媒体。替换视频 src 会重置旧时长与媒体参数，应同时提供新视频真实 durationSeconds 和已知 metadata；旧时间若超出新视频范围，先核验并调整受影响提示词或镜头，不能自动缩放、截断或沿用旧测量值。

## 5. 提示词整组保存

`jingjie_save_prompt.data` 四字段均必填，创建与修改层级一致：

| 字段 | 内容 |
| --- | --- |
| prompt | 从来源保留的完整有效原文，含所有前后内容、顺序、段落、换行和空白。 |
| promptPrefix | 第一段明确时间之前的原文；无时间戳，不绑定第一个镜头。 |
| promptLines | `[{start,end,text}]`，每行保留对应标题、完整正文及空白，按原视频绝对秒数保存。 |
| promptSuffix | 最后一段时间内容之后的原文；无时间戳，不绑定最后一个镜头。 |

- 不分四次写，不逐行保存时间提示词。修改已有提示词前先读 prompt 区块，组装四字段完整目标值；未修改部分沿用当前值，不能用空值占位而清空已有资料。只补前后文时也保留全文与时间行。
- 新案例确实无资料的部分才用空字符串或 []；无明确时间的原文仅放 prompt，其他三部分为空。图片也只允许 prompt，其余为空。只有片段时明确来源和完整性限制，不把片段描述成完整作者原文。
- 全文直接来自保留的来源，不用前文、时间行、后文重拼结果替代。四字段不会自动互相生成，整组写入仍需核对内容对应性。
- 时间行满足有限数值、`0 <= start < end <= 视频真实时长`、起点有序且不重叠，允许空档与跨镜头。保留作者时间，不拿实际切点替换，不用标称总时长、开头／结尾描述补造区间。
- 分界不清或有无法归属的无时间文字时保留全文并说明拆分限制，不静默挪动、吞掉末尾约束或相关空白。不混入页面按钮、助手分析或未经核实的复现提示词。
- 作者时间与实际时长不一致时先回查素材版本，保留作者原文，单独核验播放映射。仅在确认微小尾部差异且任务包含播放对齐时处理映射终点；不机械缩放或截断。存储使用原始精度，展示最多两位小数。
- 保存后回读 prompt，逐字比较全文、前文、后文及每行 text，核对全部起止时间与数量；不能只核对总长度或行数。页面切换、高亮、跟随滚动须另行实测，接口保存成功不证明页面行为已验证。

## 6. 镜头、人物与分类字段

每条镜头的概述、分析和固定选项分开保存。先确认画面判断所属的分析维度，再选择字段；字段合法性与语义正确性均须满足，单个固定值不能替代完整分析。当前主要映射如下；如新版本变更，以最新 taxonomy 和工具 schema 为准。

| 原始拉片资料 | 模块／网站字段 |
| --- | --- |
| 原视频、真实时长、测量参数 | save_media_info.data.src、durationSeconds、metadata.width／height／fps／hasAudio |
| frame、可见内容 | save_shot.data.summary |
| size、camera | facts.景别、facts.运镜，仅填对应合法固定值 |
| 构图、光影选项 | facts.构图、facts.光影，仅填对应合法固定值 |
| 长段景别、运镜、构图、光色说明与 note | analysis:[{label,text}]；叙事作用可用 narrative |
| rhythmNote | narrative；不能塞进只接受固定选项的 rhythm |
| audio 中来源提供的台词 | dialogue；助手声音分析放 sound，不混作原始台词 |
| transitionIn | transition，仅填合法固定值，详细衔接依据放 analysis |
| 人物 description／备注 | save_cast_member.data.note；人物图片用 image |

镜头其他资料按需保存 start、end、image、endImage、title、category、rhythm、transition、onscreenText、sound、imagePrompt、videoPrompt。未知台词、模型参数及镜头提示词省略；不生成替代尾帧，不把整片作者原文复制为每镜提示词。

- 景别、运镜、构图、光影只放 facts，不再同时写进镜头 tagValues。固定字段不能接长句或自行拼接多个选项；单选字段仅在同一分析维度内取有依据的主导值，并在 analysis 解释镜内变化，无法确定则省略。不同维度可各自成立，不因单选限制删减其他有依据的判断。
- 案例 type／emotion／lighting／movement、镜头 category／rhythm／transition 及分组值按各自字段规则选择。镜头 tagValues 只用 shotFields 允许的分组，不将案例全部分类直接继承给每个镜头。
- 案例 tags 若 caseFields 明确允许自由文本，可添加有证据的补充关键词并避免同义重复；不把它当作分组类型未完成时的替代。不要推测制作模型或为了填满字段随意选项。
- 映射须保持含义和分析维度一致。选用有证据支持的上位类别时，保留更具体的判断并说明粒度差异；仅有表面相似或属于另一维度的选项不能代替原判断。没有准确选项时，在支持该含义的合法标签或 analysis 中保留原判断，并说明哪个字段无法准确表达；不把库中缺项改写成画面结论。
- 确需补充库中概念时，先查同义项、确认维护授权及新增能力；没有能力就说明具体缺口，不造分类标识。修改既有记录时，不为绕过字段限制而自动清空原值；只有用户要求替换／清除，或核验证明原值错误时，才在本次授权范围内修正并说明原因。合理的未知留空与因工具失败未完成归类要明确区分。
- subjects 仅引用已保存人物 ID；可提前录入人物，或在默认第8步后回到同一镜头补关联。不要为补资料重建人物或镜头。
- 删除、重排仅在任务要求时进行。删除使用 `jingjie_delete_shot`／`jingjie_delete_cast_member` 与 itemId、当前 revision；仍被 subjects 引用的人物先解除或调整引用。不要用省略对象或空数组暗示删除。

## 7. 回读、失败与完成条件

写入前核对关键标签与画面依据、说明和代表帧是否一致，检查是否混淆运动来源、衔接形式、视觉手法或叙事作用，以及是否因字段限制改变或遗漏判断。这项语义核对在内部完成，不新增案例审核章节。

每步比对 savedFields、clearedFields、warnings 和本次预期，再回读刚写入的区块或 itemId，确认内容实际落盘。保存完整提示词时必须比较四字段；镜头要核对时间、首尾图、长段分析、固定选项与关联，并检查字段之间是否矛盾。回读一致只证明内容按目标保存，还需上述语义核对。

- 非法字段、枚举或分类值会拒绝本次保存。按错误中的字段路径、原因和合法选项修正，必要时重读 taxonomy／schema，再重试该模块或对象。禁止通过删掉用户要求的内容来换取成功；长段文字改放正确字段。
- 409 时读取最新相关区块和 revision，对照本次目标合并；不要只替换版本号后重发旧对象。无法确定合并结果时保留冲突并说明，不覆盖他人修改。
- 超时、断线或回执不明确时先按区块读取。已生效则继续，否则用确认后的 revision 重试同一 caseId／对象 ID；只有上传批次查 submission status。相同故障且没有新线索时停止重复写入，保留工作记录并报告未完成模块。
- 回读缺失、非预期清空或格式不一致时，继续在该模块修复并回读；不能用“接口返回成功”代替内容一致。其他独立且已授权模块可继续完成。
- 最后读取 summary 确认最新 revision、状态和完成情况。某模块尚未核实，不报告整案已完整保存；整案 all 只在差异无法定位时使用。

结尾简短报告实际保存的模块、最新 revision、当前状态及可访问入口，明确仍缺的分类或来源。MCP 只改编辑稿；当前 status 即使为 published 或 offline，也不表示本次编辑更新了公开快照，不能声称已经发布。