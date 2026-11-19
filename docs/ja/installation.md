# インストール

このリポジトリは、ドキュメントと Agent Skill 1件で構成されています。エージェントが読み込むファイルは [`skills/legacy-to-ai-ready/SKILL.md`](../../skills/legacy-to-ai-ready/SKILL.md) です。リポジトリ全体ではなく、このフォルダを、使うツールのスキル用ディレクトリへコピーしてください。

English instructions: [Installation](../installation.md).

## コピーする対象

クローンまたはダウンロードしたあと、インストールするフォルダは次のとおりです。

```text
skills/legacy-to-ai-ready/
└── SKILL.md
```

フォルダ名は `legacy-to-ai-ready` のままにしてください。スキルの frontmatter の `name` と、多くのツールが用意するコマンド（例: `/legacy-to-ai-ready`）が、この名前だからです。

## Claude Code

Claude Code は、個人用のディレクトリか、プロジェクトのディレクトリからスキルを読み込みます。公式の説明は [Extend Claude with skills](https://code.claude.com/docs/en/skills) を見てください。

### 個人向け（このマシンのすべてのプロジェクト）

```bash
git clone --depth 1 https://github.com/tkc310/legacy-to-add.git /tmp/legacy-to-add
mkdir -p ~/.claude/skills
cp -r /tmp/legacy-to-add/skills/legacy-to-ai-ready ~/.claude/skills/legacy-to-ai-ready
```

個人向けスキルは、そのマシンのローカルな Claude Code セッションで使えます。Claude Code のクラウドセッションや Cowork には、自動では行きません。

### プロジェクト向け（そのリポジトリだけ。チームと共有できる）

スキルを置きたいプロジェクトのルートで、次を実行します。

```bash
git clone --depth 1 https://github.com/tkc310/legacy-to-add.git /tmp/legacy-to-add
mkdir -p .claude/skills
cp -r /tmp/legacy-to-add/skills/legacy-to-ai-ready .claude/skills/legacy-to-ai-ready
```

チームが pull したときにスキルを受け取れるよう、`.claude/skills/legacy-to-ai-ready/` をコミットしてください。プロジェクト向けスキルは、そのリポジトリで開始したセッションで読み込まれます。

### 読み込まれたことの確認

1. プロジェクトで Claude Code の新しいセッションを開始します（新しいスキルを監視しないビルドの場合は、再起動してください）。
2. `/` を入力します。コマンドの一覧に `legacy-to-ai-ready` が出ます。`/skills` では、説明つきで一覧できます。
3. `/legacy-to-ai-ready` で直接呼び出せます。

出てこない場合は、ファイルが `…/skills/legacy-to-ai-ready/SKILL.md` にあること、ファイルが `---` で囲んだ YAML frontmatter で始まっていること、frontmatter に `name` と `description` があることを確認してください。

## Cursor

Cursor は、プロジェクト用とユーザー用のディレクトリからスキルを読み込みます。互換のため、Claude Code や Codex のスキル用ディレクトリも読みます。公式の説明は [Agent Skills](https://cursor.com/docs/skills) を見てください。

| 範囲 | ディレクトリ |
|---|---|
| プロジェクト（リポジトリを pull した人） | `.cursor/skills/`、`.agents/skills/` |
| ユーザー（このマシン） | `~/.cursor/skills/`、`~/.agents/skills/` |
| 互換のために追加で読む場所 | `.claude/skills/`、`~/.claude/skills/`、および Codex の対応する場所 |

### プロジェクト向け

```bash
git clone --depth 1 https://github.com/tkc310/legacy-to-add.git /tmp/legacy-to-add
mkdir -p .cursor/skills
cp -r /tmp/legacy-to-add/skills/legacy-to-ai-ready .cursor/skills/legacy-to-ai-ready
```

チームにもスキルを渡す場合は、コピーしたフォルダをコミットしてください。

### ユーザー向け

```bash
git clone --depth 1 https://github.com/tkc310/legacy-to-add.git /tmp/legacy-to-add
mkdir -p ~/.cursor/skills
cp -r /tmp/legacy-to-add/skills/legacy-to-ai-ready ~/.cursor/skills/legacy-to-ai-ready
```

`~/.cursor/skills/` のユーザースキルは、Cloud Agents 向けに同期するか、チームへ公開するまでは、そのマシンに留まります。同期していないローカルのユーザースキルは、Cloud Agents へ自動ではコピーされません。

### 読み込まれたことの確認

サイドバーの **Customize** を開き、**Skills** を見てください。Agent のチャットで `/legacy-to-ai-ready` と入力しても呼び出せます。

Cursor が GitHub のリポジトリからスキルを入れる公式の経路は、`.cursor-plugin/marketplace.json` を含むプラグインです。このリポジトリにはそのファイルはないので、上のとおりフォルダをコピーする方法を使ってください。

## Agent Skills を実装しているほかのツール

このスキルは、よく使われる配置に従っています。スキル名のディレクトリの中に、YAML frontmatter（`name` と `description`）と Markdown 本文を持つ `SKILL.md` を置きます。[Agent Skills](https://agentskills.io/specification) の配置を実装しているツールであれば、そのツールのスキル用ディレクトリへ `legacy-to-ai-ready/SKILL.md` を置けば読み込めます。正確なパスは、そのツールの文書で確認してください。

## 更新

インストール済みのコピーを、このリポジトリの新しい版で置き換えるには、次を実行します。

```bash
git clone --depth 1 https://github.com/tkc310/legacy-to-add.git /tmp/legacy-to-add
cp -r /tmp/legacy-to-add/skills/legacy-to-ai-ready/. /path/to/skills/legacy-to-ai-ready/
```

`/path/to/skills/legacy-to-ai-ready/` は、以前選んだインストール先（`~/.claude/skills/legacy-to-ai-ready` や `.cursor/skills/legacy-to-ai-ready` など）に置き換えてください。そのあと、新しいセッションを始めるか、実行中のセッションが変更を拾ったことを確認してください。

プロジェクト内のコピーだけを直して、このリポジトリと自動で一致し続けるとは想定しないでください。進め方自体を変える場合は、こちらを直すか、対象プロジェクトの方針書に差異を書いてください。
