---
name: setup-matt-pocock-skills
description: AGENTS.md/CLAUDE.md に `## Agent skills` ブロックを、そして `docs/agents/` をセットアップし、engineering 系スキルがこのリポジトリの Issue トラッカー（GitHub またはローカル markdown）、トリアージラベルの語彙、ドメインドキュメントの構成を把握できるようにする。`to-issues`、`to-prd`、`triage`、`diagnose`、`tdd`、`improve-codebase-architecture`、`zoom-out` を初めて使う前に実行すること。あるいはこれらのスキルが Issue トラッカー・トリアージラベル・ドメインドキュメントのコンテキストを欠いているように見える場合にも実行する。
disable-model-invocation: true
---

# Setup Matt Pocock's Skills

engineering 系スキルが前提とする、リポジトリごとの設定の初期構成を構築する：

- **Issue トラッカー** — Issue が存在する場所（デフォルトは GitHub。ローカル markdown も標準でサポートする）
- **トリアージラベル** — 標準の 5 つのトリアージロールに使う文字列
- **ドメインドキュメント** — `CONTEXT.md` と ADR が存在する場所、およびそれらを読むための利用者向けルール

これは決定論的なスクリプトではなく、プロンプト駆動のスキルである。探索し、見つけた内容を提示し、ユーザーに確認を取ってから書き込んでください。

## 手順

### 1. 探索

現在のリポジトリを見て、作業前の状態を把握してください。存在するものは何でも読み、決めつけないこと：

- `git remote -v` と `.git/config` — これは GitHub リポジトリか？ どのリポジトリか？
- リポジトリルートの `AGENTS.md` と `CLAUDE.md` — どちらかが存在するか？ どちらかにすでに `## Agent skills` セクションがあるか？
- リポジトリルートの `CONTEXT.md` と `CONTEXT-MAP.md`
- `docs/adr/` および `src/*/docs/adr/` ディレクトリ
- `docs/agents/` — このスキルが過去に出力したものがすでに存在するか？
- `.scratch/` — ローカル markdown の Issue トラッカー方式がすでに使われている兆候

### 2. 調査結果を提示して質問する

何が存在し、何が欠けているかを要約してください。そのうえで、3 つの決定事項を、ユーザーと一緒に **1 つずつ順に** 進めてください — 1 つのセクションを提示し、ユーザーの回答を得てから、次に進む。3 つを一度にまとめて投げないこと。

ユーザーはこれらの用語の意味を知らない前提で進めてください。各セクションは短い説明（それが何か、なぜこれらのスキルがそれを必要とするか、選択が変わると何が変わるか）から始める。そのあとに選択肢とデフォルトを示す。

**セクション A — Issue トラッカー。**

> 説明：「Issue トラッカー」とは、このリポジトリの Issue が存在する場所のことです。`to-issues`、`triage`、`to-prd`、`qa` といったスキルはそこから読み取り、そこへ書き込みます — `gh issue create` を呼ぶのか、`.scratch/` 配下に markdown ファイルを書くのか、それともあなたが説明する別のワークフローに従うのかを、これらのスキルは知る必要があります。このリポジトリで実際に作業を管理している場所を選んでください。

デフォルトの方針：これらのスキルは GitHub 向けに設計されている。`git remote` が GitHub を指していれば、それを提案する。`git remote` が GitLab（`gitlab.com` またはセルフホスト版のホスト）を指していれば、GitLab を提案する。それ以外の場合（またはユーザーが希望する場合）は、次を提示してください：

- **GitHub** — Issue はリポジトリの GitHub Issues に存在する（`gh` CLI を使う）
- **GitLab** — Issue はリポジトリの GitLab Issues に存在する（[`glab`](https://gitlab.com/gitlab-org/cli) CLI を使う）
- **ローカル markdown** — Issue はこのリポジトリの `.scratch/<feature>/` 配下にファイルとして存在する（ソロプロジェクトやリモートのないリポジトリに適する）
- **その他**（Jira、Linear など）— ワークフローを 1 段落で説明するようユーザーに依頼する。スキルはそれを自由記述の文章として記録する

**セクション B — トリアージラベルの語彙。**

> 説明：`triage` スキルは新規 Issue を処理するとき、それをステートマシンに沿って動かします — 評価が必要、報告者待ち、AFK エージェントが拾える状態、人間が必要、または対応しない、のいずれかです。そのためには、**あなたが実際に設定済みの**文字列に一致するラベル（または Issue トラッカーでの同等物）を付ける必要があります。リポジトリですでに別のラベル名（例：`needs-triage` の代わりに `bug:triage`）を使っている場合は、ここで対応づけてください。そうすればスキルは重複を作らず、正しいラベルを付けられます。

標準の 5 ロール：

- `needs-triage` — メンテナによる評価が必要
- `needs-info` — 報告者待ち
- `ready-for-agent` — 完全に仕様化済みで、AFK エージェントが拾える状態（人間によるコンテキストなしでエージェントが着手できる）
- `ready-for-human` — 人間による実装が必要
- `wontfix` — 対応しない

デフォルト：各ロールの文字列はその名前と同じ。どれかを上書きしたいかユーザーに尋ねること。Issue トラッカーに既存のラベルがなければ、デフォルトのままでよい。

**セクション C — ドメインドキュメント。**

> 説明：一部のスキル（`improve-codebase-architecture`、`diagnose`、`tdd`）は、プロジェクトのドメイン用語を学ぶために `CONTEXT.md` ファイルを読み、過去のアーキテクチャ上の決定を知るために `docs/adr/` を読みます。これらのスキルは、リポジトリにコンテキストが 1 つだけあるのか複数あるのか（例：フロントエンドとバックエンドで別々のコンテキストを持つモノレポ）を知って、正しい場所を見にいく必要があります。

レイアウトを確認してください：

- **シングルコンテキスト** — リポジトリルートに `CONTEXT.md` 1 つ ＋ `docs/adr/`。ほとんどのリポジトリはこれ。
- **マルチコンテキスト** — ルートの `CONTEXT-MAP.md` が、コンテキストごとの `CONTEXT.md` を指す（通常はモノレポ）。

### 3. 確認して編集する

次の内容の草案をユーザーに見せてください：

- `CLAUDE.md` / `AGENTS.md` のうち編集対象となる方に追加する `## Agent skills` ブロック（対象の選び方は手順 4 を参照）
- `docs/agents/issue-tracker.md`、`docs/agents/triage-labels.md`、`docs/agents/domain.md` の内容

書き込む前に、ユーザーが編集できるようにすること。

### 4. 書き込む

**編集するファイルを選ぶ：**

- `CLAUDE.md` が存在すれば、それを編集する。
- なければ `AGENTS.md` が存在する場合は、それを編集する。
- どちらも存在しなければ、どちらを作成するかユーザーに尋ねること — 勝手に選ばないこと。

`CLAUDE.md` がすでに存在するのに `AGENTS.md` を作成してはならない（逆も同様）— 必ずすでに存在する方を編集すること。

選んだファイルにすでに `## Agent skills` ブロックが存在する場合は、重複を末尾に追加するのではなく、その内容をその場で更新すること。周囲のセクションへのユーザーの編集を上書きしないこと。

Agent skills ブロック：

```markdown
## Agent skills

### Issue tracker

[Issue をどこで追跡しているかの 1 行サマリ]。`docs/agents/issue-tracker.md` を参照。

### Triage labels

[ラベル語彙の 1 行サマリ]。`docs/agents/triage-labels.md` を参照。

### Domain docs

[レイアウトの 1 行サマリ — 「シングルコンテキスト」または「マルチコンテキスト」]。`docs/agents/domain.md` を参照。
```

続いて、このスキルフォルダ内のシードテンプレートを出発点として、3 つのドキュメントファイルを書き込んでください：

- [issue-tracker-github.md](./issue-tracker-github.md) — GitHub の Issue トラッカー
- [issue-tracker-gitlab.md](./issue-tracker-gitlab.md) — GitLab の Issue トラッカー
- [issue-tracker-local.md](./issue-tracker-local.md) — ローカル markdown の Issue トラッカー
- [triage-labels.md](./triage-labels.md) — ラベルの対応づけ
- [domain.md](./domain.md) — ドメインドキュメントの利用者向けルールとレイアウト

「その他」の Issue トラッカーの場合は、ユーザーの説明をもとに `docs/agents/issue-tracker.md` を一から書く。

### 5. 完了

セットアップが完了したこと、そしてどの engineering 系スキルがこれらのファイルから読み取るようになるかを、ユーザーに伝えてください。あとから `docs/agents/*.md` を直接編集してよいことも伝える — このスキルの再実行が必要なのは、Issue トラッカーを切り替えたい場合か、一から作り直したい場合だけである。
