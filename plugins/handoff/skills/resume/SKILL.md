---
description: ハンドオフドキュメントから作業を再開します。
---
# Handoff からの再開

## Variables
- HANDOFF_PATH: $ARGUMENTS

## Instruction

別のAIエージェントが作成したhandoffから作業を再開してください。

### 1. Handoff の読み込み

handoffドキュメントを探してください:
- `HANDOFF_PATH` が指定されていればそのパスを読む
- 未指定なら作業ディレクトリの `HANDOFF.md` を確認
- 見つからない場合はユーザーにパスを尋ねる

handoffドキュメント全体を注意深く読んでください。

### 2. 状態のドリフト確認

`git status` と `git log --oneline -3` を実行し、以下を確認してください:
- ブランチはhandoff記載と同一か
- handoff作成後にコミットされた変更はないか
- handoffに記載されていないuncommitted変更はないか

!git status
!git log --oneline -3

状態が大きく乖離している場合、ユーザーに警告してください:
> 「このhandoff作成後にリポジトリが変化しています。[変更内容]。
>  このままhandoffのコンテキストで進めますか? それとも変更内容を説明していただけますか?」

### 3. ユーザーへのサマリ

ドキュメント全文ではなく簡潔なサマリを示してください:

```
Handoffから再開: [タイトル]

Goal: [1文]
Status: [X / Y タスク完了]
Next: [Resume Instructionsの最初の項目]

続行しますか?
```

### 4. 警告事項の遵守

以下に特に注意してください:
- **Failed Approaches** — 同じ失敗を繰り返さない
- **Warnings** — 前のエージェントが残した落とし穴を尊重する
- **Key Decisions** — ユーザーが変更を求めない限り、確立されたパターンに従う

### 5. 作業の継続

ユーザーが別の指示をしない限り、「Resume Instructions」の最初の項目から開始してください。

handoffに重要な点で不明瞭な箇所があれば、推測せずユーザーに尋ねてください。
