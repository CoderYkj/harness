# 50 - Git

## 分支

- `vibe/<slug>`（Agent 专用）
- `feat/<slug>`
- `fix/<slug>`
- 从 `main` 拉出，PR 回 `main`。

## 提交信息

Conventional Commits：

```text
feat(ui): add settings panel toggle
fix(scene): resolve null label on ready
test(ui): cover primary button signal
chore(ci): pin godot version
```

## 规则

- 一个提交只做一件事。
- 提交前必须通过 `verify`。
- 不提交 `.godot/`、`.import/`、导出产物、密钥。
- 不 `--no-verify`。
- 不 `git push --force` 到共享分支。

## Agent 默认行为

- 默认不 commit、不 push、不打 tag、不导出发布。
- 用户明确要求时才执行，且提交前展示 `git diff --stat` 与 `git status`。