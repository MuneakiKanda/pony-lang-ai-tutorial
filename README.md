# Pony Lang AI Tutorial

AI（LLM）を活用して、プログラミング言語 **Pony** の日本語チュートリアルを作成するプロジェクトです。

## 概要

| 項目 | 内容 |
|------|------|
| 対象 Pony バージョン | **0.60.6**（2026-02-06 リリース） |
| チュートリアル言語 | 日本語（技術用語は英語併記） |
| 対象読者 | Python / Java の基礎を知っている程度 |
| 実行環境 | Linux もしくは WSL2 |
| ファイル形式 | Markdown (.md) |

## ディレクトリ構成

```
pony-lang-ai-tutorial/
├── README.md                  ← このファイル（プロジェクト概要）
├── TUTORIAL_GUIDE.md          ← AI向けチュートリアル作成指示書
├── .gitignore
└── tutorial/
    └── 0.60.6/                ← チュートリアル本体
        ├── INDEX.md           ← 全章リンク付き目次
        ├── 00-getting-started.md
        ├── 01-types.md
        ├── 02-expressions.md
        ├── 03-reference-capabilities.md
        ├── 04-object-capabilities.md
        ├── 05-generics.md
        ├── 06-packages.md
        ├── 07-testing.md
        ├── 08-c-ffi.md
        ├── 09-gotchas.md
        ├── 10-where-next.md
        └── 11-appendices.md
```

## 作成フロー

1. `TUTORIAL_GUIDE.md`（作成指示書）に従い、AI に章番号を指定して1章ずつ生成を依頼
2. 生成後にレビュー → フィードバック → 修正
3. 全章完了後に `INDEX.md` を生成
4. 全体の整合性チェック

## ライセンス

TBD
