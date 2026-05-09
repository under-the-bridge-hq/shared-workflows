# shared-workflows

Organization横断で使うreusable workflows・Claude Codeスキル集

## Reusable Workflows

### gitleaks.yaml

Gitleaksによるsecretスキャン。PR時に差分をチェックし、secretの漏洩を防止する。

```yaml
# 呼び出し側の例
name: Security
on: [pull_request]
jobs:
  gitleaks:
    uses: under-the-bridge-hq/shared-workflows/.github/workflows/gitleaks.yaml@main
```

| Input | Default | 説明 |
|---|---|---|
| `scan-mode` | `protect` | `protect`(差分のみ) / `detect`(全履歴) |
| `config-path` | `""` | カスタム設定ファイルパス |

### sensitive-files.yaml

リポジトリ内のsensitive files（秘密鍵、.env、credentials等）の存在チェック。

```yaml
name: Security
on: [pull_request]
jobs:
  sensitive-files:
    uses: under-the-bridge-hq/shared-workflows/.github/workflows/sensitive-files.yaml@main
```

| Input | Default | 説明 |
|---|---|---|
| `extra-patterns` | `""` | 追加のファイルパターン（カンマ区切り） |

## Claude Code プラグイン

Claude Code Plugin Marketplace としてプラグインを配布しています。
利用側は以下のコマンドで marketplace を追加し、必要なプラグインを個別に install してください。

### インストール

利用シーンに応じて2種類の方法があります。

#### 1. 個人で使う（User scope）

自分のすべてのプロジェクトで使えるよう、ユーザーグローバルに install します。Claude Code 内で実行:

```
/plugin marketplace add under-the-bridge-hq/shared-workflows
/plugin install handoff@shared-workflows
/plugin install billing@shared-workflows
/plugin install security@shared-workflows
```

CLI 直接 install のデフォルトは User scope（`~/.claude/settings.json` に記録）。

更新時:

```
/plugin marketplace update shared-workflows
```

#### 2. チームに配布する（Project scope）

特定リポジトリのコラボレーター全員に使ってもらう場合は、リポジトリの
`.claude/settings.json` に marketplace と enabled plugins を記述してコミットします。

```json
{
  "extraKnownMarketplaces": {
    "shared-workflows": {
      "source": {
        "source": "github",
        "repo": "under-the-bridge-hq/shared-workflows"
      }
    }
  },
  "enabledPlugins": {
    "handoff@shared-workflows": true,
    "billing@shared-workflows": true,
    "security@shared-workflows": true
  }
}
```

メンバーがリポジトリを trust すると、Claude Code が
「この marketplace を追加して plugin を install しますか?」と**プロンプトで提案**します
（強制 install ではなくユーザー承認が必要）。

> **メモ**: UI から `/plugin install` 時に **Project scope** を選択しても同じ `.claude/settings.json` に書き込まれます。
> 手動編集と UI 操作のどちらでも結果は同じです。

#### スコープ早見表

| スコープ | 範囲 | 設定ファイル | チーム共有 | 使いどころ |
|---|---|---|---|---|
| **User** | 全プロジェクト・自分のみ | `~/.claude/settings.json` | × | 個人で使う（デフォルト） |
| **Project** | このリポジトリ・全コラボレーター | `.claude/settings.json` | ○ | チーム配布 |
| **Local** | このリポジトリ・自分のみ | `.claude/settings.local.json` | × | 一時的に試す |

### 提供プラグインとスキル

| プラグイン | スキル | 説明 |
|---|---|---|
| `handoff` | `/handoff:create [path]` | AIエージェント間のハンドオフドキュメントを作成 |
| `handoff` | `/handoff:quick [path]` | 必要最小限のハンドオフを作成 |
| `handoff` | `/handoff:resume [path]` | ハンドオフドキュメントから作業を再開 |
| `billing` | `/billing:check <org>` | GHEC billing のアノマリー検出 |
| `security` | `/security:sensitive-files <org>` | Organization 横断の sensitive files チェック |

`/handoff:*` は [willseltzer/claude-handoff](https://github.com/willseltzer/claude-handoff) を参考に、
任意のAIコーディングエージェント（Claude Code, Cursor, Copilot, Aider等）間でコンテキストを引き継ぐためのスキル。

### ローカル開発

このリポジトリ内でプラグインを試す場合は `--plugin-dir` を使用:

```bash
claude --plugin-dir ./plugins/handoff
claude --plugin-dir ./plugins/billing --plugin-dir ./plugins/security
```

### ディレクトリ構成

```
.claude-plugin/
└── marketplace.json          # マーケットプレイスカタログ
plugins/
├── handoff/
│   ├── .claude-plugin/plugin.json
│   └── skills/{create,quick,resume}/SKILL.md
├── billing/
│   ├── .claude-plugin/plugin.json
│   └── skills/check/SKILL.md
└── security/
    ├── .claude-plugin/plugin.json
    └── skills/sensitive-files/SKILL.md
```
