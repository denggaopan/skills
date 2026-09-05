# Skills

[English](README.md) | [简体中文](README.zh-CN.md)

A collection of reusable skills for Claude Code and Codex. Each skill lives in its own directory with a `SKILL.md` file, supporting references, and client metadata.

## Available Skills

| Skill | Purpose | Documentation |
| --- | --- | --- |
| `fast-do` | Complete clearly scoped development tasks, verify results, and finish agreed delivery steps with fewer interruptions | [Skill instructions](skills/fast-do/SKILL.md) |

### fast-do

Use this skill to fix bugs, implement clearly defined features, or carry a task through development, verification, and agreed delivery steps. The workflow covers:

1. Check the task scope, repository conventions, and workspace state while preserving existing changes.
2. Work directly, proceed in stages, or delegate to subagents according to task complexity and available tools and permissions.
3. Implement the change and run relevant tests, checks, or verification of the affected workflows.
4. Commit, merge, and push as authorized by the user. Client application tasks also include packaging, artifact verification, and publishing by default.

Client application tasks follow the project's existing release channels and process. For code changes only, explicitly state "only change the code; do not publish" in your request. This skill does not apply to questions, investigation without fixes, or requests to discuss, plan, or review first.

The skill requires no specific plugins and follows the current client's tools, permissions, and repository instructions.

## Installation

Clone the repository:

```sh
git clone https://github.com/denggaopan/skills.git
cd skills
```

Copy or link the **entire `skills/fast-do/` directory** into a skill location supported by your client, including `references/` and `agents/`.

| Client | Project installation (relative to the target project root) | User installation |
| --- | --- | --- |
| Claude Code | `.claude/skills/fast-do/` | `~/.claude/skills/fast-do/` |
| Codex | `.agents/skills/fast-do/` | `$CODEX_HOME/skills/fast-do/`, normally `~/.codex/skills/fast-do/` |

For example, install for the current Codex user with Windows PowerShell (run from this repository's root):

```powershell
$codexSkillRoot = if ($env:CODEX_HOME) {
    Join-Path $env:CODEX_HOME 'skills'
} else {
    Join-Path $HOME '.codex/skills'
}
New-Item -ItemType Directory -Path $codexSkillRoot -Force | Out-Null
Copy-Item -LiteralPath './skills/fast-do' -Destination $codexSkillRoot -Recurse
```

To install for the current Codex user on macOS or Linux:

```sh
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
cp -R skills/fast-do "${CODEX_HOME:-$HOME/.codex}/skills/"
```

These examples are for a first installation. After installing, confirm that your client lists `fast-do`. If a skill with the same name already exists, inspect it before updating, and avoid multiple copies within the same client's discovery scope. This repository's `skills/` directory contains the source; cloning the repository alone does not load the skill into your client.

See the [platform reference](skills/fast-do/references/platforms.md) for more details on platform differences and tool compatibility.

## Usage

Invoke the skill in your client conversation and specify the task and delivery scope.

Claude Code:

```text
/fast-do Fix the login button staying in its loading state after a failed sign-in. Add a regression test. Only modify files; do not commit.
```

Codex:

```text
$fast-do Add theme switching to the settings page. Verify, commit, merge, and push the changes, then package and publish the client using the project's existing process.
```

You can also describe the task in natural language, such as "fast do ..." or "complete this task in one go ...". Automatic skill selection depends on your client's skill discovery and matching behavior.

## Directory Structure

```text
skills/
    fast-do/
        SKILL.md                # Skill entry point and workflow
        agents/
            openai.yaml         # Codex UI metadata
        references/
            platforms.md        # Platform, installation, and worktree reference
```

## License

This repository is licensed under the [MIT License](LICENSE).
