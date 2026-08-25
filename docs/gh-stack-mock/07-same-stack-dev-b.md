# 同一条 stack — 开发者 B 后完成（`stack-mock-5`）

A 已经先 `submit`，Stack #5 的 top 从 `stack-mock-3` 变成了 `stack-mock-4`。B 本地还停在旧 top，所以 **不能直接 add**，要先 sync。

```
main
└── feat/stack-mock     #1
    └── stack-mock-1    #2
        └── stack-mock-2  #3
            └── stack-mock-3  #4
                └── stack-mock-4  #8   ← A 先完成，现在的 top
                    └── stack-mock-5      ← B 后完成（本文件）
```

## 开发者 B 的操作步骤

```bash
# B 本地还在开工时的 top
git checkout stack-mock-3
# 或: gh stack down   （如果当前在 stack-mock-4 上）

# 把 A 已经 push 上去的新层拉回来
gh stack sync

# sync 之后 stack-mock-4 出现在 stack 里，但当前分支仍可能停在 stack-mock-3
# add 必须在 top 上执行：
gh stack top                          # → stack-mock-4

# 叠在 A 上面，而不是叠回 stack-mock-3
gh stack add -Am "docs: same-stack linear mock (dev B second)" stack-mock-5

gh stack submit --auto
```

## 为什么不能还在 stack-mock-3 上 add

`gh stack add` 只能加在 **当前 stack 的 top**。sync 之后 top 已经是 `stack-mock-4`。如果 B 坚持从 `stack-mock-3` 长出旁支，就会和远程 diverged，而不是同一条上下层。

## 预期结果

- B 的 PR base = `stack-mock-4`（A 的 branch），不是 `stack-mock-3`
- Files changed 只有本文件，看不到 A 的 `06-same-stack-dev-a.md`
- Stack #5 的 map 变成 6 层，A 在下、B 在上
- 合并必须从下往上：要合 B，会连同 A 以及更下面的层一起合
