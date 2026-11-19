# legacy-to-ai-ready

[English](README.md) | [日本語](README.ja.md)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Agent Skills](https://img.shields.io/badge/Agent_Skills-compatible-555555)](https://agentskills.io/specification)
[![Claude Code](https://img.shields.io/badge/Claude_Code-compatible-d97757)](https://code.claude.com/docs/en/skills)
[![Cursor](https://img.shields.io/badge/Cursor-compatible-000000)](https://cursor.com/docs/skills)
[![Skill language](https://img.shields.io/badge/skill_language-Japanese-red)](skills/legacy-to-ai-ready/SKILL.md)

文脈が失われたレガシープロジェクトを、段階を踏んで、「人が差分を読むこと」ではなく、構造・検証・成果物で安全性を担保し、AI 駆動開発できる状態まで進めるための [Agent Skill](https://agentskills.io/specification) です。

インストールするスキルは [`skills/legacy-to-ai-ready/SKILL.md`](skills/legacy-to-ai-ready/SKILL.md) です。スキル本文は日本語です。英語の入口は [README.md](README.md) と [`docs/`](docs/) です。公開先の GitHub リポジトリは [`tkc310/legacy-to-add`](https://github.com/tkc310/legacy-to-add) です。

## できること、できないこと

このリポジトリが配布するのは、エージェント向けの手順と、文書、方針書のひな型です。エージェントは、その手順を *対象* プロジェクト（レガシーのコードベース）に適用します。

コード生成器でも、linter でも、ホスティングされたアプリでもありません。レガシーシステムを一度に書き直すものでもありません。人の判断がなくなる、とも書いていません。何が正しいかの決定、方針書の承認、挙動を変える変更の取り込み、取り消せない操作の承認は、人に残ります。スキルが反対しているのは、「誰かが差分を読む」ことを安全性の保証にすることです。

## 構成

```text
LICENSE
README.md                          # 英語版 README（正本）
README.ja.md                       # このファイル
skills/legacy-to-ai-ready/SKILL.md # スキル本文（日本語。エージェントが読み込む成果物）
docs/installation.md
docs/usage.md
docs/stages.md
docs/faq.md
docs/ja/                           # 上記ドキュメントの日本語版
templates/policy-document.md       # 方針書のひな型（日本語。スキル本文と一致）
templates/policy-document.en.md    # 方針書の英語訳
```

## クイックスタート

スキルのフォルダを、使うツールのスキル用ディレクトリへコピーしてください。リポジトリ全体はコピーしないでください。

**Claude Code**（個人向け。このマシンのローカルなプロジェクトすべて）:

```bash
git clone --depth 1 https://github.com/tkc310/legacy-to-add.git /tmp/legacy-to-add
mkdir -p ~/.claude/skills
cp -r /tmp/legacy-to-add/skills/legacy-to-ai-ready ~/.claude/skills/legacy-to-ai-ready
```

**Cursor**（プロジェクト向け。チームと共有できる）:

```bash
git clone --depth 1 https://github.com/tkc310/legacy-to-add.git /tmp/legacy-to-add
mkdir -p .cursor/skills
cp -r /tmp/legacy-to-add/skills/legacy-to-ai-ready .cursor/skills/legacy-to-ai-ready
```

そのあと、新しいセッションを開始し、`/legacy-to-ai-ready` で呼び出すか、スキルの説明に合う作業を依頼してください。プロジェクト向けの Claude Code、ユーザー向けの Cursor、ほかの Agent Skills 対応ツール、読み込みの確認方法は [インストール](docs/ja/installation.md) にあります。

## 使い方

エージェントがコードや資料を外部の AI サービスへ送ったり、変更を始めたりする前に、次の3点を確認してください（スキルが求めています）。

1. 契約上、その資料を外部の AI サービスへ送ってよいか。了承がなければ、許可された範囲に縮小する。
2. エージェントが書き込んでよいブランチと環境。本番環境と本番データには、明示の許可なく触らない。
3. 判断を担う人を、役割名で確認する。仕様、挙動を変える変更の取り込み、リリース、ガードレールの変更。

開始時のプロンプトの例:

> 文脈が失われたレガシープロジェクトを、安全に AI 駆動開発できる状態まで進めたいです。`legacy-to-ai-ready` スキルに従って、段階0の現状診断と方針書の初版から始めてください。方針書が承認されるまで、振る舞いは変えないでください。

エージェントは、[`templates/policy-document.md`](templates/policy-document.md)（または英語訳の [`templates/policy-document.en.md`](templates/policy-document.en.md)）から方針書を作り、段階が進むたびに更新し、人の判断が必須の作業で止まり、完了の報告には証拠（実行したコマンドと出力、または記録の場所）を付けます。

ほかのプロンプト例、エージェントに求める動き、人に残る判断、対象プロジェクトでの文書の置き場所の例は、[使い方](docs/ja/usage.md) を見てください。

## 段階

作業は6つの段階で進みます。厳密な直列ではありません。規模が大きい場合は、領域ごとに段階1〜2を回し、対象の領域に行き渡ってから段階3に進みます。

| 段階 | 目的 | 主な成果物 |
|---|---|---|
| 0. 現状診断 | どこに力を使うかを決め、方針書の初版を書く | 診断の記録（コマンドと出力を含む）、方針書の初版 |
| 1. 仕様の復元 | 用語と決定を正本に残す | 体制の一覧、用語集、ADR、質問票と回答、保留と未決事項 |
| 2. 振る舞いの固定と修正 | 現状を固定し、「仕様どおりかバグか」を判定して直す | 特性テストと仕様のテスト、判定の台帳、修正、あとの検査の証明に使うバグの事例 |
| 3. ガードレールの整備 | 設計・型・lint・テストに落とし、検証スキルを置く | フォーマッタ、linter、型の設定、テスト、検証スキル、形式検証（行う場合）、エージェント向けルール、ルールと強制手段の対応表、CI |
| 4. 完了条件の確認と運用 | 安全に回せることを確認し、運用の流れを決める | 具体化した完了条件、AI 作業の流れ、失敗と訂正の記録場所 |
| 5. 改善ループ | 繰り返す誤りを構造に落とし、ガードレールとハーネスを調整する | 失敗の種類の一覧、自動で変えてよい範囲の合意、検査の証明、指標 |

完了条件、次の段階に進む判定、段階をまたぐ原則は [段階](docs/ja/stages.md) を見てください。

## よくある質問

- なぜ `SKILL.md` は日本語なのか
- リポジトリ名は `legacy-to-add` だが、それがスキル名か
- このスキルはレガシーシステムを書き直すのか
- 人のレビューはもう不要なのか
- pstack や Lauren Tan（`@poteto`）氏のスキルは必要か

答えは [よくある質問](docs/ja/faq.md) にあります。

## 出典

このスキルの「人のレビューに頼らない」構成は、Lauren Tan（[@poteto](https://x.com/poteto)）氏の公開投稿、pstack の公式ガイド、pstack に同梱されたスキルを参考にしています。資料の取得日は 2026-11-19 です。リンクつきの出典は、スキル本文の「出典」にあります。段階の構成、人の判断の線引き、レガシープロジェクトへの当てはめは、このスキル独自のものであり、出典の主張ではありません。

同氏の長文記事（Loops You Can Trust、The Complete Guide to pstack の Pt. 1 と Pt. 2）は、スキルを書いた時点で原文の全文を取得していないため、スキル本文では引用していません。

## ライセンス

[MIT](LICENSE)。Copyright (c) 2026 Takashi Sato / tkc310.
