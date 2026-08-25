# `stack-mock-7`：演示用的新 top

继续叠在 `stack-mock-6` 上面，让还开着的链变成 4 层，方便演示 merge / rebase / sync。

```
feat/release-3.4.4
└── stack-mock-4  #8
    └── stack-mock-5  #9
        └── stack-mock-6        ← 上一层
            └── stack-mock-7    ← 你现在看的这一层（top）
```

已合的 `#1`–`#4` 在 map 里仍看得到，只是标成 merged。

## 操作步骤

```bash
# 必须停在 top（此时应是 stack-mock-6）
gh stack view

gh stack add -Am "docs: add gh stack mock (layer 7)" stack-mock-7
gh stack submit --auto
```

## 提交之后还开着的 PR

| 层 | Branch | base |
|---|---|---|
| 4 | `stack-mock-4` | `feat/release-3.4.4`（下面已经合完） |
| 5 | `stack-mock-5` | `stack-mock-4` |
| 6 | `stack-mock-6` | `stack-mock-5` |
| 7 | `stack-mock-7` | `stack-mock-6` |

Files changed 只有本文件。合的时候可以从 `#8` 往上合，或在 top 上 `gh stack merge`。
