# 0003. Java のフォーマッター

## 決定

Spotless（Gradle プラグイン）と palantir-java-format を採用する。`./gradlew spotlessApply` で整形し、`spotlessCheck` で検査する。

### 理由

- Gradle から整形と検査ができ、lefthook や CI に組み込みやすい
- インデント 4、1 行 120 文字で、一般的な Java の見た目に近い
- Spring のコードで多いラムダやメソッドチェーンの折り返しが読みやすい
- 書式を自動で揃えることで、スタイルの検査（Checkstyle）が不要になる

## 比較案

| 案 | 名称 | バージョン | 特徴 | 判定 |
| --- | --- | --- | --- | --- |
| 1 | Spotless + palantir-java-format | 8.11.0 / 2.103.0 | インデント 4、120 文字 | 採用 |
| 2 | Spotless + google-java-format | 8.11.0 / 2.0.0 | インデント 2、100 文字 | 次点 |

## 案1: Spotless + palantir-java-format

### 概要

Spotless は、Gradle からフォーマッターを実行・検査するプラグイン。palantir-java-format は google-java-format から派生したフォーマッターで、読みやすさを重視した整形ルールを持つ。

### メリット

- インデント 4、1 行 120 文字で、一般的な Java の見た目に近い
- ラムダやメソッドチェーンの折り返しが読みやすい
- Gradle から整形と検査ができる

### デメリット

- google-java-format の派生のため、利用者や情報は google-java-format より少ない

## 案2: Spotless + google-java-format

### 概要

Google Java Style に準拠したフォーマッターを、Spotless から実行する構成。

### メリット

- 設定項目がほぼなく、迷わない
- 利用者が多い

### デメリット

- インデント 2、1 行 100 文字で、ラムダやメソッドチェーンが縦に長くなりやすい

## 変更履歴

| 日付 | 内容 |
| --- | --- |
| 2026-11-17 | 初版作成 |
| 2026-11-17 | 構成を変更（決定の理由、比較表、各案の概要を追加） |
