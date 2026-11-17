---
name: jianba-material-assembly
description: "通过剪吧 MCP 把多个视频、图片和音频按一句目标实际编排为可预览、可撤销、可导出的成片，并支持一句话局部修改。用于多素材混合成片；不用于已有教程整体精剪、已定位片段局部删改、讨论或开发 MCP/Skill。"
---

# 剪吧混合素材成片

从用户目标形成有主线的粗剪成片。用户给一句目标即可开始：结合素材选择原声、现有配音或音乐主线，允许混音，不预设每种音轨都要有，不要求所有素材用完。保留明确必用、排除、时长、画幅和顺序要求；缺少不影响起步的信息时自行作合理选择，只有实际阻塞成片的歧义才询问。

## 定位与节省读取

使用当前客户端提供的剪吧 MCP；Codex 与 Claude Code 都按实际工具 Schema 操作，工具短名仅用于定位。默认连接和可选的 `--toolset assembly` 精简连接均可；本 Skill 不自行更改客户端配置。首次读取 `get_workflow_guide(workflow=assembly)`，本会话已读且未变则复用。工具缺失或 MCP 未连接时指出具体缺项，不绕过应用改项目文件或自动配置客户端。

用户给新素材时用 `import_assembly_media` 建立独立素材库；给文件夹时先用现有文件工具列举真实受支持文件，再传各媒体路径，不能把文件夹当单个媒体或编造文件名。加入明确的已有项目则绑定其最新 `expectedVersion`。已有库先 `get_assembly_context(section=summary)`，再读相关 `assets` 页。导入、索引与预览按返回 `waitQuery` 用 `get_assembly_context(projectId,jobId,waitMs=20000)` 长轮询轻状态，完成后按 `readQuery` 读内容或预览；超时续等同任务，不短间隔反复查询，也不调用旧 `wait_media_job`。固定返回的目标 `projectId`，不把导入接到旧时间线，不重置人工剪辑、字幕、保护或效果。此独立作品的原声和 ASR 选择依素材与本次计划决定，不由旧项目的剪辑模式推断。

先用轻资产摘要、角色、时长及 `get_assembly_evidence(mode=overview)` 的真实概览帧筛选；长视频不必为初筛逐窗解码整片音视频。概览默认跨视频6帧，可指定最多8个时间点，没有 WAV，稀疏采样不证明连续内容。图片可用 overview 读取真实图；纯音频跳过 overview，直接 window 试听。选中候选后再 `mode=window` 核对相关声画。`asset.index` 已准备的技术索引和 ASR 直接复用；需要时 `prepare_assembly_index` 只准备候选，确需原话定位或理解口播才显式 `transcribe=true`，再读相应资产的 `transcript` 页；图片、器乐和纯视觉片不默认做 ASR。索引不调用语言模型，不代表语义已经理解。

沿返回 `nextQuery` 分页并保持 `libraryKey`；方案分页同时保持 `expectedPlanRevision`，摘要或省略字段仍是未知。会话只记目标、素材库键、已读页、所选源区间、真实证据、方案 revision、任务及续读请求，不反复贴转写、整套计划或媒体数据。源未变的证据和索引可复用，项目写版本与方案 revision 分别记录。

## 源素材与编排

选中后用 `get_assembly_evidence(mode=window)` 实际查看图片、视频帧和试听 WAV；`includeMedia=false` 只读信息，不算审阅。`start/end` 与 `timestamps` 都是 `asset_source`，即单个原文件从零开始的秒数，单窗口默认30秒、最多60秒。可在同一连续音频窗口内指定帧时间点；较长原声按窗口补齐，不靠文字替代听感。真实图片、声音与观察写入计划项的 `observation`、`reason`、`evidenceIds`；不编造观察或 ID，路径、转写、技术数据及 `ready` 不能当作已经听看。

原声主线保留完整观点、否定、条件和指代，所用原声音频范围须有连续证据覆盖；重复与停顿按实际听感取舍。配音主线使用实际已有音频并对齐画面，不能把尚未生成的配音写成现成素材。音乐主线按画面节奏编排，检查人声与音乐音量、淡入淡出及尾音。看不清关键操作或产品细节时补取源证据，不把稀疏帧推断成看过连续动作。

`plan.clips` 顺序就是作品画面顺序：视频用源 `sourceStart/sourceEnd`，图片用展示 `duration`。`audioTracks` 的源区间仍是资产秒，`start` 为作品 `output` 秒；`captions.start/end` 也为作品秒。音轨、字幕与画面按同一输出时间对齐。新作品只含明确引用的素材、音轨和字幕，不能声称继承旧时间线的调色、字幕、聚焦或其它效果。

## 保存、核对与局部修改

新作品 `save_assembly_plan(plan=...)` 保存独立草稿，绑定最新 `expectedVersion/libraryKey`。修改已有作品优先用 `patch`，带 `assemblyId/expectedPlanRevision`；先读取目标完整条目，以及与替换范围重叠或指代相关的音轨、字幕，只提交授权变化。`upsert` 必须有条目自身 `item.id`，按整条替换，保留其仍需设置与其它人工决定；默认保持输出时长以保留后续作品秒定位，改变时长时重对齐受影响条目。`clips.order` 若提供须列出修改后完整播放顺序。改变画布或需要整体重编排时才读完整相关方案并提交完整 `plan`。具体实例与输出字段恢复按需读 [例子与恢复](references/examples-and-recovery.md)。

随后 `create_assembly_preview`，先按 `waitQuery` 用 `get_assembly_context` 长轮询到终态，再按 `readQuery` 用 `get_assembly_preview` 并沿 `nextQuery` 读完全部窗口和必要媒体，实际检查片头片尾、全部新接点、原声语义、画面衔接、配音同步、音乐音量及字幕。预算不足继续读取必要窗口，不用省略审阅节省 token。当前预览是完整作品，未改片段缓存可复用，但不能宣称服务已只渲染或只验证改动；返回的全部审阅窗口都要读完。遇问题先改草稿再预览，不把路径、终态或交付记录当作观看结果。

审阅通过且用户要求实际成片时，以同一 `assemblyId/jobId/expectedPlanRevision` 和最新 `expectedVersion` 调用 `apply_assembly_plan`，可用项目 `undo` 撤销。只要方案或预览的请求停在相应阶段。素材、库键、方案或版本变化时按返回错误重读受影响部分并重新预览，不只替换版本号提交旧候选。

粗剪即可交付。用户要文件时首次读 `get_workflow_guide(workflow=export)`，目标为 `{type:'assembly',assemblyId}`。视频用 `get_export_context(kind=video)` → `export_video`；用户还要字幕时，独立读取 `kind=subtitles,source={kind:'screen'}` 上下文再 `export_subtitles`，字幕来自此计划。每类使用各自上下文的 `expectedVersion/exportContextKey`，首任务返回 `exportFolderId` 后，后续类别的上下文和提交都携同一 ID，续写同一作品文件夹。沿用应用保存设置，未要求的编码和目录覆盖字段省略。用 `get_export_job` 等到完成，再 `detail=results` 分页核实真实文件。完成后简述主线、主要选材与实际应用/导出状态及剩余问题，不自动增加配音、封面或发布工作。
