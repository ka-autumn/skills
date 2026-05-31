# Issue トラッカー：GitLab

このリポジトリの Issue と PRD は GitLab の Issue として存在する。すべての操作で [`glab`](https://gitlab.com/gitlab-org/cli) CLI を使うこと。

## 規約

- **Issue を作成する**：`glab issue create --title "..." --description "..."`。複数行の説明には ヒアドキュメント を使う。エディタを開くには `--description -` を渡す。
- **Issue を参照する**：`glab issue view <number> --comments`。機械可読な出力には `-F json` を使う。
- **Issue の一覧を表示する**：`glab issue list -F json`。適切な `--label` フィルタを付ける。
- **Issue にコメントする**：`glab issue note <number> --message "..."`。GitLab はコメントを "notes" と呼ぶ。
- **Issue にラベルを付与／削除する**：`glab issue update <number> --label "..."` / `--unlabel "..."`。複数のラベルはカンマ区切り、またはフラグの繰り返しで指定できる。
- **Issue をクローズする**：`glab issue close <number>`。`glab issue close` はクローズ時のコメントを受け付けないため、先に `glab issue note <number> --message "..."` で説明を投稿してからクローズする。
- **マージリクエスト**：GitLab は PR を "merge requests" と呼ぶ。`glab mr create`、`glab mr view`、`glab mr note` などを使う — `gh pr ...` と同じ形で、`pr` の代わりに `mr`、`comment`/`--body` の代わりに `note`/`--message` を使う。

リポジトリは `git remote -v` から推測する — clone 内で実行すれば `glab` が自動で行う。

## スキルが「Issue トラッカーに公開する」と言ったとき

GitLab の Issue を作成する。

## スキルが「対象のチケットを取得する」と言ったとき

`glab issue view <number> --comments` を実行する。
