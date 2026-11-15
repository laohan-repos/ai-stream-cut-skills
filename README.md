# 剪吧剪辑 Skills

通过剪吧 MCP 在 Codex 和 Claude Code 中完成 AI 教程精剪与局部修改。两个 Skill 都要求读取真实项目状态、核对原片声画、审阅预览，并按用户授权应用修改。

## 选择 Skill

| Skill | 适用任务 | 调用示例 |
| --- | --- | --- |
| [jianba-ai-tutorial-edit](skills/jianba-ai-tutorial-edit/SKILL.md) | 对教程话题或素材做整体精剪，清理重复讲解、无信息的机器执行和等待，保留完整教学步骤。 | 使用 jianba-ai-tutorial-edit 精剪当前 AI 教程，节奏自然偏紧，保留关键操作与结果，核对后应用。 |
| [jianba-local-edit](skills/jianba-local-edit/SKILL.md) | 修改已定位的原话、时间段、选区或接点，完成恢复、删减、局部倍速与衔接修正，沿用范围外的剪辑决定。 | 使用 jianba-local-edit 将当前选区恢复为原速，核对衔接后应用，保留其他剪辑。 |

局部修改可独立使用，无需先执行全片精剪。只需要建议时，在请求中说明“先给方案，不应用”。

显式调用时，Codex 使用 `$jianba-ai-tutorial-edit` 或 `$jianba-local-edit`；Claude Code 使用 `/jianba-ai-tutorial-edit` 或 `/jianba-local-edit`。

## 使用前提

- 已运行剪吧，并在实际使用的 Codex 或 Claude Code 客户端中连接剪吧 MCP；本仓库不负责安装或配置 MCP。
- 剪吧中已有目标项目与素材，MCP 具备相应的读取、预览和编辑权限。工具与参数以客户端实时提供的 Schema 和工作流指南为准。
- 客户端能够实际读取并核对音频与画面。安装 Skill 不会省略声画审阅：媒体路径、`ready` 状态或交付完成均不代表已经听看。能力缺失时，应说明缺口并保留未审阅草稿。

## 安装到 Codex 和 Claude Code

以下命令适用于 macOS / Linux，使用 `git` 和系统自带的 shell 命令。先进入你准备长期保留仓库的目录，再执行：

```sh
git clone https://github.com/laohan-repos/ai-stream-cut-skills.git
cd ai-stream-cut-skills
SKILLS_REPO="$(pwd -P)"

for skill in jianba-ai-tutorial-edit jianba-local-edit; do
  if [ ! -f "$SKILLS_REPO/skills/$skill/SKILL.md" ]; then
    printf '未找到 Skill，跳过：%s\n' "$SKILLS_REPO/skills/$skill"
    continue
  fi
  for skills_dir in "$HOME/.agents/skills" "$HOME/.claude/skills"; do
    mkdir -p "$skills_dir"
    target="$skills_dir/$skill"
    if [ -e "$target" ] || [ -L "$target" ]; then
      printf '已存在，跳过：%s\n' "$target"
    else
      ln -s "$SKILLS_REPO/skills/$skill" "$target"
    fi
  done
done
```

Codex 的入口位于 `~/.agents/skills`，Claude Code 的入口位于 `~/.claude/skills`。两端通过绝对路径软链接共享仓库中的同源文件，不复制内容，也不改动全局配置。已有同名目录、文件或软链接会跳过；如需替换，请先检查并自行处理原入口。

仓库需要保留在原位置；移动或删除后，软链接会失效。若已克隆仓库，直接进入该仓库目录，从 `SKILLS_REPO=...` 一行开始执行即可。

## 更新

在仓库目录中执行：

```sh
git pull
```

软链接指向的文件会随仓库更新，两端无需重复复制安装。
