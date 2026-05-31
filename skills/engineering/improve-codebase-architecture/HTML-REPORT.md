# HTML レポートの形式（HTML Report Format）

アーキテクチャレビューは、OS の一時ディレクトリに置かれる単一の自己完結した HTML ファイルとして描画される。Tailwind と Mermaid はどちらも CDN から読み込む。Mermaid はグラフ状の図を確実に扱える。手作りの div とインライン SVG は、より編集的なビジュアル（質量図、断面図）を扱う。両者を混在させること——すべてを Mermaid に頼らないこと。そうしないと、ありきたりに見えはじめる。

## 骨組み（Scaffold）

```html
<!doctype html>
<html lang="ja">
  <head>
    <meta charset="utf-8" />
    <title>アーキテクチャレビュー — {{repo name}}</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script type="module">
      import mermaid from "https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs";
      mermaid.initialize({ startOnLoad: true, theme: "neutral", securityLevel: "loose" });
    </script>
    <style>
      /* small custom layer for things Tailwind doesn't cover cleanly:
         dashed seam lines, hand-drawn-feeling arrow heads, etc. */
      .seam { stroke-dasharray: 4 4; }
      .leak { stroke: #dc2626; }
      .deep { background: linear-gradient(135deg, #0f172a, #1e293b); }
    </style>
  </head>
  <body class="bg-stone-50 text-slate-900 font-sans">
    <main class="max-w-5xl mx-auto px-6 py-12 space-y-12">
      <header>...</header>
      <section id="candidates" class="space-y-10">...</section>
      <section id="top-recommendation">...</section>
    </main>
  </body>
</html>
```

## ヘッダー（Header）

リポジトリ名、日付、そしてコンパクトな凡例：実線のボックス＝モジュール、破線＝seam、赤い矢印＝漏れ（leakage）、太い濃色のボックス＝深いモジュール。導入の段落は置かない——そのまま候補に入る。

## 候補カード（Candidate card）

図が中心的な役割を担う。地の文は控えめかつ平易で、用語集（[LANGUAGE.md](LANGUAGE.md)）の用語を、もったいぶらずに使う。

各候補は 1 つの `<article>` とする：

- **タイトル（Title）** — 短く、深化に名前を付ける（例：「Order 受け入れパイプラインを畳む」）。
- **バッジ行（Badge row）** — 提案の強さ（`強く推奨（Strong）`＝emerald、`検討の価値あり（Worth exploring）`＝amber、`仮説段階（Speculative）`＝slate）、加えて依存関係の分類のタグ（`in-process`、`local-substitutable`、`ports & adapters`、`mock`）。
- **ファイル（Files）** — 等幅のリスト、`font-mono text-sm`。
- **ビフォー／アフター図（Before / After diagram）** — 中心となる要素。2 列を横に並べる。下記のパターンを参照。
- **課題（Problem）** — 一文。何が痛みか。
- **解決策（Solution）** — 一文。何が変わるか。
- **成果（Wins）** — 箇条書き、各 6 語以内。例：「テストが 1 つのインターフェースに当たる」「価格ロジックの漏れが止まる」「浅いラッパーを 4 つ削除」。
- **ADR コールアウト（ADR callout）**（該当する場合）— amber 系の色のボックスに一行。

説明の段落は書かない。図を理解するのに段落が必要なら、図を描き直すこと。

## 図のパターン（Diagram patterns）

候補に合うパターンを選ぶこと。それらを混在させること。すべての図を同じ見た目にしないこと——多様性も要点の 1 つである。

### Mermaid グラフ（依存関係／コールフローの主力）

「X が Y を呼び、Y が Z を呼び、ほら、この混乱ぶり」を伝えたいときは、Mermaid の `flowchart` または `graph` を使う。唐突に貼り付けた感じにならないよう、Tailwind でスタイリングしたカードで包む。classDef でスタイルを付け、漏れ（leakage）のエッジを赤く、深いモジュールを濃色にする。シーケンス図は「ビフォー：6 回の往復、アフター：1 回」を示すのに向いている。

```html
<div class="rounded-lg border border-slate-200 bg-white p-4">
  <pre class="mermaid">
    flowchart LR
      A[OrderHandler] --> B[OrderValidator]
      B --> C[OrderRepo]
      C -.leak.-> D[PricingClient]
      classDef leak stroke:#dc2626,stroke-width:2px;
      class C,D leak
  </pre>
</div>
```

### 手作りのボックスと矢印（Mermaid のレイアウトが言うことを聞かないとき）

モジュールを、枠線とラベルを付けた `<div>` で表す。矢印は、relative なコンテナの上に absolute で配置したインライン SVG の `<line>` や `<path>` 要素で表す。「アフター」の図を、内部がグレーアウトされた 1 つの太枠の深いモジュールのように見せたいときに、これを使う——Mermaid では適切な重みで描画できない。

### 断面図（層状の浅さに向く）

呼び出しが通過する層を示すために、水平の帯（`h-12 border-l-4`）を積み重ねる。ビフォー：6 つの薄い層が、それぞれ何もしていない。アフター：統合された責務をラベルに記した、1 つの太い帯。

### 質量図（「インターフェースが実装と同じ幅」に向く）

モジュールごとに 2 つの長方形——1 つはインターフェースの表面積、もう 1 つは実装。ビフォー：インターフェースの長方形が、実装の長方形とほぼ同じ高さ（浅い）。アフター：インターフェースの長方形は低く、実装の長方形は高い（深い）。

### コールグラフの畳み込み

ビフォー：関数呼び出しのツリーを、入れ子のボックスとして描く。アフター：同じツリーを 1 つのボックスに畳み込み、いまや内部となった呼び出しを、その中に薄く表示する。

## スタイルの指針（Style guidance）

- 企業のダッシュボードではなく、編集的に寄せること。余白をたっぷり取る。見出しにはセリフ体も可（`font-serif` は stone/slate とよく合う）。
- 色は控えめに：アクセント 1 色（emerald か indigo）に加えて、漏れには赤、警告には amber。
- ビフォー／アフターがスクロールなしで横に快適に並ぶよう、図は高さ約 320px に保つ。
- 図の中のモジュールのラベルには `text-xs uppercase tracking-wider` を使う——UI ではなく、図式として読めるようにする。
- スクリプトは Tailwind CDN と Mermaid の ESM インポートだけ。それ以外はレポートは静的である——アプリのコードはなく、Mermaid 自身の描画を超えるインタラクティブ性もない。

## 最優先の提案セクション（Top recommendation section）

大きめのカードを 1 つ。候補名、理由を一文、そのカードへのアンカーリンク。それだけ。

## トーン（Tone）

平易な日本語で、簡潔に——ただし、アーキテクチャの名詞と動詞は [LANGUAGE.md](LANGUAGE.md) からそのまま使う。簡潔さは、用語がぶれてよい言い訳にはならない。

**正確に次の語を使う：** モジュール（module）、インターフェース（interface）、実装（implementation）、深さ（depth）、深い（deep）、浅い（shallow）、seam、アダプター（adapter）、レバレッジ（leverage）、局所性（locality）。

**決して次の語で置き換えない：** component、service、unit（module の代わりに）・API、signature（interface の代わりに）・boundary（seam の代わりに）・layer、wrapper（module を意味するときに）。

**このスタイルに合う言い回し：**

- 「Order 受け入れモジュールは浅い——インターフェースが実装とほぼ一致している。」
- 「価格ロジックが seam を越えて漏れている。」
- 「深化する：1 つのインターフェース、テストする場所は 1 つ。」
- 「2 つのアダプターが seam を正当化する：本番では HTTP、テストではインメモリ。」

**成果（Wins）の箇条書き**は、利得を用語集の語で言い表す：*「局所性：バグが 1 つのモジュールに集中する」*、*「レバレッジ：1 つのインターフェース、N 個の呼び出し箇所」*、*「インターフェースが縮み、実装がラッパーを吸収する」*。*「保守しやすくなる」*や*「コードがきれいになる」*とは書かないこと——これらの語は用語集になく、載せる資格がない。

ぼかしも、前置きも、「〜という点は注目に値する」もなし。文が箇条書きにできるなら、箇条書きにすること。箇条書きが削れるなら、削ること。ある語が [LANGUAGE.md](LANGUAGE.md) にないなら、新しい語を考案する前に、用語集にある語に手を伸ばすこと。
