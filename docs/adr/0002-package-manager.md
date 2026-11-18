# 0002. パッケージマネージャー

## 決定

pnpm を採用する。バージョンは `mise.toml` で固定し、`package.json` の `packageManager` にも記載する。`package-lock.json` は削除し、`pnpm-lock.yaml` をコミットする。

### workspace

pnpm workspace を使い、Node のプロジェクトである `api/`（TypeSpec、[0011](0011-api-definition.md)）と `frontend/` をまとめて管理する。`backend/` は Gradle のため workspace には含めない。

```
Insiora/
├── pnpm-workspace.yaml   ← workspace の対象（api, frontend）
├── package.json          ← 共通のスクリプトと開発ツール（oxlint、oxfmt など）
├── pnpm-lock.yaml        ← ロックファイルは 1 つ
├── api/package.json
├── frontend/package.json
└── backend/              ← Gradle（workspace の対象外）
```

- 依存関係のインストールは、ルートで `pnpm install` を 1 回実行する
- oxlint（[0005](0005-frontend-linter.md)）と oxfmt（[0006](0006-frontend-formatter.md)）はルートに入れ、全プロジェクトで共有する
- `frontend` は `api` を `workspace:*` で参照し、`pnpm -r build` で TypeSpec のコンパイル → orval の生成（[0012](0012-frontend-codegen.md)）の順に実行されるようにする
- 特定のプロジェクトだけで実行する場合は `pnpm --filter <名前>` を使う

### 理由

- 宣言していない依存パッケージを import できないため、依存関係の漏れに早く気付ける
- インストールが速く、ディスク使用量も少ない
- Node と別のランタイム（Bun）を持ち込む必要がない
- TypeSpec も Node のツールで、`api/` と `frontend/` の 2 つの Node のプロジェクトを持つことになる。workspace でインストールとロックファイルを 1 つにまとめ、依存関係のバージョンを揃えやすくする
- TypeSpec の出力（OpenAPI）を orval が読み込むため、workspace の依存関係で「コンパイルしてから生成する」順序を自動で扱える
- 開発ツールをルートで一元管理でき、プロジェクトごとに同じ設定を持たなくてよい

## 比較案

| 案 | 名称 | バージョン | 特徴 | 判定 |
| --- | --- | --- | --- | --- |
| 1 | pnpm | 12.21.0 | 共有ストアと厳密な node_modules | 採用 |
| 2 | npm | 12.2.2 | Node に同梱 | 次点 |
| 3 | Yarn (Berry) | 4.18.1 | Plug'n'Play | 見送り |
| 4 | Bun | 1.4.4 | ランタイムを兼ねる | 見送り |

## 案1: pnpm

### 概要

依存パッケージを PC 全体の共有ストアに 1 回だけ保存し、各プロジェクトからはリンクで参照するパッケージマネージャー。

### メリット

- ディスク使用量が少なく、インストールが速い
- 宣言していないパッケージを import できない、厳密な構成になる
- workspace 機能で複数パッケージを管理できる

### デメリット

- 一部の古いパッケージは厳密な構成で動かないことがある（設定で回避できる）

## 案2: npm

### 概要

Node に同梱されている標準のパッケージマネージャー。

### メリット

- 追加の導入が不要

### デメリット

- インストールが比較的遅い
- 宣言していない依存パッケージも import できてしまう

## 案3: Yarn (Berry)

### 概要

Yarn の 2 系以降。Plug'n'Play により node_modules を使わずに依存関係を解決できる。

### メリット

- Plug'n'Play によりインストールが速い

### デメリット

- Plug'n'Play はエディタやツールとの相性の調整が必要

## 案4: Bun

### 概要

JavaScript のランタイム、パッケージマネージャー、テストランナーなどを 1 つにまとめたツール。

### メリット

- インストールが非常に速い

### デメリット

- Node とは別のランタイムを持ち込むことになり、役割が重複する

## 変更履歴

| 日付 | 内容 |
| --- | --- |
| 2026-11-17 | 初版作成 |
| 2026-11-17 | 構成を変更（決定の理由、比較表、各案の概要を追加） |
| 2026-11-18 | pnpm workspace の採用を追記 |
