# `stack-mock-6`：部分 merge 之后继续往上叠

`#1`–`#4`（`feat/stack-mock` … `stack-mock-3`）已经合进 `feat/release-3.4.4`。剩下的还开着：

```
feat/release-3.4.4          ← trunk，已经包含 layer 0–3
└── stack-mock-4  #8        ← 合完之后 GitHub 会自动 retarget 到 trunk
    └── stack-mock-5  #9    ← 当时的 top
        └── stack-mock-6    ← 你现在看的这一层
            └── stack-mock-7
```

## 这一层做了什么

演示：stack 下半截合掉以后，**不必开新 stack**，还可以在当前 top 上继续 `add`。

合完之后先 sync，让剩下的层叠到新的 trunk 上，再 add。

## 操作步骤

```bash
# 停在还开着的 top（当时是 stack-mock-5）
git checkout stack-mock-5
gh stack sync                 # 拉 trunk、跳过已合的层、rebase 4/5

gh stack top                  # 确认在 stack-mock-5
gh stack add -Am "docs: add gh stack mock (layer 6)" stack-mock-6
```

## 预期结果

- 当前分支变成 `stack-mock-6`
- 这一层 PR 的 base 是 `stack-mock-5`，Files changed 只有本文件
- 已合的 `#1`–`#4` 还在 stack map 里，但是 merged
