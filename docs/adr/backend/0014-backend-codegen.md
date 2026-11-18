# 0014. backend のコード生成

## 決定

openapi-generator（`spring` ジェネレーター）を採用する。

### 理由

- TypeSpec は Java のサーバーコードを直接出力できないため、OpenAPI から生成するツールが必要
- 利用者と情報が最も多く、Spring Boot 4、Jackson 3、record に対応したオプションがある（公式ドキュメントで確認）
- インターフェースだけを生成するオプションがあり、生成コードの癖の影響を小さくできる
- DTO を record で生成できるので、Lombok が不要になる

## 比較案

| 案 | 名称 | バージョン | 特徴 | 判定 |
| --- | --- | --- | --- | --- |
| 1 | openapi-generator | 7.27.0 | 利用者が多く、Spring Boot 4 に対応 | 採用 |
| 2 | openapi-processor-spring | 2026.3.2 | Spring 専用、生成コードが少ない | 次点 |
| 3 | Swagger Codegen | 3.0.85 | openapi-generator の分岐元 | 見送り |
| 4 | 手書き + 契約テスト | — | 生成しない | 見送り |

## 案1: openapi-generator

### 概要

OpenAPI から、多数の言語・フレームワーク向けのサーバーコードやクライアントを生成するツール。Spring 向けには `spring` ジェネレーターがある。

### メリット

- 利用者と情報が最も多い
- `useSpringBoot4`、`useJackson3`、`useRecords` など、Spring Boot 4 と最近の Java に合わせたオプションがある
- `interfaceOnly` でインターフェースだけを生成できる

### デメリット

- 生成コードに癖があり、細かく調整するには設定やテンプレートの変更が必要
- オプションが多く、どれを使うか見極めが必要

## 案2: openapi-processor-spring

### 概要

OpenAPI から、Spring のインターフェースと DTO だけを生成する、Spring 専用のツール。

### メリット

- 生成されるコードが少なく読みやすい

### デメリット

- 利用者と情報が少ない

## 案3: Swagger Codegen

### 概要

OpenAPI からコードを生成するツール。openapi-generator はここから分岐した。

### メリット

- 歴史が長い

### デメリット

- 今は openapi-generator のほうが開発が活発で、Spring Boot 4 への対応も先行している

## 案4: 手書き + 契約テスト

### 概要

コードを生成せず、DTO とコントローラーを手で書き、OpenAPI とのずれはテストで検出する方法。

### メリット

- 生成コードの癖がなく、自由に書ける

### デメリット

- DTO とエンドポイントを手で書く必要がある
- OpenAPI とのずれをテストでしか検出できず、気付くのが遅れる

## 変更履歴

| 日付 | 内容 |
| --- | --- |
| 2026-11-17 | 初版作成 |
| 2026-11-17 | 構成を変更（決定の理由、比較表、各案の概要を追加） |
