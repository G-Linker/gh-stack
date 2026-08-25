# Layer 1 — `stack-mock-1`：在 stack 顶上加第二层

当前 stack 结构（自下而上）：

```
main (trunk)
└── feat/stack-mock     ← bottom，PR base = main
    └── stack-mock-1    ← 你现在看的这一层（current）
        └── stack-mock-2
            └── stack-mock-3
```

## 这一层做了什么

在已经有 commit 的 bottom 之上，用 `gh stack add` 长出下一条分支。`add` 必须在 **当前 stack 的 top** 上执行。

## 操作步骤

```bash
# 确认还在 top（此时应是 feat/stack-mock）
gh stack view
git branch --show-current

# 写本文件后，一条命令完成：暂存全部改动 + commit + 新建顶层分支
gh stack add -Am "docs: add gh stack mock (layer 1)" stack-mock-1

# 等价的拆开写法：
#   gh stack add stack-mock-1
#   git add docs/gh-stack-mock/01-add-second-layer.md
#   git commit -m "docs: add gh stack mock (layer 1)"
```

`-A` 会把未跟踪文件也 stage 进去；`-m` 提供 commit message；最后的 `stack-mock-1` 是新分支名。

## 预期结果

- 当前分支变成 `stack-mock-1`
- 本文件只存在于这一层及之上，不会出现在 `feat/stack-mock` 的 diff 里
- 提交到 GitHub 后，这一层 PR 的 base 是 `feat/stack-mock`，reviewer 只看到本文件
