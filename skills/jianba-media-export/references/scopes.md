# 范围与已有物料

仅在多话题、组合、指定字幕文档、物料筛选或范围有歧义时读取。工具名沿主文件绑定到实际剪吧服务，参数取实时 Schema。

## 目标选择

| 用户意图 | target |
| --- | --- |
| 当前剪后全片 | `{type:"project"}` |
| 一个话题 | `{type:"topic",topicId}` |
| 已保存组合 | `{type:"composition",compositionId}` |
| 指定话题分别导出 | `{type:"topics",topicIds:[...]}` |
| 所有真实话题各导一份 | `{type:"all_topics"}` |

`topics` 按给定顺序分别输出，不拼接；`all_topics` 也不是全片。每批最多100个真实话题，没有话题时不把全片冒充逐话题。使用 `target` 后省略旧 `scopeId/topicIds`。

“当前话题”从 active 编辑器取 projectId/scopeId；普通编辑器上下文没有 compositionId，也不会把播放选区自动变成导出范围。已知组合 ID 直接复用；仅有组合名称时，先用已有项目中的组合列表识别，缺列表才读取必要的项目/组合上下文，不为查 ID 通读转写。多个匹配仍不明确时只问目标。

“这几题合成一条”先识别同顺序、同引用的已保存组合，按 composition 统一导出。没有合适组合时，该请求可授权建立对应组合：进入实时 `composition` 指南，按用户指定顺序建立、真实预览并保存，只保留原话题的剪辑决定。若仅要导出现有组合，不重新选材、制造片头或重做预览。明确顺序直接采用，顺序仍不清才询问。

已保存组合也用 `get_export_context → export_video → get_export_job`。`compositionId` 必须来自真实项目；新链的 `exportContextKey` 与旧 `export_composition` 的 `compositionContextKey` 不互换。

## 字幕来源

独立字幕默认 `source:{kind:"screen"}`，导出目标当前生效的最终画面字幕。沿用文字、时间与人工修改，不应用未生效草稿。字幕文件与烧录字幕是分别选择的产物，导出 SRT 不改变视频字幕开关。

- 话题读取自身已应用字幕。全片在继承模式下汇集话题字幕，显式全片覆盖优先；全片覆盖不会反向改成话题字幕。
- 组合按保存 items 的顺序、裁切和重复引用拼接各片段已应用字幕；不要用全片覆盖代替组合各片段来源。
- 用户明确选择已有文档才用 `document/documents`；保持文档 ID、匹配范围与有效来源基准。语言字段是标签，不执行翻译。组合目前使用 screen，不能因 Schema 含 compositionId 就推断任意字幕文档可用于组合。
- `stale` 时保留人工内容并报告来源问题，不恢复自动字幕或重跑 ASR 来消除阻塞。

## 封面与平台资料

仅处理已登记且适用于目标的物料。需要识别保存的名称/ID 时，分页读取 `list_export_covers` 或 `list_platform_materials` 默认摘要；只有查看已有文案确有必要才取 full，受 maxBytes 约束。导出请求本身不需要读取完整文案或图像字节。

`coverIds/platformIds` 会与目标适用范围取交集；project 可包含全部登记物料，话题可能包含全局共享项。输入 IDs 不保证全部实际选中，按上下文数量与最终结果核对；不匹配时说明具体范围，不借扩大 target 包含其他话题。

封面保留保存原图；平台 `format` 默认为 markdown，可按用户要求使用 json，保持上下文与执行一致。导出平台资料保存文案、元数据和素材引用，不下载引用素材、上传平台或制作完整发布包。
