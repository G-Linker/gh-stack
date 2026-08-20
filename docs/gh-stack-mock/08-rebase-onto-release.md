# Rebase Stack #5 onto `feat/release-3.4.4`

场景：`feat/stack-mock` 本来要对 `main` 合。发布窗口改成走 `feat/release-3.4.4`，整条 stack 必须先变基到这条 release 分支，不能直接合进 `main`。

变基前：

```
main
└── feat/stack-mock   #1   ← PR base = main
    └── … → stack-mock-5
```

变基后：

```
main
└── feat/release-3.4.4          ← 新 trunk（本文件所在分支）
    └── feat/stack-mock   #1    ← PR base 改成 feat/release-3.4.4
        └── … → stack-mock-5
```

`gh stack rebase` 只会叠到 **当前 trunk**。trunk 还是 `main` 时，rebase 不会帮你切到 release。要先改 trunk，再 cascade rebase，再 submit 把底层 PR 的 base 改掉。

## 1. 准备 release 分支

```bash
git checkout main
git checkout -b feat/release-3.4.4

# 写本文件、CHANGELOG、VERSION 并提交
git add VERSION CHANGELOG.md docs/gh-stack-mock/08-rebase-onto-release.md
git commit -m "chore: cut feat/release-3.4.4"

git push -u origin feat/release-3.4.4
```

这一层的 commit 必须出现在 `feat/stack-mock` **下面**，这样底层 PR 的 Files changed 仍然只有 stack 自己的 diff，不会把 release 文件算进去。

## 2. 把 stack 的 trunk 从 `main` 改成 release

`gh stack rebase` 没有 `--onto`。现有 stack 要换 trunk：本地先 unstack，再用 `--base` 把同一串分支认回来。

```bash
git checkout stack-mock-5          # 任意一层，先拿到当前 stack
gh stack unstack --local           # 只删本地 tracking，GitHub 上 Stack #5 还在

gh stack init --base feat/release-3.4.4 \
  feat/stack-mock stack-mock-1 stack-mock-2 \
  stack-mock-3 stack-mock-4 stack-mock-5

gh stack view                      # 最底下应显示 feat/release-3.4.4，不再是 main
```

## 3. 级联变基

```bash
gh stack rebase
```

实际顺序：

1. `feat/stack-mock` rebase 到 `feat/release-3.4.4` 的 tip
2. `stack-mock-1` rebase 到新的 `feat/stack-mock`
3. 一直到 `stack-mock-5`

每一层只 replay **自己相对下一层多出来的 commit**。release 上的 `CHANGELOG.md` / `VERSION` / 本文件会进入 git 历史，但不会出现在 stack PR 的 Files changed 里。

冲突时：

```bash
# 看冲突文件，改完 git add
gh stack rebase --continue

# 或者整段撤销
gh stack rebase --abort
```

## 4. 推上去，改底层 PR 的 base

GitHub **不允许**在 PR 还属于某个 Stack 时改它的 base：

```
Cannot change the base branch because the pull request is part of a stack.
```

所以要先在远端拆掉旧 Stack，改 `#1` 的 base，再重新 link。本地 tracking 会一起被清掉，改完再 `init --base` 认回来。

```bash
# 拆掉 GitHub 上的旧 Stack #5（PR 都还在）
gh stack unstack

# 底层 PR 现在可以改 base 了
gh pr edit 1 --base feat/release-3.4.4

# 用新 trunk 把同一串分支认回来
gh stack init --base feat/release-3.4.4 \
  feat/stack-mock stack-mock-1 stack-mock-2 \
  stack-mock-3 stack-mock-4 stack-mock-5

# 推分支，并把 6 个已有 PR 重新组成 Stack（这次会拿到新的 stack number）
gh stack submit --auto
```

上面几层的 base 仍然是各自的下一层，只有 `#1` 从 `main` 改成 `feat/release-3.4.4`。

## 预期结果

- `gh stack view` 的 trunk 是 `feat/release-3.4.4`
- https://github.com/G-Linker/gh-stack/pull/10 是 release 自己对 `main` 的 PR，**不属于**这条 stack
- https://github.com/G-Linker/gh-stack/pull/1 的 base 不再是 `main`
- `#1` Files changed 仍只有 `00-init.md`（release 文件在 trunk 上，不算进 stack diff）
- `git log feat/stack-mock` 里能看到 release 的 chore commit
- 合 stack 时合进的是 `feat/release-3.4.4`，不是 `main`
- 旧的 Stack #5 被 unstack 后，重新 submit 会得到新的 Stack #11（PR 号码不变）

## 并行的 3a / 3b

`stack-mock-3a` / `3b` 是以 **旧的** `stack-mock-3` SHA 为 trunk 的独立 stack。主链 rebase 后它们会和远程 `stack-mock-3` diverged，需要各自再 `gh stack rebase`（或重新 `init --base stack-mock-3`）。这次 mock 只动 Stack #5。
