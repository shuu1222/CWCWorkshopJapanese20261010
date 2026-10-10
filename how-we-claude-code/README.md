<!-- Copyright 2026 Anthropic PBC -->
<!-- SPDX-License-Identifier: Apache-2.0 -->

# How We Claude Code

> **ワークショップ用サンプル。** メンテナンスは行われず、コントリビューションも受け付けていません。

AI支援によるプロダクト開発ワークフローを3つのフェーズで辿るウォークスルー。
自由度の高いアイデアから始まり、書き起こされた仕様、複数のデザイン探索を経て、
各コンポーネントを実行時に検証できる React アプリへとたどり着きます。

フェーズ1と2は**プロンプトのみ**（各フォルダの `PROMPT.MD`）で提供されています。
自分で実行して、仕様とデザイン探索を再現してください。フェーズ3は完成済みの成果物です。

## フェーズ

| フェーズ | 内容 |
|---|---|
| [`phase-1-exploration/`](phase-1-exploration) | インタビュー形式のブレインストーミング。アカウント不要・リンクベースの割り勘アプリのプロダクト仕様を作り上げます。 |
| [`phase-2-planning/`](phase-2-planning) | 仕様に対する4つの異なるビジュアルデザインの方向性。それぞれを静的HTMLのモックアップとして描き出し、横並びで比較します。 |
| [`phase-3-verify/`](phase-3-verify) | **検証可能なコンポーネントアーキテクチャ**を実演する、独立した Vite + React の Todo アプリ。すべてのユニットがフィクスチャ・不変条件・機械可読なDOMコントラクトを宣言し、エージェント（やCI）が実行時に観測できます。 |

## 非エンジニア向けスライド

このワークショップの流れを、IT業界以外の一般の方向けに「旅行の計画」にたとえて解説したスライドです（各15枚・白背景）。

| スライド | 内容 |
|---|---|
| [参加者用](https://claude.ai/artifact/MRE7PZ4hUQwRUmBc3jxKYh) | 発表で映すスライド。3つのステップで実演の目印つき。最後の2枚は家でのハンズオン手順 |
| [発表者用](https://claude.ai/artifact/8JRWdvy7s5VuHaopF56WNj) | 参加者用の縮小版に、話すこと・補足・想定質問・実演手順を添えたもの |

## フェーズ3の実行方法

```sh
cd phase-3-verify
bun install
bun run dev          # アプリは http://localhost:5199、ダッシュボードは /verify
bun run verify       # 検証マトリクスをヘッドレスで実行
bun run typecheck
```

設計の詳しい解説は [`phase-3-verify/README.md`](phase-3-verify/README.md)、
検証システムの仕組みの詳細なツアーは [`phase-3-verify/docs/verification.html`](phase-3-verify/docs/verification.html)
を参照してください。
