---
description: Organization内のリポジトリにsensitive files（秘密鍵、.env、credentials等）がpushされていないかチェックします。
---
# Sensitive Files チェック

## Variables
- ORG_NAME: $ARGUMENTS

## Instruction

Organization内の全リポジトリを対象に、sensitive filesがコミットされていないかチェックしてください。
ORG_NAMEが空の場合はユーザーにOrganization名を確認してください。

### 1. リポジトリ一覧の取得

!~/.local/bin/gh-wrapper.sh api orgs/{ORG_NAME}/repos --paginate --jq '.[].name'

### 2. 各リポジトリのsensitive filesチェック

以下のパターンに該当するファイルがgit管理下にあるかAPIで確認してください。
リポジトリのルートおよび主要なディレクトリを再帰的に検索します。

チェック対象パターン:
- 秘密鍵・証明書: `*.pem`, `*.key`, `*.p12`, `*.pfx`, `*.jks`
- 環境変数: `.env`, `.env.*`, `*.env`
- 認証情報: `credentials.json`, `service-account*.json`
- SSH鍵: `id_rsa`, `id_ed25519`
- AWS: `.aws/credentials`
- Terraform state: `*.tfstate`, `*.tfvars`
- その他: `.htpasswd`, `.netrc`, `.npmrc`, `.pypirc`, `kubeconfig`

各リポジトリに対して:
!~/.local/bin/gh-wrapper.sh api "repos/{ORG_NAME}/{repo}/git/trees/HEAD?recursive=1" --jq '[.tree[].path]'

ツリーから上記パターンにマッチするファイルを抽出してください。

### 3. レポート出力

```
## Sensitive Files チェックレポート

### サマリー
- 状態: ✅ 問題なし / ⚠️ 要確認ファイルあり
- チェック対象: N リポジトリ

### 検出結果（検出された場合のみ）
| リポジトリ | ファイルパス | 種別 |
|---|---|---|
| repo-name | path/to/file | 秘密鍵 / 環境変数 / ... |

### 推奨アクション（必要な場合のみ）
- 該当ファイルをgit履歴から削除する手順
- .gitignoreへの追加
- secretのローテーション
```
