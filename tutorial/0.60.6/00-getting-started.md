# Ch.00: Getting Started

この章では、プログラミング言語 Pony の概要を学び、開発環境をセットアップし、最初のプログラムを作成・実行します。

---

## Pony とは何か

**Pony** は、安全で高速な並行プログラミングのために設計されたオープンソースのプログラミング言語です。以下の特徴を持っています：

1. **アクターモデル（Actor Model）ベース** — 並行処理の基本単位として「アクター」を採用。メッセージパッシングによる安全な並行プログラミングが可能です。
2. **型安全（Type Safe）** — 強力な静的型システムにより、多くのバグをコンパイル時に検出します。
3. **データ競合なし（Data-Race Free）** — 「参照能力（Reference Capabilities）」という独自の仕組みにより、コンパイル時にデータ競合がないことを保証します。ランタイムのロックやミューテックスは不要です。
4. **高性能（High Performance）** — ネイティブコードにコンパイルされ、ガベージコレクション（GC）もアクターごとに独立して動作するため、GC による停止時間が最小限に抑えられます。
5. **C-FFI（Foreign Function Interface）** — C ライブラリを直接呼び出せるため、既存の C エコシステムを活用できます。

### なぜ Pony を学ぶのか

現代のソフトウェアは並行処理が当たり前になっています。しかし、多くの言語では並行プログラミングにおけるデータ競合やデッドロックの回避はプログラマーの注意力に依存しています。

Pony はこの問題に対して、**言語レベルでの安全性保証**というアプローチを取ります。正しくコンパイルが通れば、データ競合が発生しないことが型システムによって保証されるのです。

> 🐴 **Pony の哲学**: 「安全であることを、プログラマーの注意力ではなく、コンパイラに保証させる」

### Python / Java との比較

| 特性 | Python | Java | Pony |
|------|--------|------|------|
| 実行方式 | インタープリタ | JVM バイトコード | ネイティブコード |
| 型付け | 動的型付け | 静的型付け | 静的型付け |
| 並行処理 | GIL による制限 | スレッド + ロック | アクターモデル |
| データ競合防止 | GIL（制限的） | `synchronized` 等（手動） | コンパイル時保証（自動） |
| メモリ管理 | 参照カウント + GC | GC（全体停止あり） | アクターごとの GC |
| ヌル安全 | なし | 限定的（`Optional`） | 型レベルで保証 |

---

## 環境セットアップ

Pony の開発環境を Linux または WSL2 上に構築します。

### 前提条件

- Linux（Ubuntu 22.04 以降推奨）または WSL2 環境
- ターミナルから `curl` コマンドが実行できること
- `sudo` 権限があること

### ステップ 1: ponyup のインストール

**ponyup** は Pony のツールチェーンマネージャーです。Python における `pyenv`、Java における `SDKMAN!` に相当するツールです。

```bash
sh -c "$(curl --proto '=https' --tlsv1.2 -sSf https://raw.githubusercontent.com/ponylang/ponyup/latest-release/ponyup-init.sh)"
```

インストール後、シェルを再起動するか、以下のコマンドでパスを通します：

```bash
source ~/.bashrc  # zsh の場合は ~/.zshrc
```

正しくインストールされたことを確認します：

```bash
ponyup version
```

```text
ponyup X.Y.Z-... [release]
```

のような出力が表示されれば成功です。

### ステップ 2: ponyc のインストール

**ponyc** は Pony のコンパイラです。ponyup を使ってインストールします。

```bash
ponyup update ponyc release
```

バージョンを確認します：

```bash
ponyc --version
```

```text
0.60.6-xxxxxxx [release]
```

> ⚠️ **注意**: バージョン番号のハイフン以降の部分（コミットハッシュ）は環境によって異なります。`0.60.6` であることが重要です。

### ステップ 3: C コンパイラのインストール

Pony はコンパイル時に C コンパイラを使用します。**clang** が公式に推奨されています。

```bash
# Ubuntu / Debian / WSL2 (Ubuntu)
sudo apt-get update
sudo apt-get install -y clang
```

インストールを確認します：

```bash
clang --version
```

### ステップ 4: セットアップの確認

すべてのツールが正しくインストールされているか確認しましょう：

```bash
ponyup version && ponyc --version && clang --version
```

3つすべてのコマンドがエラーなく出力を返せば、環境セットアップは完了です。

### サポートされるプラットフォーム

| プラットフォーム | バージョン |
|-----------------|-----------|
| Ubuntu | 22.04, 24.04 |
| Linux Mint | 19, 20, 21 |
| Pop!_OS | 22.04, 24.04 |
| Alpine | 3.17+（musl libc） |

WSL2 を使用する場合は、**Ubuntu 22.04 以降**のディストリビューションを推奨します。

### トラブルシューティング: プラットフォーム検出の問題

ponyup がプラットフォームを正しく検出できない場合は、手動で指定できます：

```bash
# Ubuntu 24.04 の場合
ponyup default x86_64-linux-ubuntu24.04

# Ubuntu 22.04 / Mint 21 の場合
ponyup default x86_64-linux-ubuntu22.04
```

---

## Hello World — 最初の Pony プログラム

環境が整ったら、最初の Pony プログラムを作成しましょう。

### プロジェクトディレクトリの作成

Pony では、**ディレクトリがパッケージ**を表します。ディレクトリ名がデフォルトで実行ファイルの名前になります。

```bash
mkdir helloworld
cd helloworld
```

### ソースファイルの作成

ディレクトリ内に `main.pony` というファイルを作成します：

```bash
cat > main.pony << 'EOF'
actor Main
  new create(env: Env) =>
    env.out.print("Hello, world!")
EOF
```

> 💡 **ヒント**: お好みのテキストエディタ（`vim`、`nano`、VS Code など）を使ってファイルを作成しても構いません。

### コンパイル

ディレクトリ内で `ponyc` コマンドを実行します：

```bash
ponyc
```

以下のような出力が表示されます：

```text
Building .
Building builtin
Generating
Optimising
Writing ./helloworld.o
Linking ./helloworld
```

`ponyc` は現在のディレクトリ（`.`）と組み込みライブラリ（`builtin`）をビルドし、コードの生成・最適化・リンクを自動的に行います。Make や Gradle のようなビルドシステムは必要ありません。

### 実行

生成された実行ファイルを実行します：

```bash
./helloworld
```

```text
Hello, world!
```

おめでとうございます！ 最初の Pony プログラムが動きました。

> **Python/Java との比較**
> - Python: `python hello.py` — インタープリタがソースを直接実行
> - Java: `javac Hello.java` → `java Hello` — コンパイル後にJVMで実行
> - Pony: `ponyc` → `./helloworld` — コンパイル後にネイティブバイナリとして直接実行

---

## Hello World — コード解説

3行のコードを1行ずつ詳しく見ていきましょう。

```pony
actor Main
  new create(env: Env) =>
    env.out.print("Hello, world!")
```

### 1行目: `actor Main`

```pony
actor Main
```

`actor` キーワードは**アクター（Actor）**を定義します。アクターは Pony における並行処理の基本単位で、クラスに似ていますが、**非同期に実行されるメソッド（ビヘイビア）**を持てる点が異なります。

Pony のプログラムには、**必ず `Main` という名前のアクター**が必要です。これがプログラムのエントリーポイント（開始地点）になります。

> **Python/Java との比較**
> 
> | 言語 | エントリーポイント |
> |------|------------------|
> | Python | スクリプトの先頭から実行（`if __name__ == "__main__":` が慣習） |
> | Java | `public static void main(String[] args)` メソッド |
> | Pony | `actor Main` の `create` コンストラクタ |

アクターモデルについては [Ch.01: Types](01-types.md) で詳しく学びます。ここでは「`Main` はプログラムの入り口」と覚えておけば十分です。

### 2行目: `new create(env: Env) =>`

```pony
new create(env: Env) =>
```

この行には複数の重要な要素があります：

- **`new`** — コンストラクタを定義するキーワードです。Pony のコンストラクタには**名前**があります（ここでは `create`）。1つの型に複数のコンストラクタを定義できます。
- **`create`** — コンストラクタの名前です。`Main` アクターは、**`create` という名前のコンストラクタ**を持つ必要があります。
- **`env: Env`** — パラメータ名 `env`、型 `Env`。Pony では型は名前の**後ろ**にコロン（`:`）で区切って書きます。

  > 💡 **ヒント**: この「名前: 型」の順序は、Go や Rust、TypeScript と同じ形式です。Java の `String[] args` のような「型 名前」の逆順です。

- **`Env`** — プログラムが呼び出された「環境（Environment）」を表す型です。コマンドライン引数、環境変数、標準入出力（`stdin`、`stdout`、`stderr`）へのアクセスを提供します。
- **`=>`** — コンストラクタの本体（処理内容）の開始を示す記号です。

> ⚠️ **注意**: Pony には**グローバル変数がありません**。他の言語では `System.out`（Java）や `sys.stdout`（Python）のようにどこからでもアクセスできる標準出力が、Pony では `Env` オブジェクトを通じてのみアクセスできます。これは Pony の「オブジェクト能力（Object Capabilities）」モデルの一部です（詳細は [Ch.04: Object Capabilities](04-object-capabilities.md) で学びます）。

### 3行目: `env.out.print("Hello, world!")`

```pony
env.out.print("Hello, world!")
```

この行をドット（`.`）で区切って見ていきます：

1. **`env`** — `create` コンストラクタで受け取った環境オブジェクトへの参照
2. **`env.out`** — `env` の `out` フィールドにアクセス。これは標準出力（`stdout`）を表します
3. **`.print("Hello, world!")`** — `out` オブジェクトの `print` メソッドを呼び出し、文字列を出力

Pony では、括弧 `()` がある場合はメソッド呼び出し、ない場合はフィールドアクセスです。

文字列リテラル（String Literal）は、`"..."` のようにダブルクォートで囲みます。Python と同様に、`"""..."""` のようなトリプルクォートも使えます。

> **Python/Java との比較**
> ```python
> # Python — グローバル関数でどこからでも出力可能
> print("Hello, world!")
> ```
> ```java
> // Java — System.out はグローバルにアクセス可能
> System.out.println("Hello, world!");
> ```
> ```pony
> // Pony — env を通じてのみ出力にアクセスできる
> env.out.print("Hello, world!")
> ```
> 
> Pony で `env.out.print()` と書く必要があるのは、標準出力への「アクセス権」が `env` を通じて明示的に渡されるためです。これは制限ではなく、**セキュリティと安全性のための設計**です。

---

## Pony プロジェクトの基本構造

### ディレクトリ = パッケージ

Pony では、**1つのディレクトリが1つのパッケージ**を構成します。ディレクトリ内のすべての `.pony` ファイルが同じパッケージに属します。

```
helloworld/        ← パッケージ名 = "helloworld"（＝実行ファイル名）
├── main.pony      ← Main アクターを含むソース
└── (他の .pony ファイルも同じパッケージに属する)
```

> **Python/Java との比較**
> - Python: 1つのディレクトリ + `__init__.py` = パッケージ
> - Java: `package` 宣言 + ディレクトリ構造
> - Pony: ディレクトリ自体がパッケージ（宣言不要）

### コンパイルの流れ

Pony のコンパイルは以下の流れで行われます：

```
.pony ソースファイル
    ↓  ponyc（コンパイラ）
構文解析 → 型チェック → コード生成 → 最適化
    ↓
.o オブジェクトファイル
    ↓  リンク
実行ファイル（ネイティブバイナリ）
```

重要なポイント：

- **ビルドツール不要**: `ponyc` がコンパイル・最適化・リンクをすべて行います
- **型チェック**: コンパイル時に型の整合性、参照能力の正しさ、データ競合の有無がすべてチェックされます
- **ネイティブバイナリ**: 生成される実行ファイルは、JVM や Python インタープリタのようなランタイムを必要としません

### 実行ファイル名のカスタマイズ

デフォルトではディレクトリ名が実行ファイル名になりますが、`--bin-name`（`-b`）オプションで変更できます：

```bash
ponyc -b my_program
```

```text
Building .
Building builtin
Generating
Optimising
Writing ./my_program.o
Linking ./my_program
```

### 複数ファイルのプロジェクト

パッケージ内に複数の `.pony` ファイルを置くこともできます。すべて同じパッケージとして扱われます：

```
myproject/
├── main.pony       ← Main アクターの定義
├── helper.pony     ← ヘルパー関数の定義
└── types.pony      ← 型の定義
```

ファイル名はコンパイラにとって重要ではありません（`.pony` 拡張子であればどんな名前でも構いません）。ファイルの分割は純粋にコードの整理のためです。

---

## まとめ

この章では以下のことを学びました：

| 項目 | 内容 |
|------|------|
| **Pony の特徴** | アクターモデル、型安全、データ競合なし、高性能、C-FFI |
| **環境構築** | ponyup → ponyc → clang のインストール |
| **Hello World** | `actor Main` + `new create(env: Env)` + `env.out.print()` |
| **コンパイルと実行** | `ponyc` でコンパイル → ネイティブバイナリを直接実行 |
| **プロジェクト構造** | ディレクトリ = パッケージ、ビルドツール不要 |

### 次の章へ

次章 [Ch.01: Types](01-types.md) では、Pony の型システムを学びます。クラス、プリミティブ、アクター、トレイト、インターフェースなど、Pony の型の全体像を体系的に理解していきましょう。

### Key Terms（この章で登場した用語）

| 用語 | 英語 | 説明 |
|------|------|------|
| アクター | Actor | 並行処理の基本単位。非同期メソッド（ビヘイビア）を持てる |
| ビヘイビア | Behaviour | アクターの非同期メソッド |
| コンストラクタ | Constructor | オブジェクトを生成するメソッド。Pony では名前付き |
| 参照能力 | Reference Capabilities | データ競合をコンパイル時に防ぐ Pony の型システムの仕組み |
| オブジェクト能力 | Object Capabilities | システムリソースへのアクセスを制御するセキュリティモデル |
| パッケージ | Package | ディレクトリに対応するコードの単位 |
| ponyup | — | Pony のツールチェーンマネージャー |
| ponyc | — | Pony のコンパイラ |
