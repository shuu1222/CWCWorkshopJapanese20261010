<!-- Copyright 2026 Anthropic PBC -->
<!-- SPDX-License-Identifier: Apache-2.0 -->

# Verifiable React（検証可能な React）

ひとつの考えを中心に据えて作られた、小さな Vite + React の Todo アプリ。その考えとは、
**アプリのあらゆる部分が、実行時にAIエージェントによって簡単に検証できるべきだ**というもの。
人間がコードを読む、あるいはテストスイートをオフラインで走らせるだけでは足りません。

この設計はシンプルな原則に基づいています。検証とは *表層における実行時の観測* である、
つまり実際に動かし、操作し、本当に表示されている内容を読み取るということです。
テストや型チェックはCIの仕事であり、検証器（verifier）の仕事は本物の成果物が
正しく振る舞うことを確認することです。このアプリはその哲学をアーキテクチャに組み込んでいます。

```
bun install
bun run dev          # アプリ: http://localhost:5199
                     # ダッシュボード: http://localhost:5199/verify
bun run verify       # 検証マトリクス全体をヘッドレスで実行（vitest + jsdom）
bun run typecheck
```

## 6つのアイデア

### 1. DOM こそが機械可読な表層である

すべてのコンポーネントが、自身の状態を表す `data-verify-*` 属性を出力します。

```html
<section data-verify-unit="TodoApp" data-verify-total="3"
         data-verify-done="1" data-verify-active="2" data-verify-filter="all">
```

検証器とエージェントが読むのは React の内部実装ではなく、**DOMコントラクト**です。
これにより、リファクタリングをまたいでも表層が安定します。コントラクトが守られている限り、
内部実装は自由に書き換えられます。`src/verify/core/contract.ts` と `verifyAttrs()` ヘルパーを参照。

### 2. 検証可能なユニットはフィクスチャと不変条件を宣言する

各コンポーネント／機能は **VerifiableUnit** を登録します（`src/verify/specs/`）。

- props に対する **Zod スキーマ**
- **フィクスチャ** — 名前付きで再現可能なレンダリング構成（操作を駆動するための
  命令的な `act()` ステップを任意で持てる）
- **不変条件** — マウントされたDOM上で必ず成り立つべき述語

`probe: true` が付いたフィクスチャは、敵対的なエッジケースです。probe フィクスチャが
ゼロのユニットは、ハッピーパスを再生しただけにすぎません。

### 3. 隔離されたレンダリング先: `/verify/:unit/:fixture`

すべてのユニット × フィクスチャの組み合わせに、ディープリンク可能なルートが割り当てられます。
そこでは *そのユニットだけ* が既知の状態でマウントされ、アプリのシェルは一切ありません。
`?chrome=0` を付けるとスクリーンショット向けにクリーンな表示になります。
エージェント（やPlaywright）はここへ遷移し、観測し、結果を読み取ります。

### 4. 差し替え可能な検証器（Pluggable verifiers）

検証器（`src/verify/verifiers/`）はコンポーネントから独立しています。
それぞれがマウント済みのユニットを調べ、構造化された `Check` を返します。

| 検証器 | チェック内容 |
|---|---|
| `schema` | props が Zod スキーマに一致しているか |
| `invariants` | ユニットが宣言した述語が成り立っているか |
| `dom-contract` | `data-verify-*` 属性が存在し、自己識別できているか |
| `a11y` | ボタンに名前があるか、入力にラベルがあるか、画像に alt があるか |

新しい種類の検証（ビジュアル差分、パフォーマンス予算、i18n など）を追加する手順は、
ファイルを1つ作って登録する、以上です。コンポーネント側の変更は不要です。

### 5. `window.__verify` — エージェント用ハンドル

機械が利用するための、構造化されたバージョン付きAPI。

```js
__verify.manifest()       // すべてのユニット × フィクスチャ × 検証器
__verify.current()        // 現在マウントされているものの構造化された結果
await __verify.runAll()   // マトリクス全体を実行し、結果を返す
```

`/verify` のダッシュボードは、同じデータを人間向けに描画しただけのものです。
エージェントと人間が、同一の真実を見ます。

### 6. ひとつの判定分類（taxonomy）を、3種類の利用者が共有する

判定は `PASS | FAIL | BLOCKED | SKIP`、チェックは `ok ✅ | fail ❌ | warn ⚠️ | probe 🔍`。
同じ `runFixture()` のコードパスが、次の3者から呼び出されます。

- **ダッシュボード**（「Run all」ボタン → 判定グリッド）
- **エージェント**（`window.__verify.runAll()`）
- **CI**（`bun run verify` → vitest マトリクス）

`BLOCKED`（観測できなかった）は、`FAIL`（観測した結果が誤っていた）とは意図的に区別しています。
迷ったときはランナーは失敗側に倒します。誤った PASS はバグを出荷してしまいますが、
誤った FAIL のコストはもう一度確認する手間だけです。

## ファイル構成

```
src/
  features/todos/          アプリ本体（data-verify-* コントラクトを出力する）
  verify/
    core/
      types.ts             VerifiableUnit, Fixture, Invariant, Verifier, Verdict
      contract.ts          data-verify-* 用のヘルパー
      registry.ts          ユニットと検証器の中央レジストリ
      runner.ts            マウント → 操作 → 検証 → 判定
    verifiers/             差し替え可能: schema, invariants, dom-contract, a11y
    specs/                 1ユニットにつき1つの .verify.ts（中央の /verify ディレクトリ）
    harness/
      handle.ts            window.__verify
      Dashboard.tsx        /verify
      UnitPage.tsx         /verify/:unit/:fixture
      Report.tsx           構造化された結果の描画
    matrix.test.ts         CIのパス: 全ユニット×フィクスチャを実行し、判定をアサートする
```

## エージェントで試してみる

1. `bun run dev`
2. AIエージェント（またはPlaywright）にこう伝えます。「`/verify` を開いて
   `window.__verify.manifest()` を実行し、ユニットを1つ選んでそのルートへ遷移し、
   `window.__verify.current().verdict === 'PASS'` であることを確認して」
3. 何かを壊してみます。例えば `TodoApp.tsx` から `data-verify-total` を削除すると、
   `dom-contract` と `counts-add-up` のチェックが的確な診断つきで失敗する様子が、
   ダッシュボードでも、`bun run verify` でも、`window.__verify` でも観測できます。

## 意図的に実演していること

- `TodoStats/inconsistent-counts` は **失敗するように設計された probe** です。
  このフレームワークが真実を確認するだけでなく、嘘を見つけ出せることを証明しています。
- `todos.feature/whitespace-submit` は実物のフォームを空白文字で操作し、
  件数が変わっていないことをアサートします。機能の表層における振る舞いのprobeです。
- すべてのユニットは（`matrix.test.ts` によって）少なくとも1つの probe フィクスチャを
  持つことが必須です。ハッピーパスしかテストしていないユニットは出荷できません。
