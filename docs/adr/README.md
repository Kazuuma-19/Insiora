# ADR（Architecture Decision Records）

Insiora の技術的な決定と、その理由を記録する。

## 運用ルール

- 層ごとにディレクトリを分ける
  - `common/`: 複数の層にまたがる決定
  - `backend/`: backend（Spring Boot）の決定
  - `frontend/`: frontend（React）の決定
- 番号はディレクトリをまたいだ通し番号にする。新しい ADR は、全体の最後の番号の次を使う
- 各 ADR は「決定（理由を含む）」「比較案」「案ごとの概要・メリット・デメリット」「変更履歴」の構成で書く
- 決定を変更する場合は、内容を更新し、変更履歴に日付と内容を追記する

## 一覧

### common

| No. | 項目 | 決定 |
| --- | --- | --- |
| [0002](common/0002-package-manager.md) | パッケージマネージャー | pnpm（workspace） |
| [0011](common/0011-api-definition.md) | API 定義 | TypeSpec |
| [0023](common/0023-date-time-handling.md) | 日時の扱い | UTC で保存し、ISO 8601 で受け渡す |

### backend

| No. | 項目 | 決定 |
| --- | --- | --- |
| [0001](backend/0001-db-migration.md) | DB マイグレーション | Flyway |
| [0003](backend/0003-java-formatter.md) | Java のフォーマッター | Spotless + palantir-java-format |
| [0004](backend/0004-java-linter.md) | Java のリンター | Error Prone |
| [0007](backend/0007-static-analysis.md) | 静的解析 | Qodana（JVM Community） |
| [0008](backend/0008-backend-test.md) | backend のテスト | JUnit + Testcontainers |
| [0014](backend/0014-backend-codegen.md) | backend のコード生成 | openapi-generator |
| [0016](backend/0016-db-access.md) | DB アクセス | JdbcClient |
| [0017](backend/0017-null-safety.md) | null 安全性 | JSpecify + NullAway |
| [0022](backend/0022-external-api-client.md) | 外部 API の呼び出し | HTTP Service Client（`@HttpExchange`） |

### frontend

| No. | 項目 | 決定 |
| --- | --- | --- |
| [0005](frontend/0005-frontend-linter.md) | リンター | oxlint |
| [0006](frontend/0006-frontend-formatter.md) | フォーマッター | oxfmt |
| [0009](frontend/0009-frontend-test.md) | テスト | Vitest + React Testing Library |
| [0010](frontend/0010-e2e-test.md) | E2E テスト | Playwright |
| [0012](frontend/0012-frontend-codegen.md) | コード生成 | orval |
| [0013](frontend/0013-data-fetching.md) | サーバーデータの取得 | TanStack Query |
| [0015](frontend/0015-api-mock.md) | API のモック | MSW |
| [0018](frontend/0018-routing.md) | ルーティング | TanStack Router |
| [0019](frontend/0019-styling.md) | スタイリング | Tailwind CSS |
| [0020](frontend/0020-ui-components.md) | UI コンポーネント | 使わない（自作） |
| [0021](frontend/0021-date-library.md) | 日付ライブラリ | Temporal（+ temporal-polyfill） |
