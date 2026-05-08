---
description: 任意のAIコーディングエージェントが作業を継続できるハンドオフドキュメントを作成します。
---
# Handoff 作成

## Variables
- HANDOFF_PATH: $ARGUMENTS

## Instruction

任意のAIコーディングエージェント（Claude Code, Cursor, Copilot, Aider等）が作業を継続できるよう、
`HANDOFF.md` を作成してください。

### 1. コンテキストの収集

現在の状態を把握するため、以下を実行してください。

!git status
!git diff --stat
!git log --oneline -5

会話履歴から以下を抽出してください:
- 元のタスク・ゴール
- 完了した作業
- **試したが失敗したアプローチ**（最重要 — 次のエージェントが同じ失敗を繰り返さない）
- 主要な意思決定とその根拠
- セッション中にユーザーから示された好み・指示
- 発生したエラーとその解決方法

### 2. HANDOFF.md の作成

以下の構造で記述してください。空のセクションは省略可。ただし **Failed Approaches** に該当があれば必ず記載すること。

```markdown
# Handoff: [簡潔なタスクタイトル]

**Generated**: [日時]
**Branch**: [git branch]
**Status**: [In Progress / Blocked / Ready for Review]

## Goal

[1-2文: ユーザーが達成したいこと]

## Completed

- [x] [完了した具体的な項目]
- [x] [もう1項目]

## Not Yet Done

- [ ] [残作業 — 具体的に]
- [ ] [もう1項目]

## Failed Approaches (Don't Repeat These)

[何かを試して断念した場合は必ず記載すること:]
- 試したこと
- 失敗の理由（エラーメッセージ、性能問題、設計上の欠陥）
- なぜ現在のアプローチがより良いか

例:
> passport.jsでOAuthを試したが、既存のExpress middlewareと衝突しreq.userがundefinedになった。
> oauth4webapiに変更し、fetchで直接動くようにした。

## Key Decisions

| 決定事項 | 根拠 |
|---|---|
| [選択した内容] | [なぜこのアプローチか] |

## Current State

**Working**: [現在動作している箇所]

**Broken**: [動作していない箇所、関連エラーメッセージ]

**Uncommitted Changes**: [未staging/staged済みの変更サマリ]

## Files to Know

| ファイル | 重要な理由 |
|---|---|
| `path/to/key/file.ts` | [簡潔な説明] |

## Code Context

[次のエージェントが必要な実コードを含める。説明ではなく実物を示す]

**主要なinterface/signature** (呼び出し方・修正方法を理解するため):
\`\`\`typescript
// 例: hookのシグネチャ
function useAuth(): { user: User | null; login: (creds: Credentials) => Promise<void> }
\`\`\`

**APIリクエスト/レスポンス形状** (バックエンド作業の場合):
\`\`\`json
// POST /api/resource - レスポンス例
{ "id": 123, "status": "created" }
\`\`\`

**自明でないロジック** (一読では分からない箇所)

## Resume Instructions

[極めて具体的に。「機能を確認」ではなく、期待結果を含むステップバイステップで:]

1. [必要な準備 — マイグレーション、env var等]
2. [最初のアクション — 正確なコマンド・編集対象ファイル]
3. [検証ステップ — 期待結果を含めて]
   - 期待: [何が起きるべきか]
   - 失敗時: [何を確認するか]

例:
1. `alembic upgrade head` でマイグレーション適用
2. サーバー起動: `./start.sh`
3. ログインフローのテスト: POST /api/login に test@example.com / testpass
   - 期待: 200レスポンス、{ token: "..." }
   - 401の場合: DBにユーザーが存在するか確認

## Setup Required

[次のエージェントに必要な前提条件がある場合のみ:]
- 環境変数: `API_KEY`, `DATABASE_URL`
- テストアカウント: test@example.com / password123
- 必要サービス: Redisが :6379 で起動していること

## Edge Cases & Error Handling

[既知のエッジケースとその扱い:]
- [Xが失敗した場合] → [現在の挙動 / 「未対応」]
- [ユーザーがYをした場合] → [期待される挙動]

## Warnings

[落とし穴、間違って見えるが意図的なもの、避けるべき罠]
```

### 3. ガイドライン

- **Failed approachesは必須** — 試して断念したものがあれば必ず記載
- **コードを示し、説明しない** — 実際のsignature・interface・レスポンス形状を含める
- **テスト手順には期待結果を** — 「動作確認する」では役に立たない
- 徹底的に簡潔に — すべての単語に存在意義を持たせる
- エラーメッセージは原文のまま含める
- ファイルパスはリポジトリルートからの相対で
- ブロッカーがあれば目立つ位置に明示
- 空セクションは省略（Failed Approachesのみ例外 — 該当なしなら「None」と書く）

### 4. 保存

`HANDOFF_PATH` が指定されていればそのパスへ、未指定なら作業ディレクトリの `HANDOFF.md` に保存してください。
