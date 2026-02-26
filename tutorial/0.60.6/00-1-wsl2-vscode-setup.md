# Ch.00-1: WSL2 + VS Code 環境構築ガイド

この章では、**Windows 上の WSL2 と VS Code** を使って Pony の開発環境を構築する具体的な手順を解説します。Ch.00 の環境セットアップをより詳細に、WSL2 + VS Code に特化した形で手順化したものです。

---

## 前提条件

| 項目 | 要件 |
|------|------|
| OS | Windows 10（Build 19041 以降）または Windows 11 |
| VS Code | インストール済み（未インストールの場合は手順に含む） |
| 管理者権限 | PowerShell を管理者として実行できること |

---

## ステップ 1: WSL2 のインストール

### 1.1 WSL2 を有効化する

**PowerShell を管理者として起動**し、以下のコマンドを実行します：

```powershell
wsl --install
```

このコマンドで以下が自動的に行われます：

- WSL 機能の有効化
- 仮想マシンプラットフォームの有効化
- **Ubuntu**（デフォルトディストリビューション）のインストール
- WSL 2 をデフォルトバージョンに設定

```text
インストール中: Ubuntu
Ubuntu がインストールされました。
要求された操作は正常に終了しました。変更を有効にするには、システムを再起動する必要があります。
```

> ⚠️ **注意**: インストール完了後、**PC の再起動が必要**です。

### 1.2 Ubuntu の初期設定

再起動後、スタートメニューから **Ubuntu** を起動します。初回起動時にユーザー名とパスワードの設定を求められます：

```text
Enter new UNIX username: あなたのユーザー名
New password: パスワード
Retype new password: パスワード（再入力）
```

### 1.3 WSL2 で動作していることを確認

PowerShell で以下を実行して、WSL 2 で動作していることを確認します：

```powershell
wsl --list --verbose
```

```text
  NAME      STATE           VERSION
* Ubuntu    Running         2
```

`VERSION` が **2** になっていれば OK です。もし 1 になっている場合は以下で変換できます：

```powershell
wsl --set-version Ubuntu 2
```

### 1.4 Ubuntu を最新状態にする

Ubuntu ターミナルで以下を実行し、パッケージを最新にします：

```bash
sudo apt-get update && sudo apt-get upgrade -y
```

---

## ステップ 2: VS Code のセットアップ

### 2.1 VS Code のインストール（未インストールの場合）

[https://code.visualstudio.com/](https://code.visualstudio.com/) から **Windows 版** の VS Code をダウンロード・インストールします。

> ⚠️ **注意**: VS Code は **Windows 側**にインストールします。WSL2 内にインストールする必要はありません。

### 2.2 WSL 拡張機能のインストール

VS Code を起動し、以下の拡張機能をインストールします：

1. **WSL**（`ms-vscode-remote.remote-wsl`）
   - サイドバーの拡張機能アイコン（□が4つのマーク）をクリック
   - 検索欄に「WSL」と入力
   - **Microsoft** 発行の「WSL」をインストール

この拡張機能により、VS Code から WSL2 内のファイルをシームレスに編集・実行できるようになります。

### 2.3 Pony シンタックスハイライト拡張機能のインストール

`.pony` ファイルのシンタックスハイライト（色分け表示）を有効にします：

1. 拡張機能の検索欄に「Pony」と入力
2. **Pony Language Colorizer**（`npruehs.pony`）をインストール

> 💡 **ヒント**: この拡張機能はシンタックスハイライトのみを提供します。現時点では Pony にはコード補完やリアルタイムエラー表示を行う Language Server は成熟したものがないため、コンパイルエラーは `ponyc` コマンドで確認します。

### 2.4 推奨 VS Code 設定

VS Code の設定（`Ctrl + ,`）で以下を設定するとデバッグ時に便利です：

```json
{
  "debug.allowBreakpointsEverywhere": true
}
```

この設定により、`.pony` ファイル内にもブレークポイントを設置できるようになります（GDB/LLDB を使ったデバッグ時に使用）。

### 2.5 拡張機能の一覧

| 拡張機能 | 拡張機能 ID | 用途 | 必須/推奨 |
|---------|------------|------|----------|
| WSL | `ms-vscode-remote.remote-wsl` | WSL2 内の開発 | **必須** |
| Pony Language Colorizer | `npruehs.pony` | `.pony` シンタックスハイライト | **推奨** |
| C/C++ | `ms-vscode.cpptools` | GDB/LLDB デバッグ | 任意 |

---

## ステップ 3: VS Code から WSL2 に接続する

### 3.1 WSL2 への接続

VS Code で WSL2 に接続する方法は2つあります：

**方法 A: VS Code のコマンドパレットから接続**

1. `Ctrl + Shift + P` でコマンドパレットを開く
2. 「WSL: Connect to WSL」と入力して選択
3. VS Code が WSL2 内に必要なサーバーコンポーネントを自動インストール

**方法 B: Ubuntu ターミナルから VS Code を起動**

```bash
code .
```

Ubuntu ターミナルで `code` コマンドを実行すると、Windows 側の VS Code が WSL2 に接続した状態で開きます。

### 3.2 接続の確認

VS Code の左下に以下のような表示があれば、WSL2 に接続できています：

```
WSL: Ubuntu
```

> 💡 **ヒント**: WSL2 接続中の VS Code ターミナル（`` Ctrl + ` ``）は、自動的に Ubuntu のシェルになります。ここで `ponyc` 等の Linux コマンドを直接実行できます。

---

## ステップ 4: Pony ツールチェーンのインストール

VS Code の WSL2 ターミナル（または Ubuntu ターミナル）で以下を実行します。

### 4.1 必要なパッケージのインストール

```bash
sudo apt-get update
sudo apt-get install -y curl clang
```

- **curl**: ponyup のインストールに必要
- **clang**: Pony のコンパイルに必要な C コンパイラ

### 4.2 ponyup のインストール

```bash
sh -c "$(curl --proto '=https' --tlsv1.2 -sSf https://raw.githubusercontent.com/ponylang/ponyup/latest-release/ponyup-init.sh)"
```

インストール完了後、パスを通します：

```bash
source ~/.bashrc
```

確認：

```bash
ponyup version
```

```text
ponyup X.Y.Z-... [release]
```

### 4.3 ponyc のインストール

```bash
ponyup update ponyc release
```

確認：

```bash
ponyc --version
```

```text
0.60.6-xxxxxxx [release]
```

### 4.4 インストール確認（まとめ）

3つのツールすべてが利用可能か確認します：

```bash
ponyup version && ponyc --version && clang --version
```

すべてエラーなく出力されれば完了です。

---

## ステップ 5: プロジェクトの作成と動作確認

### 5.1 プロジェクト用ディレクトリの作成

> ⚠️ **注意**: プロジェクトは **WSL2 の Linux ファイルシステム内**（`~/` 配下）に作成してください。`/mnt/c/` 配下（Windows のファイルシステム）ではファイル I/O のパフォーマンスが大幅に低下します。

```bash
mkdir -p ~/projects/helloworld
cd ~/projects/helloworld
```

### 5.2 VS Code でプロジェクトを開く

```bash
code .
```

VS Code が WSL2 に接続した状態で、`helloworld` ディレクトリが開きます。

### 5.3 ソースファイルの作成

VS Code のエクスプローラ（サイドバー）で新しいファイルを作成します：

1. エクスプローラの「新しいファイル」アイコンをクリック
2. ファイル名に `main.pony` と入力
3. 以下のコードを記述して保存（`Ctrl + S`）：

```pony
actor Main
  new create(env: Env) =>
    env.out.print("Hello, world!")
```

Pony Language Colorizer 拡張機能がインストールされていれば、`actor`、`new`、`env` などのキーワードが色分けされて表示されます。

### 5.4 コンパイルと実行

VS Code の統合ターミナル（`` Ctrl + ` ``）で以下を実行します：

```bash
ponyc
```

```text
Building .
Building builtin
Generating
Optimising
Writing ./helloworld.o
Linking ./helloworld
```

実行：

```bash
./helloworld
```

```text
Hello, world!
```

---

## ステップ 6: VS Code の便利な使い方

### 6.1 統合ターミナルの活用

VS Code の統合ターミナル（`` Ctrl + ` ``）を使えば、エディタとターミナルを画面を切り替えることなく同時に使えます。コードを編集 → ターミナルでコンパイル → 実行、という流れを1つのウィンドウ内で完結できます。

**ターミナルの分割**: `Ctrl + Shift + 5` でターミナルを横に分割できます。片方でコンパイル、もう片方でプログラムの実行、といった使い分けが可能です。

### 6.2 ビルドタスクの設定（任意）

頻繁にコンパイルする場合、VS Code のタスク機能を使うと `Ctrl + Shift + B` でビルドを実行できます。

`.vscode/tasks.json` を作成します：

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Pony Build",
      "type": "shell",
      "command": "ponyc",
      "group": {
        "kind": "build",
        "isDefault": true
      },
      "problemMatcher": []
    },
    {
      "label": "Pony Build & Run",
      "type": "shell",
      "command": "ponyc && ./${workspaceFolderBasename}",
      "group": "build",
      "problemMatcher": []
    }
  ]
}
```

これにより：
- `Ctrl + Shift + B` → 「Pony Build」でコンパイル
- タスク一覧から「Pony Build & Run」でコンパイル＋実行

### 6.3 デバッグ設定（任意）

GDB を使って Pony プログラムをデバッグする場合、`.vscode/launch.json` を作成します：

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug Pony Program",
      "type": "cppdbg",
      "request": "launch",
      "program": "${workspaceFolder}/${workspaceFolderBasename}",
      "args": [],
      "stopAtEntry": false,
      "cwd": "${workspaceFolder}",
      "environment": [],
      "externalConsole": false,
      "MIMode": "gdb",
      "preLaunchTask": "Pony Build",
      "setupCommands": [
        {
          "description": "Enable pretty-printing for gdb",
          "text": "-enable-pretty-printing",
          "ignoreFailures": true
        }
      ]
    }
  ]
}
```

> ⚠️ **注意**: デバッグには C/C++ 拡張機能（`ms-vscode.cpptools`）と GDB のインストールが必要です：
> ```bash
> sudo apt-get install -y gdb
> ```

---

## よくある問題と解決策

### Q: `ponyc` コマンドが見つからない

**原因**: ponyup のパスが通っていない可能性があります。

**解決策**:
```bash
source ~/.bashrc
# パスを確認
echo $PATH | grep ponyup
```

パスに `$HOME/.local/share/ponyup/bin` が含まれていない場合は、`.bashrc` の末尾に以下を追加してください：

```bash
export PATH="$HOME/.local/share/ponyup/bin:$PATH"
```

### Q: `code .` コマンドが動かない

**原因**: Windows 側の VS Code のパスが WSL2 から見えていない可能性があります。

**解決策**: Windows 側で VS Code を起動し、コマンドパレット（`Ctrl + Shift + P`）から「Shell Command: Install 'code' command in PATH」を実行してください。

### Q: コンパイルが遅い（/mnt/c/ 配下のプロジェクト）

**原因**: `/mnt/c/`（Windows ファイルシステム）上のファイルへのアクセスは WSL2 から非常に低速です。

**解決策**: プロジェクトを `~/projects/` など、Linux ファイルシステム内に移動してください。

```bash
# Windows 側から Linux ファイルシステムへコピー
cp -r /mnt/c/Users/yourname/projects/myproject ~/projects/
```

### Q: ponyup がプラットフォームを検出できない

**解決策**: 手動でプラットフォームを指定します：

```bash
# Ubuntu 24.04 の場合
ponyup default x86_64-linux-ubuntu24.04

# Ubuntu 22.04 の場合
ponyup default x86_64-linux-ubuntu22.04
```

---

## まとめ

この章で構築した環境の全体像：

```
┌─────────────────────────────────────────────────┐
│  Windows                                         │
│  ┌─────────────────────────────────────────────┐ │
│  │  VS Code (Windows)                          │ │
│  │  ├── WSL 拡張機能 (remote-wsl)              │ │
│  │  ├── Pony Language Colorizer (npruehs.pony) │ │
│  │  └── 統合ターミナル → WSL2 シェル           │ │
│  └──────────────┬──────────────────────────────┘ │
│                 │ WSL2 接続                       │
│  ┌──────────────▼──────────────────────────────┐ │
│  │  WSL2 (Ubuntu)                              │ │
│  │  ├── ponyup (ツールチェーンマネージャー)     │ │
│  │  ├── ponyc 0.60.6 (コンパイラ)              │ │
│  │  ├── clang (C コンパイラ)                    │ │
│  │  └── ~/projects/ (プロジェクト置き場)        │ │
│  └─────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────┘
```

| 完了したこと | 詳細 |
|-------------|------|
| WSL2 のインストール | Ubuntu ディストリビューション |
| VS Code のセットアップ | WSL 拡張機能 + Pony シンタックスハイライト |
| Pony ツールチェーン | ponyup → ponyc 0.60.6 → clang |
| Hello World の動作確認 | コンパイル・実行成功 |
| VS Code タスク・デバッグ設定 | ビルドタスク + GDB デバッグ設定（任意） |

次の章 [Ch.01: Types](01-types.md) から、Pony 言語の学習を本格的に始めましょう。
