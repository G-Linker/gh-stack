# 并行开发 A — `stack-mock-3a`

两条 stack 从同一层分叉（GitHub 的 stack **不能**在同一条 stack 里分叉）：

```
main
└── feat/stack-mock          Stack #5
    └── stack-mock-1
        └── stack-mock-2
            └── stack-mock-3 ← 两人的共同起点 / 新 stack 的 trunk
                ├── stack-mock-3a   ← 开发者 A 的新 stack（本文件）
                └── stack-mock-3b   ← 开发者 B 的新 stack
```

## 场景

两个人同时从 `stack-mock-3` 往上做互不依赖的改动。`gh stack add` 只能加在 **当前 stack 的 top**，而且一条 stack 必须是线性链，所以 **不能** 两人都 `add` 进 Stack #5。正确做法是各自 `gh stack init --base stack-mock-3`，开一条新 stack。

## 开发者 A 的操作步骤

```bash
# 共同起点
git checkout stack-mock-3

# 先建自己的分支，再 init。直接在 stack-mock-3 上 init 会失败：
#   current branch "stack-mock-3" is already part of a stack
git checkout -b stack-mock-3a
gh stack init --base stack-mock-3 stack-mock-3a

# 写本文件。因为 stack-mock-3a 还没有独立 commit，
# `add -Am` 的 commit 会落在当前分支上，而不是再长一层
gh stack add -Am "docs: parallel work from stack-mock-3 (dev A)"

# A 把自己的 stack submit 上去（不影响 Stack #5，也不包含 B 的分支）
gh stack submit --auto
```

## 预期结果

- 新 stack 的 trunk 是 `stack-mock-3`，不是 `main`
- 这一层 PR 的 base = `stack-mock-3`，Files changed 只有本文件
- GitHub 上会出现 **另一条** stack，和 #5 在 `stack-mock-3` 处分叉
- 打开 `stack-mock-3` 的 PR 时，能看到有后续工作叠在上面（A / B 各自一条）
