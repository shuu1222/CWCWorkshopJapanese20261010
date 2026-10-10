# CWC Workshops 日本語版

[anthropics/cwc-workshops](https://github.com/anthropics/cwc-workshops) のワークショップ教材を日本語化したものです。
原典は Apache-2.0 ライセンス（[LICENSE](LICENSE)）。日本語化したのは**人間が読むドキュメント**（README / PROMPT / 解説HTML）のみで、
ソースコードとコード内コメントは原文の英語のままです。

## 収録ワークショップ

| ワークショップ | 状態 | 内容 |
|---|---|---|
| [`how-we-claude-code/`](how-we-claude-code) | 翻訳済み | AI支援によるプロダクト開発ワークフローを3フェーズで辿る。アイデア → 仕様 → デザイン探索 → 実行時に検証可能な React アプリ。 |
| `eval-driven-development-for-non-engineers/` | 追加予定 | 非エンジニア向けの評価駆動開発（eval-driven development）ワークショップ。 |

## ディレクトリ構成

ワークショップ1つにつきトップレベルのディレクトリ1つ、という構成です。追加するときは
同じ階層にディレクトリを作り、上の表に1行足してください。

```
.
├── README.md                                   このファイル
├── LICENSE                                     Apache-2.0（原典由来）
├── how-we-claude-code/                         翻訳済みワークショップ
│   ├── README.md
│   ├── phase-1-exploration/PROMPT.MD
│   ├── phase-2-planning/PROMPT.MD
│   └── phase-3-verify/                         Vite + React アプリ（コードは英語）
│       ├── README.md
│       └── docs/verification.html              検証システムの詳細解説
└── （次のワークショップはここに追加）
```

## 原典との対応

- 上流: `anthropics/cwc-workshops`（`how-we-claude-code` サブディレクトリ）
- 翻訳時点: 2026-10-10
- 翻訳の範囲: `README.md`、各 `PROMPT.MD`、`phase-3-verify/README.md`、`phase-3-verify/docs/verification.html`
- `data-verify-*`、`PASS / FAIL / BLOCKED / SKIP`、`runFixture()` などの識別子は原文を維持

> 原典は「ワークショップ用サンプル。メンテナンスは行われず、コントリビューションも受け付けていません」と明記されています。
