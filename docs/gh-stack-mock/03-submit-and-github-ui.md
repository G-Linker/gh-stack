# Layer 3 — `stack-mock-3`：submit 到 GitHub 看呈现效果

当前 stack 结构（自下而上）：

```
main (trunk)
└── feat/stack-mock     PR base = main
    └── stack-mock-1    PR base = feat/stack-mock
        └── stack-mock-2  PR base = stack-mock-1
            └── stack-mock-3  ← 你现在看的这一层（top）
```

## 这一层做了什么

把整条 stack push 上去，并为每一层创建 PR，同时在 GitHub 上把它们 **link 成一个 Stack**。

## 操作步骤

```bash
# 仍停在 top
gh stack view

# 写本文件后加最后一层
gh stack add -Am "docs: add gh stack mock (layer 3)" stack-mock-3

# 推送全部 branch，创建/更新 PR，并在 GitHub 上组成 Stack
# --auto 跳过交互式编辑器，用自动生成的 PR title
# 不加 --open 时新 PR 默认是 draft，避免打扰 reviewer
gh stack submit --auto

# 之后随时看本地 + 远端状态
gh stack view
```

`submit` 实际会做四件事：

1. push 这条 stack 里的全部 branch
2. 给还没有 PR 的 branch 开 PR
3. 把已有 PR 的 base 改成「下一层的 branch」
4. 在 GitHub 上创建 / 更新 Stack 对象，把这些 PR 串起来

## 在 GitHub 仓库上重点看这些地方

1. **任意一层 PR 页顶部的 stack map**
   - 能看到 4 层的顺序、每层状态（draft / open）
   - 可以点进相邻层，不必自己记 branch 依赖
2. **每一层的 Files changed**
   - layer 0：只有 `00-init.md`
   - layer 1：只有 `01-add-second-layer.md`
   - layer 2：只有 `02-add-third-layer.md`
   - layer 3：只有本文件
3. **base branch**
   - 最底层对 `main`
   - 上面每一层的 base 都是它下面那条 branch，而不是全部对 `main`
4. **仓库的 Pull requests 列表**
   - 这些 PR 会被归到同一个 Stack 下，而不是 4 个互不相关的 PR
5. **merge 行为（本仓库可以随便合）**
   - 合并某一层时，会连同它下面还没合的层一起合
   - 上面还开着的层会自动 rebase / retarget
   - 这个 playground 仓库可以随意试 merge / unstack / 再叠

## 常用后续命令

```bash
gh stack up / gh stack down     # 在层之间移动
gh stack sync                   # fetch + rebase + push + 同步 PR 状态
gh stack rebase                 # 级联 rebase，保持线性历史
gh stack merge                  # 一次合并到底
```
