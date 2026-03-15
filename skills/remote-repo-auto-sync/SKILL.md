---
name: remote-repo-auto-sync
description: Sync local file changes to a remote Git repository, including initializing the current directory as a git repo, creating a matching GitHub repository when no remote exists, generating a concise commit message from the file changes, and pushing to `origin/main`. Use this whenever the user asks to "推送", "同步", "更新远程仓库", "提交到 GitHub", "上传代码", or wants local changes published to a remote repository even if they do not explicitly mention git commands.
---

# Remote Repo Auto Sync

Use this skill when the user wants the current working directory synchronized to a remote repository.

This skill is request-driven. It does not run a background file watcher. Each time the user asks to push or update the remote repository, inspect the current state and sync the latest local changes.

## Expected result

Reply with this format after the workflow finishes:

```text
更新状态：成功|失败
更新远程仓库：<owner/repo 或远程 URL>
更新的hash值：<commit hash；失败时写 无>
提交信息：<本次 commit message；没有新提交时说明原因>
```

If the run fails, add:

```text
失败原因：<一句话说明>
下一步：<需要用户提供的最小信息，或你将继续执行的动作>
```

## Workflow

1. Work in the current directory unless the user names a different path.
2. Inspect the repository state before changing anything.
3. If the directory is not a git repository, initialize it on branch `main`.
4. If no usable remote exists, create a GitHub repository whose name matches the local directory name, then connect it as `origin`.
5. Stage all current changes with `git add -A`.
6. Generate a concise commit message from the actual diff.
7. Commit only when there are staged changes.
8. Push to `origin/main`.
9. Report the final status in the required output format.

## Repository inspection

Check these things first:

- Whether the current directory is already inside a git work tree.
- Current branch name. Prefer `main`.
- Whether `origin` exists and whether it is writable.
- Whether there are local changes to stage and commit.
- Whether GitHub credentials are available through `gh auth status` or environment variables such as `GITHUB_PAT_TOKEN`, `GH_TOKEN`, or `GITHUB_TOKEN`.

If `origin` already exists, use it. Do not create a second remote unless the user explicitly asks for that.

## Remote creation rules

When the current directory is not a git repository, or it is a git repository with no usable `origin`, create and connect a remote repository with the same name as the current directory basename.

Default provider: GitHub.

Preferred creation order:

1. Use `gh repo create` if `gh` is installed and authenticated.
2. Otherwise use the GitHub REST API with an available token.
3. If neither works, stop and explain exactly what credential or tool is missing.

If the remote repository already exists, attach to it instead of failing.

## Commit message rules

Generate commit messages from the staged diff, not from generic placeholders.

Use a short conventional-commit style message:

- `feat:` for new functionality
- `fix:` for bug fixes
- `docs:` for documentation-only changes
- `refactor:` for code restructuring without feature changes
- `style:` for formatting or CSS-only changes
- `test:` for tests
- `chore:` for dependency, config, build, or mixed maintenance changes

Keep the subject line brief and specific. Good examples:

- `feat: add homepage hero section`
- `fix: correct login redirect logic`
- `docs: update setup instructions`
- `chore: sync project scaffolding files`

If the change spans multiple areas, choose the dominant category. If nothing stands out, use `chore: sync local changes`.

## Push rules

- Prefer `git push -u origin main` for the first push on a new repository.
- Prefer `git push origin main:main` for later syncs when tracking is already set.
- Do not use force push unless the user explicitly asks for it.
- Do not rewrite history.
- Do not remove remotes or reset branches unless the user explicitly asks.

## Authentication and safety

- Never leave tokens embedded in `.git/config` or in the saved remote URL.
- If a one-off authenticated URL must be used for a push, immediately restore the remote configuration afterward.
- Prefer temporary headers, credential helpers already configured by the user, or `gh` authentication.
- If a command needs network access or higher privileges, request escalation instead of working around the sandbox.

## No-change behavior

If there are no local changes to commit:

- Do not create an empty commit unless the user explicitly asks for one.
- Verify whether the local branch is already aligned with `origin/main`.
- Return `成功` with the current `HEAD` hash and a message such as `无新增提交，远程已是最新`.

## Failure handling

On failure, report the most relevant blocker:

- Missing GitHub credentials
- Remote creation denied or unauthorized
- Push rejected by the remote
- Missing `user.name` or `user.email`
- Merge or branch mismatch that requires user confirmation

Keep the failure message short and actionable.

## Example triggers

- `帮我推送到远程仓库`
- `更新远程仓库`
- `把本地修改同步到 GitHub`
- `提交并上传当前项目`

