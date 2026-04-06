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

## Claude Code スキル

`.claude/commands/` 配下のスキルは、各リポジトリにコピーまたはsymlinkして使用。

| スキル | 説明 |
|---|---|
| `/billing:check <org>` | GHEC billingのアノマリー検出 |
| `/security:sensitive-files <org>` | Organization横断のsensitive filesチェック |
