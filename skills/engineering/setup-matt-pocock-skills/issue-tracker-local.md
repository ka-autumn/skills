# Issue トラッカー：ローカル markdown

このリポジトリの Issue と PRD は `.scratch/` 内の markdown ファイルとして存在する。

## 規約

- 1 つの機能につき 1 ディレクトリ：`.scratch/<feature-slug>/`
- PRD は `.scratch/<feature-slug>/PRD.md`
- 実装 Issue は `.scratch/<feature-slug>/issues/<NN>-<slug>.md`。`01` から連番を振る
- トリアージのステータスは、各 Issue ファイル先頭付近の `Status:` 行に記録する（ロール文字列は `triage-labels.md` を参照）
- コメントと会話履歴は、ファイル末尾の `## Comments` 見出しの下に追記する

## スキルが「Issue トラッカーに公開する」と言ったとき

`.scratch/<feature-slug>/` の下に新しいファイルを作成する（必要ならディレクトリも作成する）。

## スキルが「対象のチケットを取得する」と言ったとき

参照されたパスのファイルを読む。通常はユーザーがパスまたは Issue 番号を直接渡す。
