# Skills

[English](README.md) | [简体中文](README.zh-CN.md)

适用于 Claude Code 和 Codex 的可复用技能集合。每个技能以独立目录维护，包含 `SKILL.md` 以及所需的参考资料和客户端元数据。

## 技能列表

| 技能 | 用途 | 文档 |
| --- | --- | --- |
| `fast-do` | 连续完成范围明确的开发任务，验证结果并完成已约定的交付步骤，减少确认往返 | [技能说明](skills/fast-do/SKILL.md) |

### fast-do

适合修复 bug、实现明确的功能，以及需要一次完成开发、验证和约定收尾的任务。执行流程包括：

1. 检查任务范围、仓库约定和工作区状态，保留已有修改。
2. 根据复杂度直接执行、分阶段推进，或在工具和权限允许时使用子代理协作。
3. 完成实现，并运行与改动相关的测试、检查或实际流程验证。
4. 按用户授权完成提交、合并和推送；客户端应用默认还包含打包、产物验证和发布。

客户端任务沿用项目既有的发布渠道和流程。如果只需要修改代码，可以在请求中明确写出“只改代码，不发布”。纯问答、仅排查不修复，以及要求先讨论、先计划或先评审的任务不适用此技能。

技能不依赖特定插件，实际执行遵循当前客户端的工具、权限和仓库指令。

## 安装

先克隆仓库：

```sh
git clone https://github.com/denggaopan/skills.git
cd skills
```

将 `skills/fast-do/` **整个目录**复制或链接到所用客户端支持的技能目录，保留其中的 `references/` 和 `agents/`。

| 客户端 | 项目级安装位置（相对于目标项目根目录） | 用户级安装位置 |
| --- | --- | --- |
| Claude Code | `.claude/skills/fast-do/` | `~/.claude/skills/fast-do/` |
| Codex | `.agents/skills/fast-do/` | `$CODEX_HOME/skills/fast-do/`，通常为 `~/.codex/skills/fast-do/` |

例如，在 Windows PowerShell 中安装到 Codex 用户目录（在本仓库根目录执行）：

```powershell
$codexSkillRoot = if ($env:CODEX_HOME) {
    Join-Path $env:CODEX_HOME 'skills'
} else {
    Join-Path $HOME '.codex/skills'
}
New-Item -ItemType Directory -Path $codexSkillRoot -Force | Out-Null
Copy-Item -LiteralPath './skills/fast-do' -Destination $codexSkillRoot -Recurse
```

在 macOS 或 Linux 中安装到 Codex 用户目录：

```sh
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
cp -R skills/fast-do "${CODEX_HOME:-$HOME/.codex}/skills/"
```

以上示例用于首次安装。安装后确认客户端已列出 `fast-do`；如已有同名技能，先检查现有内容再更新，并避免在同一客户端的发现范围内保留多份副本。本仓库的 `skills/` 是源码目录，仅克隆仓库不代表客户端已加载技能。

更多平台差异和工具适配说明见 [平台参考](skills/fast-do/references/platforms.md)。

## 使用

在客户端对话中调用，并写明任务及交付范围。

Claude Code：

```text
/fast-do 修复登录失败后按钮一直处于加载状态的问题，补充回归测试，只修改文件，不提交。
```

Codex：

```text
$fast-do 为设置页增加主题切换，验证后提交、合并并推送；客户端按项目现有流程打包和发布。
```

也可以使用“快速搞定……”或“一口气做完……”等自然语言描述任务。是否自动选择技能取决于客户端的技能发现与匹配行为。

## 目录结构

```text
skills/
    fast-do/
        SKILL.md                # 技能入口与执行流程
        agents/
            openai.yaml         # Codex 界面元数据
        references/
            platforms.md        # 平台适配、安装与 worktree 参考
```

## 许可证

本仓库采用 [MIT License](LICENSE)。
