---
description: 必要最小限のハンドオフを作成します（簡単なタスク・短いコンテキスト転送向け）。
---
# Handoff 作成 (最小版)

## Variables
- HANDOFF_PATH: $ARGUMENTS

## Instruction

必要最小限の `HANDOFF.md` を作成してください。簡単なタスクや手早いコンテキスト転送に使用します。

以下の形式で出力してください（角括弧内を埋める）:

```markdown
# Handoff: [5語以内のタスク名]

**Goal**: [1文]

**Done**: [完了項目をカンマ区切り、無ければ "Nothing yet"]

**Next**: [最も重要な次の1ステップ]

**Watch out**: [重要な警告1つ、無ければ "Nothing special"]
```

これだけ。余計なものは追加しないこと。

`HANDOFF_PATH` が指定されていればそのパスへ、未指定なら `HANDOFF.md` に保存してください。
