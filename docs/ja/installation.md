# インストール

English: [Installation](../installation.md)

このリポジトリは、ドキュメントと Agent Skill 1件で構成されています。エージェントが読み込むファイルは [`skills/legacy-to-add/SKILL.md`](../../skills/legacy-to-add/SKILL.md) です。インストールするには、リポジトリ全体ではなく、このスキルのフォルダを、使うツールのスキル用ディレクトリへコピーします。

## コピーする対象

```text
skills/legacy-to-add/
└── SKILL.md
```

フォルダ名は `legacy-to-add` のままにしてください。スキルの frontmatter の `name` と、多くのツールが用意するコマンド（例: `/legacy-to-add`）が、この名前だからです。

## リポジトリの取得

このページのコマンドはすべて、`/tmp/legacy-to-add` に置いたクローンからコピーします。

```bash
git clone --depth 1 https://github.com/tkc310/legacy-to-add.git /tmp/legacy-to-add
```

このディレクトリがすでにあり、空でない場合、`git clone` は失敗します。以前のクローンが残っているときは、先に削除するか、別の場所にクローンして以下のパスを読み替えてください。

以下の `cp` コマンドは、初回のインストール用です。コピー先のフォルダがすでにある場合は、[更新](#更新) のコマンドを使ってください。インストール用のコマンドをもう一度実行すると、既存のフォルダの中にコピーがもう1つ作られます。

## Claude Code

Claude Code は、個人用のディレクトリか、プロジェクトのディレクトリからスキルを読み込みます。公式の説明は [Extend Claude with skills](https://code.claude.com/docs/en/skills) にあります。

### 個人向け（このマシンのすべてのプロジェクト）

```bash
mkdir -p ~/.claude/skills
cp -r /tmp/legacy-to-add/skills/legacy-to-add ~/.claude/skills/legacy-to-add
```

個人向けスキルは、そのマシンのローカルな Claude Code セッションで使えます。Claude Code のクラウドセッションや Cowork では、自動では使えません。

### プロジェクト向け（そのリポジトリだけ。チームと共有できる）

スキルを置きたいプロジェクトのルートで、次を実行します。

```bash
mkdir -p .claude/skills
cp -r /tmp/legacy-to-add/skills/legacy-to-add .claude/skills/legacy-to-add
```

チームのメンバーが pull したときにスキルを受け取れるよう、`.claude/skills/legacy-to-add/` をコミットしてください。プロジェクト向けスキルは、そのリポジトリで開始したセッションで読み込まれます。

### 読み込まれたことの確認

1. プロジェクトで Claude Code の新しいセッションを開始します（新しいスキルを監視しないビルドの場合は、再起動してください）。
2. `/` を入力します。コマンドの一覧に `legacy-to-add` が出るはずです。`/skills` では、説明つきで一覧できます。
3. `/legacy-to-add` で直接呼び出します。

出てこない場合は、次の3点を確認してください。ファイルが `…/skills/legacy-to-add/SKILL.md` にあること。ファイルが `---` で囲んだ YAML frontmatter で始まっていること。frontmatter に `name` と `description` があること。

## Cursor

Cursor は、プロジェクト用とユーザー用のディレクトリからスキルを読み込みます。互換のため、Claude Code や Codex のスキル用ディレクトリも読みます。公式の説明は [Agent Skills](https://cursor.com/docs/skills) にあります。

| 範囲 | ディレクトリ |
|---|---|
| プロジェクト（リポジトリを pull した人すべて） | `.cursor/skills/`、`.agents/skills/` |
| ユーザー（このマシン） | `~/.cursor/skills/`、`~/.agents/skills/` |
| 互換のために追加で読む場所 | `.claude/skills/`、`~/.claude/skills/`、および Codex の対応する場所 |

### プロジェクト向け

スキルを置きたいプロジェクトのルートで、次を実行します。

```bash
mkdir -p .cursor/skills
cp -r /tmp/legacy-to-add/skills/legacy-to-add .cursor/skills/legacy-to-add
```

チームにもスキルを渡す場合は、コピーしたフォルダをコミットしてください。

### ユーザー向け

```bash
mkdir -p ~/.cursor/skills
cp -r /tmp/legacy-to-add/skills/legacy-to-add ~/.cursor/skills/legacy-to-add
```

`~/.cursor/skills/` のユーザースキルは、Cloud Agents 向けに同期するか、チームへ公開するまでは、そのマシンだけにあります。同期していないローカルのユーザースキルは、Cloud Agents へ自動ではコピーされません。

### 読み込まれたことの確認

サイドバーの **Customize** を開き、**Skills** を見てください。Agent のチャットで `/legacy-to-add` と入力しても呼び出せます。

### プラグインとしては配布していません

Cursor が GitHub のリポジトリからスキルを入れる公式の経路は、`.cursor-plugin/marketplace.json` を含むプラグインです。このリポジトリにはそのファイルがないので、上のとおりフォルダをコピーしてください。

## Agent Skills を実装しているほかのツール

このスキルは、一般的な配置に従っています。スキル名のディレクトリの中に、YAML frontmatter（`name` と `description`）と Markdown の本文を持つ `SKILL.md` を置く形です。[Agent Skills](https://agentskills.io/specification) の配置を実装しているツールであれば、そのツールのスキル用ディレクトリへ `legacy-to-add/SKILL.md` を置けば読み込めます。正確なパスは、そのツールの文書で確認してください。

## 更新

インストール済みのコピーを、このリポジトリの新しい版で置き換えるには、クローンし直してから（[リポジトリの取得](#リポジトリの取得) を参照）、フォルダの中身をインストール先へ上書きコピーします。

```bash
cp -r /tmp/legacy-to-add/skills/legacy-to-add/. /path/to/skills/legacy-to-add/
```

`/path/to/skills/legacy-to-add/` は、以前選んだインストール先（`~/.claude/skills/legacy-to-add` や `.cursor/skills/legacy-to-add` など）に置き換えてください。そのあと、新しいセッションを始めるか、実行中のセッションが変更を読み込んだことを確認してください。

インストール済みのコピーは、このリポジトリの変更に自動では追従しません。コピーだけを直しても、このリポジトリには反映されません。進め方そのものを変える場合は、このリポジトリを直すか、対象プロジェクトの方針書に違いを書いてください。
