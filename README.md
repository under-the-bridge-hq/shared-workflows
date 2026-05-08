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

Claude Code 内で実行:

```
/plugin marketplace add under-the-bridge-hq/shared-workflows
/plugin install handoff@shared-workflows
/plugin install billing@shared-workflows
/plugin install security@shared-workflows
```

更新時:

```
/plugin marketplace update shared-workflows
```

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
