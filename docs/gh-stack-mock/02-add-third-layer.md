# Layer 2 — `stack-mock-2`：继续往上叠第三层

当前 stack 结构（自下而上）：

```
main (trunk)
└── feat/stack-mock
    └── stack-mock-1    ← 这一层 PR base = feat/stack-mock
        └── stack-mock-2  ← 你现在看的这一层（current）
            └── stack-mock-3
```

## 这一层做了什么

演示「连续 add」：每次只在 top 上再长一层。Git 历史是线性的：`stack-mock-2` 包含下面两层的全部 commit，但 GitHub 上这一层 PR 只会显示相对 `stack-mock-1` 的增量。

## 操作步骤

```bash
# 必须停在 top（此时应是 stack-mock-1）
gh stack view

# 写本文件后直接 add 下一层
gh stack add -Am "docs: add gh stack mock (layer 2)" stack-mock-2

# 本地确认链式结构
gh stack view
git log --oneline --decorate -5
```

## 在 GitHub 上会看到什么

打开这一层 PR 时：

- **Files changed** 只有本文件，不会把 layer 0 / layer 1 的 md 再展示一遍
- PR 顶部会有 **stack map**，能跳到上下层
- base branch 显示为 `stack-mock-1`，而不是 `main`

这就是 stacked PR 和「三个都对 main 开 PR」最大的差别。
