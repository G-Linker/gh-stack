# 同一条 stack — 开发者 A 先完成（`stack-mock-4`）

这次不再从 `stack-mock-3` 分叉开新 stack，而是 **两人都 `gh stack add` 进 Stack #5**。后完成的人叠在先完成的人上面。

```
main
└── feat/stack-mock     #1
    └── stack-mock-1    #2
        └── stack-mock-2  #3
            └── stack-mock-3  #4   ← 两人开工时的共同 top
                └── stack-mock-4  ← 开发者 A（先 submit）  本文件
                    └── stack-mock-5  ← 开发者 B（后 submit，先 sync）
```

`stack-mock-3a` / `stack-mock-3b` 是上一轮「并行、两条 stack」的 mock，这条线不用它们。

## 开发者 A 的操作步骤

```bash
# 先回到 Stack #5。stack-mock-3 同时是另外两条 stack 的 trunk，
# 直接 view/checkout 它会歧义，所以从只属于 #5 的层进去再 top：
git checkout stack-mock-2
gh stack top                          # → stack-mock-3

# 在当前 top 上长出 A 的层
gh stack add -Am "docs: same-stack linear mock (dev A first)" stack-mock-4

# 推上去。Stack #5 的 top 变成 stack-mock-4
gh stack submit --auto
```

## 预期结果

- A 的 PR base = `stack-mock-3`（当时的 top）
- Stack #5 的 map 多一层：`... → stack-mock-3 → stack-mock-4`
- Files changed 只有本文件
- 开发者 B 本地如果还停在 `stack-mock-3`，必须先 `gh stack sync` 才能继续 add
