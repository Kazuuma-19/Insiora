# 0017. null 安全性

## 決定

JSpecify のアノテーションと NullAway を採用する。各パッケージの `package-info.java` に `@NullMarked` を付け、参照型は既定で null を許さない扱いにする。null になりうる箇所だけ `@Nullable` を付ける。NullAway は Error Prone（[0004](0004-java-linter.md)）のプラグインとして動かし、違反はコンパイルエラーにする。JSpecify のバージョンは Spring Boot 4.1 が管理する 1.0.1 を使う。

### 理由

- Spring Framework 7 / Spring Boot 4 自体が JSpecify のアノテーションを使っていて、Spring の API の null 許容が NullAway で検査できる
- 採用済みの Error Prone のプラグインとして動くため、ビルドの仕組みを追加しなくてよい
- コンパイルのたびに検査され、実行時の `NullPointerException` を減らせる
- `@NullMarked` で「既定は null 不可」にすると、アノテーションを付ける箇所が null になりうる所だけで済む
- Checker Framework より検査が軽く、ビルド時間への影響が小さい

## 比較案

| 案 | 名称 | バージョン | 特徴 | 判定 |
| --- | --- | --- | --- | --- |
| 1 | JSpecify + NullAway | 1.0.1（Boot 管理） / 0.14.4 | 標準化されたアノテーション、Error Prone で検査 | 採用 |
| 2 | JSpecify + Checker Framework | 1.0.1 / 4.4.0 | 厳密な検査 | 次点 |
| 3 | JetBrains Annotations | 26.1.0 | IntelliJ の警告のみ | 見送り |
| 4 | アノテーションを使わない | — | `Optional` と規約で対応 | 見送り |

## 案1: JSpecify + NullAway

### 概要

JSpecify は、Google、JetBrains、Spring などが共同で策定した、Java の null 許容を表す標準のアノテーション。NullAway は Uber 製の Error Prone のプラグインで、そのアノテーションをもとにコンパイル時に null の誤りを検出する。

### メリット

- Spring Framework 7 と同じアノテーションで、Spring の API の null 許容も検査できる
- Error Prone のプラグインなので、追加の工程が不要
- 検査が速い
- IntelliJ IDEA も JSpecify のアノテーションを理解し、エディタ上で警告する

### デメリット

- 既存のコードや、アノテーションのないライブラリとの境界で、`@Nullable` の付与や抑制が必要になる
- ジェネリクスなどの細かいケースは、Checker Framework ほど厳密ではない

## 案2: JSpecify + Checker Framework

### 概要

Checker Framework の Nullness Checker で、JSpecify などのアノテーションをもとに null を検査する構成。

### メリット

- 検査が最も厳密で、ジェネリクスなども細かく扱える

### デメリット

- 検査が重く、ビルド時間が延びる
- 指摘が多く、アノテーションの付与や抑制の手間が大きい
- Error Prone とは別の仕組みで動く

## 案3: JetBrains Annotations

### 概要

JetBrains 製の `@Nullable` / `@NotNull` アノテーション。IntelliJ IDEA がエディタ上で警告を出す。

### メリット

- IntelliJ IDEA との相性が良い

### デメリット

- ビルドでは検査されず、警告を見落とすとそのまま残る
- Spring が採用している JSpecify とは別のアノテーションになる

## 案4: アノテーションを使わない

### 概要

null を返す可能性がある所は `Optional` を使うなどの規約だけで対応する方法。

### メリット

- 追加のライブラリが不要

### デメリット

- 規約が守られているかを機械的に検査できない
- フィールドや引数の null は `Optional` では表しにくい

## 変更履歴

| 日付 | 内容 |
| --- | --- |
| 2026-11-18 | 初版作成 |
