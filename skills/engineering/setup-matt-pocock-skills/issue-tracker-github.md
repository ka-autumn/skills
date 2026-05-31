# Issue トラッカー：GitHub

このリポジトリの Issue と PRD は GitHub の Issue として存在する。すべての操作で `gh` CLI を使うこと。

## 規約

- **Issue を作成する**：`gh issue create --title "..." --body "..."`。複数行の本文には ヒアドキュメント を使う。
- **Issue を参照する**：`gh issue view <number> --comments`。コメントは `jq` で絞り込み、あわせてラベルも取得する。
- **Issue の一覧を表示する**：
  `gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'`
  。適切な `--label` と `--state` のフィルタを付ける。
- **Issue にコメントする**：`gh issue comment <number> --body "..."`
- **Issue にラベルを付与／削除する**：`gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **Issue をクローズする**：`gh issue close <number> --comment "..."`

リポジトリは `git remote -v` から推測する — clone 内で実行すれば `gh` が自動で行う。

## スキルが「Issue トラッカーに公開する」と言ったとき

GitHub の Issue を作成する。

## スキルが「対象のチケットを取得する」と言ったとき

`gh issue view <number> --comments` を実行する。
