# 并行开发 B — `stack-mock-3b`

```
main
└── feat/stack-mock          Stack #5
    └── stack-mock-1
        └── stack-mock-2
            └── stack-mock-3 ← 两人的共同起点
                ├── stack-mock-3a   开发者 A  → PR #6
                └── stack-mock-3b   开发者 B  ← 本文件
```

## 场景

开发者 B 和 A **同时**从 `stack-mock-3` 开工，彼此不知道对方的分支。B 不能 `gh stack add` 进 Stack #5（那时 top 仍是 `stack-mock-3`，A 也在 add 的话会抢同一个 top），也不能叠到 `stack-mock-3a` 上面（那会变成 A 的后续层，而不是并行）。

B 同样以 `stack-mock-3` 为 trunk 开自己的 stack。

## 开发者 B 的操作步骤

```bash
# 回到共同起点（不要从 stack-mock-3a 分出去）
git checkout stack-mock-3

# 先建自己的分支，再 init。直接在 stack-mock-3 上 init 会失败：
#   current branch "stack-mock-3" is already part of a stack
git checkout -b stack-mock-3b
gh stack init --base stack-mock-3 stack-mock-3b

# 写本文件并提交（B 的独立 commit）
git add docs/gh-stack-mock/05-parallel-dev-b.md
git commit -m "docs: parallel work from stack-mock-3 (dev B)"

# 只 submit B 自己这一条 stack，不会改 Stack #5，也不会并进 A 的 PR
gh stack submit --auto
```

## 预期结果

- A 和 B 的 PR **base 都是 `stack-mock-3`**
- 两条 PR 互不包含对方的 md
- GitHub 上它们是两条独立的后续工作，而不是同一条 stack 里的上下层
- 打开 [#4](https://github.com/G-Linker/gh-stack/pull/4)（`stack-mock-3`）时，能看到有 PR 以它为 base
