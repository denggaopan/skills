# Claude Code and Codex compatibility

Read this reference when installing the skill, choosing platform tools, or
creating a worktree without a native helper. The shared workflow is in
`../SKILL.md`; this file does not add required tools or plugins.

## Discovery and invocation

Keep the directory name and frontmatter name as `fast-do`. Install or link the
whole directory, including `references/` and `agents/`, so relative links work.
This repository's `skills/fast-do/` is the source directory; a plain `skills/`
folder is not automatically discovered by every client.

| Client | Skill location | Explicit invocation |
| --- | --- | --- |
| Claude Code, project | `.claude/skills/fast-do/` | `/fast-do <task>` |
| Claude Code, user | `~/.claude/skills/fast-do/` | `/fast-do <task>` |
| Codex, project | `.agents/skills/fast-do/` | `$fast-do <task>` |
| Codex, user | `$CODEX_HOME/skills/fast-do/`, normally `~/.codex/skills/fast-do/` | `$fast-do <task>` |

Use the location supported by the installed client and confirm the skill is
listed after installation. Avoid multiple copies of the same skill within a
client's discovery scope. Resolve reference paths relative to the installed
skill directory, not the task's working directory.

The shared frontmatter uses only `name` and `description`. Codex UI metadata
lives in `agents/openai.yaml`; the Claude workflow does not depend on it.
Automatic selection remains enabled by default. Do not add Claude-specific
`allowed-tools`, `context`, `agent`, or argument substitutions to make the
shared workflow depend on Claude's runtime.

## Select by capability

These names are examples, not a required API. Use only the tools exposed in the
current session, with their actual argument schemas and permission rules.

| Capability | Claude Code, when available | Codex, when available | Fallback |
| --- | --- | --- | --- |
| Read and search | `Read`, `Glob`, `Grep`, shell | File/search tools, shell | Shell search; prefer `rg` if installed |
| Run commands | `Bash` or the supplied shell | `exec_command` or the supplied shell | Another exposed command runner |
| Edit files | `Edit`, `Write` | `apply_patch` | The available file editor |
| Isolate work | `EnterWorktree` | A supplied worktree helper | Git CLI |
| Delegate | `Agent` | `spawn_agent` and companion tools | Staged work in the current session |
| Ask a question | `AskUserQuestion` | An input tool allowed in the current mode | A concise text question when interactive |

Use subagents only when delegation is allowed by the current instructions.
Do not assume an input tool is available outside Plan mode, or that a native
worktree helper exists just because the client supports Git. If no command
runner is available, complete possible edits and report which checks could
not run.

Use syntax for the active shell. In PowerShell, use `-LiteralPath` for paths
where applicable and do not paste Bash variable assignments or heredocs.
Quote paths with spaces, and run commands in the intended checkout using the
tool's working-directory argument or `git -C`.

## Git worktree fallback

Inspect before changing repository state:

```text
git rev-parse --show-toplevel
git status --short
git branch --show-current
git worktree list --porcelain
```

An existing worktree is not necessarily dedicated to this task. Reuse it when
its purpose and branch match the task; do not identify worktrees solely by
whether `.git` is a directory.

If a new worktree is appropriate, select the starting commit separately from
the integration target. Preserve the user's selected feature branch and any
prerequisite commits. Follow an existing worktree location convention, or
choose a unique writable path. For a path inside the repository, check whether
it is ignored before creating it; otherwise prefer a permitted sibling path
or reuse the current checkout. Do not change ignore files just to enable this
workflow unless that change belongs to the task.

The following is a template; replace the placeholders and quote actual paths:

```text
git worktree add -b <task-branch> <worktree-path> <start-ref>
```

Confirm the branch and directory after creation. A new worktree does not copy
uncommitted files, untracked files, dependencies, or local configuration.
Restore only what the task needs using the project's setup process. Respect
the permission boundary if the preferred path or setup command is unavailable.

Before an authorized push, inspect the outgoing commits using the actual
remote-tracking ref and local source ref:

```text
git log --oneline <remote>/<target-branch>..<source-ref>
```

If the remote branch is new, compare against the recorded task baseline.
Fetch when necessary to refresh remote state. A fast-forward is conditional on
ancestry, and push success must be checked independently of local validation.
Keep the worktree if it still contains needed work; cleanup is not a
prerequisite for delivering the result.

## Documentation

- [Claude Code skills](https://code.claude.com/docs/en/skills)
- [Codex skills](https://developers.openai.com/codex/skills/)

Client versions and enabled features can differ. The current session's tool
list and installed client's discovery behavior take precedence over examples.
