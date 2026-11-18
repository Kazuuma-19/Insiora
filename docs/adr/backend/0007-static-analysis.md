# 0007. 静的解析

## 決定

Qodana（JVM Community 版）を backend に採用する。GitHub Actions が使えるまではローカルで `qodana scan` を実行し、設定は `qodana.yaml` にまとめる。frontend は oxlint（[0005](../frontend/0005-frontend-linter.md)）に任せる。

### 理由

- 開発に IntelliJ IDEA を使っていて、IDE と同じインスペクションで検査できる
- IDE 上の警告と検査結果が一致するので、指摘を IDE で確認・修正しやすい
- JVM 用の Community 版は無料で、外部サービスへの登録も不要
- ローカルでも実行できるため、GitHub Actions が使えない今でも導入できる

## 比較案

| 案 | 名称 | バージョン | 特徴 | 判定 |
| --- | --- | --- | --- | --- |
| 1 | Qodana | CLI 2026.2.3 | IntelliJ のインスペクション | 採用 |
| 2 | SonarQube Cloud | SaaS | Java と TS を一括で解析 | 次点 |
| 3 | CodeQL | GitHub | 脆弱性の検出 | 見送り |

## 案1: Qodana

### 概要

JetBrains 製の静的解析ツール。IntelliJ IDEA のインスペクションを、CLI（Docker）や CI で実行する。

### メリット

- IDE 上の警告と CI の結果が一致する
- JVM 用の Community 版は無料で、Qodana Cloud のアカウントも不要

### デメリット

- JS/TS 用は無料の Community 版の対象外で、有償版が必要

## 案2: SonarQube Cloud

### 概要

Sonar 社の静的解析サービス。リポジトリを連携すると、品質ゲートや PR へのコメントで結果を返す。

### メリット

- Java と TypeScript の両方を 1 か所で解析できる
- 品質ゲートや PR へのコメントがある

### デメリット

- 外部サービスへの登録と連携が必要
- 無料で使える範囲は確認が必要

## 案3: CodeQL

### 概要

GitHub 製の静的解析エンジン。GitHub の code scanning として、脆弱性を検出する。

### メリット

- セキュリティの脆弱性の検出に強い
- Public リポジトリは無料

### デメリット

- コード品質より脆弱性の検出が中心
- GitHub Actions で動くため、Actions が使えないと実行できない

## 変更履歴

| 日付 | 内容 |
| --- | --- |
| 2026-11-17 | 初版作成 |
| 2026-11-17 | 構成を変更（決定の理由、比較表、各案の概要を追加） |
