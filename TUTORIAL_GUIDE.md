# Pony チュートリアル作成指示書（AI向け）

本ドキュメントは、AI（LLM）に対してプログラミング言語 Pony のチュートリアルを章ごとに生成させるための指示書です。

---

## 1. プロジェクト概要

| 項目 | 内容 |
|------|------|
| 目的 | Pony 言語の包括的な日本語チュートリアルを作成する |
| 対象 Pony バージョン | **0.60.6**（2026-02-06 リリース） |
| 記述言語 | **日本語**（技術用語は英語併記） |
| 対象読者 | メジャーな言語（Python, Java）の基礎を知っている程度のプログラマー |
| 実行環境 | Linux もしくは WSL2 |
| 公式チュートリアル参考元 | https://tutorial.ponylang.io/ |
| 公式ドキュメント | https://www.ponylang.io/ |
| GitHub リポジトリ | https://github.com/ponylang/ponyc |

### 前提知識（読者が持っていると想定するもの）

- 変数、関数、クラス、条件分岐、ループなどの基本的なプログラミング概念
- Python もしくは Java での簡単なプログラム作成経験
- ターミナル（コマンドライン）の基本操作
- Linux / WSL2 環境の基本操作

### 前提知識として想定しないもの（チュートリアル内で説明が必要）

- アクターモデル（Actor Model）
- 参照能力（Reference Capabilities）
- 型推論の高度な仕組み
- コンパイル時のデータ競合検出
- Pony 固有の概念全般

---

## 2. 環境セットアップ手順

チュートリアルの Ch.00（Getting Started）で読者向けに記載する内容です。AI はこの手順を Ch.00 に含めてください。

### 2.1 ponyup のインストール

ponyup は Pony のツールチェーンマネージャーです。

```bash
# ponyup のインストール
sh -c "$(curl --proto '=https' --tlsv1.2 -sSf https://raw.githubusercontent.com/ponylang/ponyup/latest-release/ponyup-init.sh)"
```

インストール後、シェルを再起動するか以下を実行してパスを通します：

```bash
source ~/.bashrc  # または ~/.zshrc
```

### 2.2 ponyc のインストール

```bash
# ponyc の最新安定版をインストール
ponyup update ponyc release
```

バージョン確認：

```bash
ponyc --version
# 出力例: 0.60.6-xxxxxxx [release]
```

### 2.3 C コンパイラのインストール

Pony は C コンパイラ（clang 推奨）を必要とします。

```bash
# Ubuntu / Debian / WSL2 (Ubuntu)
sudo apt-get update
sudo apt-get install -y clang
```

### 2.4 サポートされるプラットフォーム

| プラットフォーム | バージョン |
|-----------------|-----------|
| Ubuntu | 22.04, 24.04 |
| Linux Mint | 19, 20, 21 |
| Pop!_OS | 22.04, 24.04 |
| Alpine | 3.17+ (musl libc) |

WSL2 の場合は、Ubuntu 22.04 以降のディストリビューションを推奨します。

### 2.5 プラットフォーム検出がうまくいかない場合

```bash
# Ubuntu 24.04 の場合
ponyup default x86_64-linux-ubuntu24.04

# Ubuntu 22.04 / Mint 21 の場合
ponyup default x86_64-linux-ubuntu22.04
```

---

## 3. 出力ファイル構成規約

### 3.1 ディレクトリ構造

チュートリアルの出力先は `tutorial/0.60.6/` です。

```
tutorial/0.60.6/
├── INDEX.md                       ← 全章リンク付き目次（全章完了後に作成）
├── 00-getting-started.md          ← Ch.00: Getting Started
├── 01-types.md                    ← Ch.01: Types
├── 02-expressions.md              ← Ch.02: Expressions
├── 03-reference-capabilities.md   ← Ch.03: Reference Capabilities
├── 04-object-capabilities.md      ← Ch.04: Object Capabilities
├── 05-generics.md                 ← Ch.05: Generics
├── 06-packages.md                 ← Ch.06: Packages
├── 07-testing.md                  ← Ch.07: Testing
├── 08-c-ffi.md                    ← Ch.08: C-FFI
├── 09-gotchas.md                  ← Ch.09: Gotchas
├── 10-where-next.md               ← Ch.10: Where Next?
└── 11-appendices.md               ← Ch.11: Appendices
```

### 3.2 ファイル命名規則

- ファイル名は `{2桁の章番号}-{英語のケバブケース}.md` 形式
- 章番号は `00` から始まる連番
- ファイル名は章の内容を端的に表す英語名

### 3.3 Appendices の分割について

Ch.11（Appendices）は内容量が非常に多い場合、以下のように分割することを許容します：

```
11-appendices/
├── README.md              ← Appendices 目次
├── 11a-ponypath.md
├── 11b-lexicon.md
├── 11c-symbol-cheatsheet.md
├── 11d-keywords.md
├── 11e-examples.md
├── 11f-compiler-arguments.md
├── 11g-memory-allocation.md
├── 11h-garbage-collection.md
├── 11i-platform-dependent.md
├── 11j-error-messages.md
├── 11k-annotations.md
└── 11l-serialisation.md
```

分割する場合は、`11-appendices.md` の代わりに `11-appendices/` ディレクトリを使用してください。

---

## 4. 章構成と各章の執筆指示

公式チュートリアル（ https://tutorial.ponylang.io/ ）の全範囲に対応する12ファイル構成です。各章の詳細な執筆指示を以下に示します。

---

### Ch.00: Getting Started（00-getting-started.md）

**目的**: 読者が Pony の開発環境を構築し、最初のプログラムを動かせるようにする

**含めるべき内容**:

1. **Pony とは何か**
   - Pony の特徴を3〜5点で紹介（アクターモデル、型安全、データ競合なし、高性能、C-FFI）
   - 「なぜ Pony を学ぶのか」のモチベーション
   - Python / Java との簡単なポジショニング比較

2. **環境セットアップ**
   - 本指示書の「2. 環境セットアップ手順」の内容を読者向けにわかりやすく記載
   - 手順ごとに期待される出力を示す

3. **Hello World — 最初の Pony プログラム**
   - ファイル作成（`main.pony`）
   - コード記述
   - コンパイル（`ponyc`）
   - 実行
   - 期待される出力

4. **Hello World — コード解説**
   - `actor Main` とは何か（Java の `public static void main` との対比）
   - `new create(env: Env)` コンストラクタの役割
   - `env.out.print("Hello, World!")` の意味
   - Pony のプログラムがアクターベースであることの導入

5. **Pony プロジェクトの基本構造**
   - ディレクトリ = パッケージ の概念
   - コンパイルの流れ（ソース → 実行ファイル）

**Python/Java 対比ポイント**:
- Python: `print("Hello, World!")` → Pony ではなぜ `env.out.print()` なのか（Object Capabilities の導入）
- Java: `public static void main(String[] args)` → Pony の `actor Main` の対応関係

---

### Ch.01: Types（01-types.md）

**目的**: Pony の型システムを体系的に理解する

**含めるべき内容**:

1. **Pony の型システム概観（The Pony Type System at a Glance）**
   - 名目的型付け（Nominal Typing）と構造的型付け（Structural Typing）の両方をサポート
   - すべての値がオブジェクト（プリミティブ型も含む）
   - Java/Python との型システムの違い

2. **クラス（Classes）**
   - クラス定義の構文
   - フィールド（`var` / `let` / `embed`）
   - コンストラクタ（`new`）
   - メソッド（`fun` / `be`）
   - Java のクラスとの対比

3. **プリミティブ（Primitives）**
   - 状態を持たない型
   - 列挙型としての使い方
   - シングルトンとしての特性
   - Python の定数 / Java の `enum` との対比

4. **アクター（Actors）**
   - アクターモデルの概念説明 ← **重要：読者は知らない想定**
   - アクター定義の構文
   - ビヘイビア（`be`）の概念
   - メッセージパッシング
   - 並行処理の基礎
   - Python の `threading` / Java の `Thread` との違い

5. **トレイトとインターフェース（Traits and Interfaces）**
   - トレイト：名目的サブタイピング（Nominal Subtyping）
   - インターフェース：構造的サブタイピング（Structural Subtyping）
   - 両者の使い分け
   - Java の `interface` / `abstract class` との対比

6. **構造体（Structs）**
   - C-FFI 向けの型
   - クラスとの違い
   - 使用場面

7. **型エイリアス（Type Aliases）**
   - `type` キーワードによる定義
   - ユニオン型（Union Types）・インターセクション型（Intersection Types）
   - Python の `TypeAlias` / Java のジェネリクスとの対比

8. **型式（Type Expressions）**
   - ユニオン型（`(A | B)`）
   - インターセクション型（`(A & B)`）
   - タプル型（`(A, B)`）

**コード例の要件**: 各サブセクションに最低1つの動作する完全なコード例を含む

---

### Ch.02: Expressions（02-expressions.md）

**目的**: Pony の式と構文を網羅的に理解する

**含めるべき内容**:

1. **リテラル（Literals）**
   - 数値リテラル（整数、浮動小数点）
   - 文字列リテラル（`String`）
   - 文字リテラル
   - 配列リテラル
   - ブールリテラル（`true` / `false`）

2. **変数（Variables）**
   - `var`（可変）と `let`（不変）
   - 型推論
   - Python / Java の変数宣言との対比

3. **演算子（Operators）**
   - 算術演算子、比較演算子、論理演算子
   - 演算子のオーバーロード（シュガー）
   - Pony では演算子がメソッド呼び出しである点

4. **算術（Arithmetic）**
   - 安全な算術（Partial arithmetic / Unsafe arithmetic）
   - オーバーフロー処理
   - Python / Java の算術との違い

5. **制御構造（Control Structures）**
   - `if` / `else` / `elseif`
   - `while` / `for` ループ
   - `repeat` ... `until`
   - **Pony の `if` は式**であること（値を返す）
   - Python / Java の制御構造との対比

6. **Match 式（Match Expressions）**
   - パターンマッチングの構文
   - 値マッチング、型マッチング
   - ガード条件
   - Python の `match` / Java の `switch` との対比

7. **As 演算子（As Operator）**
   - 型変換（型キャスト）
   - 安全なキャスト vs partial キャスト

8. **メソッド（Methods）**
   - `fun`（関数）、`be`（ビヘイビア）、`new`（コンストラクタ）
   - 引数とデフォルト値
   - 戻り値の型
   - メソッドの参照能力アノテーション（`fun ref`、`fun val` 等 — 詳細は Ch.03 へリンク）

9. **エラー（Errors）**
   - `error` キーワード
   - Partial 関数（`?`）
   - `try` / `else` / `then`
   - Java の例外処理との対比（Pony には例外クラス階層がない）

10. **等価性（Equality in Pony）**
    - 同一性（Identity equality: `is`）
    - 構造的等価性（Structural equality: `==`）
    - `Equatable` インターフェース
    - Python の `is` / `==`、Java の `==` / `.equals()` との対比

11. **シュガー（Sugar）**
    - `apply` メソッド
    - `update` メソッド
    - 演算子シュガー
    - イテレータとの関連

12. **オブジェクトリテラル（Object Literals）**
    - 無名オブジェクトの作成
    - クロージャとの関連（ラムダ構文）
    - Java の匿名クラス / ラムダ式との対比

13. **部分適用（Partial Application）**
    - 関数の部分適用の構文
    - 関数を値として扱う

**コード例の要件**: 各サブセクションに最低1つのコード例。特に制御構造、match式、エラー処理には複数の例を含む

---

### Ch.03: Reference Capabilities（03-reference-capabilities.md）

**目的**: Pony 最大の特徴である参照能力を深く理解する。**この章が本チュートリアル最大の山場であり、最も丁寧な説明が必要**。

**含めるべき内容**:

1. **参照能力とは何か（Reference Capabilities）**
   - なぜ参照能力が必要なのか（データ競合の防止）
   - コンパイル時安全性の保証
   - 他言語（Rust の所有権モデル等）との簡単な比較

2. **参照能力の保証（Reference Capability Guarantees）**
   - 各参照能力の読み書き特性
   - 6つの参照能力の概要表：

     | 能力 | エイリアス可能 | 読み取り | 書き込み | 特徴 |
     |------|-------------|---------|---------|------|
     | `iso` | 不可 | 可 | 可 | 分離された可変参照 |
     | `trn` | 不可(書込) | 可 | 可 | 書き込み一意 |
     | `ref` | 可 | 可 | 可 | 可変参照 |
     | `val` | 可 | 可 | 不可 | 不変値 |
     | `box` | 可 | 可 | 不可 | 読み取り専用参照 |
     | `tag` | 可 | 不可 | 不可 | 同一性のみ |

3. **Consume とデストラクティブリード（Consume and Destructive Read）**
   - `consume` キーワードの使い方
   - なぜ consume が必要か
   - コード例で動作を示す

4. **Recovering Capabilities（能力の回復）**
   - `recover` ブロック
   - iso / val への昇格
   - 使用パターン

5. **エイリアシング（Aliasing）**
   - エイリアスとは何か
   - 各参照能力がエイリアスされたときの変化
   - エイリアスの互換性表

6. **参照の受け渡しと共有（Passing and Sharing References）**
   - アクター間で値を安全に送信する方法
   - `iso` → 送信可能（sendable）
   - `val` → 共有可能（shareable）
   - `tag` → 同一性のみ
   - 並行処理における安全性

7. **能力のサブタイピング（Capability Subtyping）**
   - サブタイプ関係の図解
   - `iso <: trn <: ref <: box`
   - `iso <: val <: box`
   - `box <: tag`

8. **能力の組み合わせ（Combining Capabilities）**
   - フィールドアクセス時の能力の組み合わせ
   - 受信者能力とフィールド能力の関係

9. **Arrow Types（ビューポイント）**
   - `this->` 記法
   - ジェネリクスでの使用

10. **参照能力マトリクス（Reference Capability Matrix）**
    - 全能力の関係を一覧表として提示
    - 各能力間の変換の可否

**図表の推奨**: 
- 能力の階層図（サブタイプ関係）
- エイリアシング時の変化表
- 送信可能性の表
- 能力をMermaid記法やASCIIアートで視覚化

**Python/Java 対比ポイント**:
- Python: すべてが参照で、GIL がデータ競合を防ぐ → Pony はコンパイル時に防ぐ
- Java: `synchronized` / `volatile` / `final` → Pony では型レベルで保証

---

### Ch.04: Object Capabilities（04-object-capabilities.md）

**目的**: Pony のセキュリティモデルであるオブジェクト能力を理解する

**含めるべき内容**:

1. **オブジェクト能力とは何か（Object Capabilities）**
   - 能力ベースのセキュリティモデル
   - 環境（`Env`）を通じたシステムリソースへのアクセス
   - なぜ `Main` アクターが `Env` を受け取るのか（Ch.00 の伏線回収）

2. **派生権限（Derived Authority）**
   - 権限の委譲
   - 最小権限の原則

3. **信頼境界（Trust Boundary）**
   - 信頼できるコードと信頼できないコードの境界
   - パッケージによる信頼境界の管理

**Python/Java 対比ポイント**:
- Python: `import os` でどこでもファイルアクセス可能 → Pony ではMain から権限を受け渡す必要がある
- Java のセキュリティマネージャとの類似と相違

---

### Ch.05: Generics（05-generics.md）

**目的**: ジェネリクスと参照能力の関係を理解する

**含めるべき内容**:

1. **ジェネリクスと参照能力（Generics and Reference Capabilities）**
   - ジェネリック型パラメータの構文
   - 参照能力をジェネリクスでどう扱うか
   - Java のジェネリクスとの対比

2. **制約（Constraints）**
   - 型パラメータの制約構文
   - 参照能力による制約
   - デフォルトの制約
   - Java の `<T extends ...>` との対比

---

### Ch.06: Packages（06-packages.md）

**目的**: Pony のパッケージシステムと標準ライブラリを理解する

**含めるべき内容**:

1. **Use 文（Use Statement）**
   - `use` キーワードの構文
   - パッケージのインポート
   - 条件付き use
   - Python の `import` / Java の `import` との対比

2. **標準ライブラリ（Standard Library）**
   - 主要パッケージの紹介（collections, files, net, time 等）
   - よく使うクラス・プリミティブの紹介
   - 標準ライブラリのドキュメント参照先

---

### Ch.07: Testing（07-testing.md）

**目的**: Pony でのテスト手法を理解する

**含めるべき内容**:

1. **PonyTest でテストする（Testing with PonyTest）**
   - テストクラスの作成方法
   - テストの実行方法
   - アサーション
   - Python の `unittest` / Java の JUnit との対比

2. **PonyCheck でテストする（Testing with PonyCheck）**
   - プロパティベーステスト
   - ジェネレータの使い方
   - Python の `hypothesis`、Java の QuickCheck 系ライブラリとの対比

---

### Ch.08: C-FFI（08-c-ffi.md）

**目的**: Pony から C ライブラリを呼び出す方法を理解する

**含めるべき内容**:

1. **Pony から C を呼び出す（Calling C from Pony）**
   - `@` 記法によるC関数呼び出し
   - 引数と戻り値の型マッピング
   - `use "lib:..."` によるライブラリ指定

2. **C ライブラリとのリンク（Linking to C Libraries）**
   - リンク方法
   - ライブラリパス

3. **C ABI**
   - Application Binary Interface の概要
   - データレイアウト

4. **コールバック（Callbacks）**
   - C から Pony を呼び出すコールバック
   - 注意点と制限

**Python/Java 対比ポイント**:
- Python の `ctypes` / `cffi`
- Java の JNI / JNA

---

### Ch.09: Gotchas（09-gotchas.md）

**目的**: Pony を使う上での典型的な落とし穴を紹介する

**含めるべき内容**:

1. **ゼロ除算（Divide by Zero）** — Pony ではパニックしない
2. **ガベージコレクション（Garbage Collection）** — アクターごとの GC
3. **スケジューリング（Scheduling）** — アクターのスケジューリング特性
4. **関数呼び出しの副作用（Function Call Side Effects）** — 評価順序の注意
5. **再帰（Recursion）** — スタックオーバーフローの可能性

**各項目で**:
- 問題が起きるコード例
- なぜ問題が起きるかの説明
- 正しい対処法のコード例

---

### Ch.10: Where Next?（10-where-next.md）

**目的**: チュートリアル修了後の次のステップを案内する

**含めるべき内容**:

1. **さらなる学習リソース**
   - 公式ドキュメント（https://www.ponylang.io/）
   - 公式チュートリアル（https://tutorial.ponylang.io/）
   - Pony Patterns（https://patterns.ponylang.io/）
   - 標準ライブラリリファレンス

2. **コミュニティ**
   - Zulip チャット
   - GitHub Discussions
   - IRC / Discord（存在する場合）

3. **実践的なプロジェクトアイデア**
   - Pony で作れるもののアイデアを3〜5個提案

---

### Ch.11: Appendices（11-appendices.md）

**目的**: リファレンスとして参照できる付録情報

**含めるべきサブセクション**:

1. **PONYPATH** — 環境変数によるパッケージ検索パスの設定
2. **用語集（Lexicon）** — Pony 固有の用語と定義
3. **シンボル早見表（Symbol Lookup Cheat Sheet）** — 記号の意味一覧
4. **キーワード一覧（Keywords）** — 全予約語の一覧と簡単な説明
5. **サンプル集（Examples）** — 実用的なコードスニペット集
6. **空白文字（Whitespace）** — Pony における空白の扱い
7. **コンパイラ引数（Compiler Arguments）** — `ponyc` のコマンドラインオプション
8. **メモリ割り当て（Memory Allocation at Runtime）** — ランタイムのメモリ管理
9. **ガベージコレクション: Pony-ORCA（Garbage Collection with Pony-ORCA）** — ORCA プロトコルの概要
10. **プラットフォーム依存コード（Platform-dependent Code）** — `ifdef` の使い方
11. **エラーメッセージガイド（A Short Guide to Pony Error Messages）** — よくあるエラーと解決法
12. **プログラムアノテーション（Program Annotations）** — `\packed\` 等のアノテーション
13. **シリアライゼーション（Serialisation）** — データのシリアライズ/デシリアライズ

**注意**: 量が多い場合はディレクトリ分割を許容（3.3節参照）

---

## 5. 執筆スタイルガイド

### 5.1 言語と表記

- **記述言語**: 日本語
- **技術用語**: 英語併記を基本とする
  - 例：「参照能力（Reference Capabilities）」「アクター（Actor）」「トレイト（Trait）」
  - 初出時は必ず英語を併記。2回目以降は日本語のみでも可
- **コード中のコメント**: 日本語で記述

### 5.2 コード例

- **コードブロック**: ` ```pony ` でマークアップ
- **動作する完全なコード例**: 各トピックに最低1つの、コンパイル・実行可能な完全なコード例を含む
- **断片的なコード例**: 構文の説明には断片的なコードも使用可
- **出力例**: コード例に対して期待される出力を ```` ```text ```` ブロックで示す
- **Pony 0.60.6 準拠**: すべてのコード例は Pony 0.60.6 で構文的に正しいこと

### 5.3 Python/Java 対比

各章で適切な箇所に Python / Java との対比を入れる。形式は以下を推奨：

```markdown
> **Python/Java との比較**
> - Python では `...` ですが、Pony では `...` です
> - Java の `...` に相当するのが Pony の `...` です
```

または表形式：

```markdown
| 概念 | Python | Java | Pony |
|------|--------|------|------|
| xxx  | ...    | ...  | ...  |
```

### 5.4 見出しレベル

| レベル | 用途 | 例 |
|--------|------|-----|
| `#`    | 章タイトル | `# Ch.00: Getting Started` |
| `##`   | トピック | `## Hello World — 最初の Pony プログラム` |
| `###`  | サブトピック | `### コード解説` |
| `####` | 補足・注意 | `#### 注意点` |

### 5.5 分量の目安

| 章 | 目安語数 | 備考 |
|----|---------|------|
| Ch.00 Getting Started | 2,000〜3,000語 | 環境構築 + Hello World |
| Ch.01 Types | 4,000〜6,000語 | セクション数が多い |
| Ch.02 Expressions | 5,000〜7,000語 | 最もセクション数が多い章 |
| Ch.03 Reference Capabilities | 5,000〜8,000語 | **最重要章・最も丁寧に** |
| Ch.04 Object Capabilities | 2,000〜3,000語 | 概念中心 |
| Ch.05 Generics | 2,000〜3,000語 | |
| Ch.06 Packages | 2,000〜3,000語 | |
| Ch.07 Testing | 3,000〜4,000語 | |
| Ch.08 C-FFI | 3,000〜5,000語 | |
| Ch.09 Gotchas | 2,000〜3,000語 | |
| Ch.10 Where Next? | 1,000〜1,500語 | |
| Ch.11 Appendices | 5,000〜8,000語 | リファレンス集 |

### 5.6 特殊な表記

- **重要な注意点**: `> ⚠️ **注意**: ...` 形式のブロック引用
- **ヒント**: `> 💡 **ヒント**: ...` 形式のブロック引用
- **Pony の哲学**: Pony の設計思想に触れる場合は `> 🐴 **Pony の哲学**: ...` 形式で強調

---

## 6. 品質基準・レビューチェックリスト

各章の生成後、以下の項目をチェックしてください。

### 6.1 コード品質

- [ ] すべてのコード例が Pony 0.60.6 の構文に準拠している
- [ ] 「完全な例」として示したコードが、実際にコンパイル・実行可能である
- [ ] コード例にインデントのズレがない
- [ ] コードブロックの言語指定が正しい（`pony`, `bash`, `text` 等）

### 6.2 内容品質

- [ ] 章の冒頭に、その章で学ぶ内容の概要がある
- [ ] 章の末尾に「まとめ」セクションがある
- [ ] 専門用語の初出時に必ず説明がある
- [ ] Python / Java との対比が適切な箇所に含まれている
- [ ] 前の章で説明した内容を前提にする場合、章へのリンクがある

### 6.3 構成品質

- [ ] 見出しレベルが正しい（`#`→`##`→`###` の階層）
- [ ] ファイル名が命名規則に準拠している
- [ ] 目次（INDEX.md）のリンクが正しい（全章完了後に確認）

### 6.4 読者体験

- [ ] 前提知識の範囲外の概念が突然登場していない
- [ ] 段階的に難易度が上がる構成になっている
- [ ] 「なぜそうなのか」の説明が十分にある（文法の羅列ではない）

---

## 7. 実行手順（AIへの指示フロー）

### 7.1 章ごとの生成フロー

以下のサイクルを Ch.00 から Ch.11 まで繰り返します。

```
1. ユーザーが AI に章番号を指定して生成を依頼
   例：「Ch.00 を TUTORIAL_GUIDE.md の指示に従って生成してください」

2. AI が指示書に従って該当章を生成
   → tutorial/0.60.6/{ファイル名}.md に出力

3. ユーザーがレビュー
   → フィードバックがあれば修正を依頼

4. 修正が完了したら次の章へ
```

### 7.2 INDEX.md の生成

全章（Ch.00 〜 Ch.11）の生成が完了した後、`tutorial/0.60.6/INDEX.md` を生成します。

INDEX.md の想定フォーマット：

```markdown
# Pony チュートリアル（v0.60.6）目次

## 目次

1. [Getting Started](00-getting-started.md)
2. [Types](01-types.md)
3. [Expressions](02-expressions.md)
4. [Reference Capabilities](03-reference-capabilities.md)
5. [Object Capabilities](04-object-capabilities.md)
6. [Generics](05-generics.md)
7. [Packages](06-packages.md)
8. [Testing](07-testing.md)
9. [C-FFI](08-c-ffi.md)
10. [Gotchas](09-gotchas.md)
11. [Where Next?](10-where-next.md)
12. [Appendices](11-appendices.md)
```

### 7.3 全体整合性チェック

全章完了後に以下を確認：

- [ ] 全12ファイル + INDEX.md が揃っている
- [ ] INDEX.md のリンクが全て正しい
- [ ] 章をまたぐ参照リンクが正しい
- [ ] 用語の表記が全章で統一されている
- [ ] 参照能力の説明が Ch.03 に集約され、他の章では適切にリンクしている

### 7.4 AI への生成依頼テンプレート

章の生成を依頼する際は、以下のテンプレートを使用できます：

```
TUTORIAL_GUIDE.md の指示に従って、Ch.{番号}（{章名}）を生成してください。

出力先: tutorial/0.60.6/{ファイル名}.md

追加の指示（あれば）:
- （特に重点的に説明してほしい点など）
```

---

## 8. 参考リソース

| リソース | URL |
|---------|-----|
| Pony 公式サイト | https://www.ponylang.io/ |
| Pony 公式チュートリアル | https://tutorial.ponylang.io/ |
| Pony GitHub | https://github.com/ponylang/ponyc |
| Pony 標準ライブラリ | https://stdlib.ponylang.io/ |
| Pony Patterns | https://patterns.ponylang.io/ |
| Pony Zulip | https://ponylang.zulipchat.com/ |
| ponyup（ツールチェーン） | https://github.com/ponylang/ponyup |
| Pony 0.60.6 リリースノート | https://github.com/ponylang/ponyc/releases/tag/0.60.6 |

---

*本指示書は必要に応じて更新されます。チュートリアル生成中に判明した問題や改善点は、フィードバックを通じて本指示書に反映してください。*
