# 0022. Toggl API の呼び出し

## 決定

Spring Framework 7 の HTTP Service Client（`@HttpExchange`）を採用する。依存関係は `spring-boot-starter-restclient` を使い、内部の HTTP クライアントは RestClient にする。

- Toggl の API は `TogglClient` インターフェースに `@GetExchange` などで定義し、`@ImportHttpServices(group = "toggl", ...)` で登録する
- 接続先の URL、Basic 認証（API トークン）、エラー処理は、グループ単位の設定（`RestClientHttpServiceGroupConfigurer`）にまとめる
- レスポンスは record で受け取り、null になりうる項目（計測中の記録の `stop` など）には `@Nullable` を付ける（[0017](0017-null-safety.md)）
- レート制限（429）などのエラーは、共通のエラー処理で専用の例外に変換する
- ページングや期間の分割などのループは、インターフェースではなく、同期を行うサービス側に書く

設定用のメソッド名やプロパティでの設定方法は、導入時に公式ドキュメントで確認する。

### 理由

- Spring Boot 4 の標準の方式で、追加のライブラリが不要
- 使っている Toggl の API がインターフェース 1 か所にまとまり、一覧しやすい
- 呼び出し側はメソッドを呼ぶだけで、リクエストの組み立てが散らばらない
- テストではインターフェースをモックに差し替えるだけで、Toggl に実際にアクセスせずに同期の処理を確認できる
- 内部は RestClient のため、細かい制御が必要な場合は RestClient を直接使う方法と併用できる
- Toggl API にはレート制限があるとされ、エラー処理を 1 か所にまとめておく必要がある

## 比較案

| 案 | 名称 | バージョン | 特徴 | 判定 |
| --- | --- | --- | --- | --- |
| 1 | HTTP Service Client（`@HttpExchange`） | Spring Framework 7 標準 | インターフェースで宣言する | 採用 |
| 2 | RestClient | Spring Framework 7 標準 | メソッドチェーンで都度組み立てる | 次点 |
| 3 | openapi-generator でクライアントを生成 | 7.27.0 | Toggl の OpenAPI から生成 | 見送り |
| 4 | WebClient | Spring Framework 7 標準 | リアクティブ | 見送り |
| 5 | RestTemplate | Spring Framework 7 標準 | 従来の方式 | 見送り |
| 6 | OpenFeign | Spring Cloud | 宣言的なクライアント | 見送り |

## 案1: HTTP Service Client（`@HttpExchange`）

### 概要

Java のインターフェースにアノテーションで API を定義すると、Spring が実装（プロキシ）を自動で作り、HTTP クライアントとして使える仕組み。Spring Framework 7 / Spring Boot 4 では `@ImportHttpServices` で登録と設定が簡単になった。

### メリット

- 使っている API がインターフェースにまとまる
- 呼び出しがメソッド呼び出しになり、読みやすい
- テストでモックに差し替えやすい
- URL、認証、エラー処理をグループ単位でまとめて設定できる

### デメリット

- インターフェースは 1 回の呼び出しだけを表すため、ページングなどのループは呼び出し側で書く
- 比較的新しい仕組みのため、情報は RestClient より少ない

## 案2: RestClient

### 概要

Spring Framework 6.1 で追加された、メソッドチェーンで書く同期の HTTP クライアント。

### メリット

- ヘッダー、パラメーター、エラー処理を呼び出しごとに細かく制御できる

### デメリット

- エンドポイントが増えると、リクエストを組み立てるコードがあちこちに散らばる
- テストでは `MockRestServiceServer` などが必要になる

## 案3: openapi-generator でクライアントを生成

### 概要

Toggl の API の OpenAPI 定義から、Java のクライアントを生成する方法。

### メリット

- リクエストとレスポンスの型を手で書かなくて済む

### デメリット

- Toggl が公式に OpenAPI を公開しているか確認できていない
- 使わない API のコードも大量に生成される

## 案4: WebClient

### 概要

Spring WebFlux のリアクティブな HTTP クライアント。

### メリット

- 非同期や並列の呼び出しに強い

### デメリット

- WebFlux の依存が増え、Spring MVC のアプリでは過剰
- リアクティブの書き方を覚える必要がある

## 案5: RestTemplate

### 概要

Spring の従来の同期の HTTP クライアント。

### メリット

- 情報が多い

### デメリット

- 廃止に向かっていて、新規の開発には向かない

## 案6: OpenFeign

### 概要

Spring Cloud OpenFeign による、インターフェースで定義する宣言的な HTTP クライアント。

### メリット

- HTTP Service Client と同じく、インターフェースで定義できる

### デメリット

- Spring Cloud への依存が必要
- Spring 7 では標準の HTTP Service Client が推奨されている

## 変更履歴

| 日付 | 内容 |
| --- | --- |
| 2026-11-18 | 初版作成 |
