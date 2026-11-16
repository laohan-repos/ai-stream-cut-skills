# 宿主和旧接口

仅在工具名称、权限、版本或旧任务查询不匹配时读取。主流程不要求某种提问组件、宿主脚本、动态 shell 注入或子代理机制；自然语言说明影响交付的歧义即可。沿用已明确的项目与设置；本次明确指定范围或明确继续旧任务时才复用相应目标，普通新导出固定全项目。

Codex 可显式用 `$jianba-media-export`，Claude Code 可用 `/jianba-media-export`，两端也可根据 description 识别实际导出意图。讨论 Skill、请求建议或制作新物料不授权实际导出。

文中 `jianba:get_export_context` 等是“剪吧服务 + 工具名”的引用；从当前宿主目录绑定真正可调用的完整名称。常见名称是 `mcp__jianba__get_export_context`、`mcp__jianba__export_video`、`mcp__jianba__get_export_job`；若连接自定义了服务名，使用实际剪吧服务对应的名称，不能借同名工具操作另一应用。发现结果未变时复用，不反复扫描所有 MCP。

共用 SKILL.md 仅使用标准 name/description；`agents/openai.yaml` 是 Codex 展示元数据。Claude 专用的 fork、动态上下文或仅手动调用配置不作为通用流程前提。本流程需要对话中的目标与授权，在当前上下文执行，但不能把前文的单题范围带入新的普通导出。

新的独立导出需要匹配 kind/target 的 `get_export_context`、所选 export 工具及 `get_export_job`。`chapter_only` 没有导出权限；报告实际连接缺项，更新或切换用户已有合适连接后继续，不通过参数提升权限。

新组合导出沿用主流程。旧 `export_composition`、桌面导出及旧发布包任务可能使用不同绑定、目录布局和查询工具；若用户提供的是旧任务 ID，按其真实类型和返回提示查询，可用兼容 `get_video_export` 读取原记录，不能将旧 key 或 manifest 直接用于新任务。不要因为旧文档提到占位文件，就给新独立导出追加目录或媒体.txt。

缺少本次必要能力时准确说明需更新剪吧服务/连接。安装 Skill 不升级 MCP；不改用固定多语言、多封面或多平台旧发布流程替代基础导出。
