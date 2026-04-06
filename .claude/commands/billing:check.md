---
description: GHEC billing のアノマリー検出。通常のライセンス費用以外の予期しない課金を検出します。
---
# GHEC Billing アノマリー検出

## Variables
- ORG_NAME: $ARGUMENTS

## Instruction

以下のチェックを順番に実行し、最後にレポートをまとめてください。
ORG_NAMEが空の場合はユーザーにOrganization名を確認してください。

### 1. Usage Report の取得

当月のbilling usageを取得してください。
!gh api orgs/{ORG_NAME}/settings/billing/usage --jq '.usageItems[]'

### 2. アノマリー判定

取得したusageItemsを以下のルールで分類してください。

**正常（想定内）:**
- `product: ghec` — Enterprise Cloudライセンス費用
- `product: actions` で `netAmount == 0` — included分内のActions利用

**アノマリー（要確認）:**
- `product: actions` で `netAmount > 0` — Actions included分を超過
- `product: advanced_security` が存在 — GHAS課金発生（ライセンス未契約のため想定外）
- `product: copilot` が存在 — Copilot課金（意図した利用か要確認）
- `product: packages` で `netAmount > 0` — Packages有料利用
- 上記以外の未知のproduct

### 3. Code Scanning default setup チェック

全リポジトリの名前とvisibilityを取得してください。
!gh api orgs/{ORG_NAME}/repos --paginate --jq '.[] | {name, visibility}'

各リポジトリに対して:
!gh api repos/{ORG_NAME}/{repo}/code-scanning/default-setup --jq '{state}'

レスポンスの解釈:
- `state: configured` + **public** → 正常（publicリポジトリのCode Scanning/Secret Scanningは無料）
- `state: configured` + **private/internal** → アノマリー（GHAS課金が発生する）
- `state: not-configured` → 正常
- 403 "Code Security must be enabled" → Code Security自体が無効。正常

### 4. Copilot 席数チェック

!gh api orgs/{ORG_NAME}/copilot/billing --jq '{total_seats: .seat_breakdown.total, setting: .seat_management_setting}'

`total_seats > 0` の場合、意図した利用か警告してください。

### 5. レポート出力

以下の形式でレポートをまとめてください。

```
## GHEC Billing アノマリーレポート（YYYY-MM）

### サマリー
- 状態: ✅ 正常 / ⚠️ アノマリー検出
- 当月netAmount合計: $X.XX
- 内訳: GHEC $X.XX / Actions $X.XX / ...

### アノマリー（検出された場合のみ）
- [product] $X.XX - 詳細説明

### Code Scanning 状態
- configured: N リポジトリ（リスト表示）
- not-configured: N リポジトリ

### Copilot 席数
- 割り当て済み: N 席

### 推奨アクション（必要な場合のみ）
- 具体的な対応手順
```
