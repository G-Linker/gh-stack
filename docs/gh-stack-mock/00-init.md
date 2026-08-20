# Layer 0 — `feat/stack-mock`：初始化 stack

本仓库是 playground，用来在 GitHub 上模拟 [Stacked Pull Requests](https://docs.github.com/en/pull-requests/reference/stacked-pull-requests)，并可以随意 merge / rebase / 再叠。不包含业务改动。

当前 stack 结构（自下而上）：

```
main (trunk)
└── feat/stack-mock     ← 你现在看的这一层（bottom）
    └── stack-mock-1
        └── stack-mock-2
            └── stack-mock-3  ← top
```

## 这一层做了什么

把 `feat/stack-mock` 收成 stack 的第一层（最靠近 `main` 的 bottom）。

## 操作步骤

```bash
# 1. 确认当前在模拟用的分支上
git checkout feat/stack-mock

# 2. 写本文件并提交（这一层必须有独立 commit，否则对 main 的 PR 会是空 diff）
git add docs/gh-stack-mock/00-init.md
git commit -m "docs: init gh stack mock (layer 0)"

# 3. 把当前分支登记为 stack 的第一层
#    --base 显式指定 trunk，避免交互式提问
gh stack init --base main feat/stack-mock

# 4. 查看本地 stack 状态
gh stack view
```

## 预期结果

- `.git/gh-stack` 会记录这条 stack（本地元数据，不会提交进仓库）
- `feat/stack-mock` 成为 bottom，之后新增的 `stack-mock-N` 都会叠在它上面
- 这一层提交到 GitHub 后，PR base 会是 `main`，reviewer 只看到本文件的 diff
