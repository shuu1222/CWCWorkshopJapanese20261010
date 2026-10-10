# CWC Workshops 日本語版

[anthropics/cwc-workshops](https://github.com/anthropics/cwc-workshops) のワークショップ教材を日本語化したものです。
原典は Apache-2.0 ライセンス（[LICENSE](LICENSE)）。翻訳したワークショップについては、日本語化したのは**人間が読むドキュメント**（README / PROMPT / 解説HTML）のみで、
ソースコードとコード内コメントは原文の英語のままです。
「日本語版オリジナル」と記したワークショップは原典の翻訳ではなく、この日本語版で独自に作成した教材です。

## 収録ワークショップ

| ワークショップ | 状態 | 内容 |
|---|---|---|
| [`how-we-claude-code/`](how-we-claude-code) | 翻訳済み | AI支援によるプロダクト開発ワークフローを3フェーズで辿る。アイデア → 仕様 → デザイン探索 → 実行時に検証可能な React アプリ。 |
| [`eval-driven-development-no-code/`](eval-driven-development-no-code) | 日本語版オリジナル | コードを書かずに始める評価駆動開発ワークショップ。題材は「断りの返信」。claude.ai とスプレッドシートだけで「テストケース → 採点表 → ベースライン → 改善ループ」を体験する。原典 `eval-driven-agent-development` の考え方を下敷きにした独自教材。 |

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
├── eval-driven-development-no-code/            日本語版オリジナル（コード不要）
│   ├── README.md
│   ├── step-1-criteria/                        指示 v1・合格基準づくり
│   ├── step-2-testcases/testcases.csv          断りたい場面10件
│   ├── step-3-grading/                         採点表・採点シート
│   ├── step-4-improve/                         改善ループ（solutions/ に改善例）
│   └── docs/guide.html                         評価駆動開発の図解ガイド
└── （次のワークショップはここに追加）
```

## 原典との対応

- 上流: `anthropics/cwc-workshops`（`how-we-claude-code` サブディレクトリ）
- 翻訳時点: 2026-10-10
- 翻訳の範囲: `README.md`、各 `PROMPT.MD`、`phase-3-verify/README.md`、`phase-3-verify/docs/verification.html`
- `data-verify-*`、`PASS / FAIL / BLOCKED / SKIP`、`runFixture()` などの識別子は原文を維持

> 原典は「ワークショップ用サンプル。メンテナンスは行われず、コントリビューションも受け付けていません」と明記されています。
