# legacy-to-ai-ready

[English](README.md) | [日本語](README.ja.md)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Agent Skills](https://img.shields.io/badge/Agent_Skills-compatible-555555)](https://agentskills.io/specification)
[![Claude Code](https://img.shields.io/badge/Claude_Code-compatible-d97757)](https://code.claude.com/docs/en/skills)
[![Cursor](https://img.shields.io/badge/Cursor-compatible-000000)](https://cursor.com/docs/skills)
[![Skill language](https://img.shields.io/badge/skill_language-Japanese-red)](skills/legacy-to-ai-ready/SKILL.md)

仕様や経緯が失われたレガシープロジェクトを、段階を踏んで、安全に AI 駆動開発できる状態まで進めるための [Agent Skill](https://agentskills.io/specification) です。安全性は、人が差分を読むことではなく、構造・検証・成果物で担保します。

インストールするスキルは [`skills/legacy-to-ai-ready/SKILL.md`](skills/legacy-to-ai-ready/SKILL.md) で、本文は日本語です。日本語の説明は、この README と `docs/ja/` のページ（[インストール](docs/ja/installation.md)、[使い方](docs/ja/usage.md)、[段階](docs/ja/stages.md)、[よくある質問](docs/ja/faq.md)）にあります。英語の入口は [README.md](README.md) です。

スキル名は `legacy-to-ai-ready` です。公開先の GitHub リポジトリは [`tkc310/legacy-to-add`](https://github.com/tkc310/legacy-to-add) です。

## できること、できないこと

このリポジトリが配布するのは、エージェント向けの手順、その説明の文書、方針書のテンプレートです。エージェントは、この手順を対象プロジェクト（レガシーのコードベース）に適用します。

コード生成ツール、linter、ホスト型のアプリのいずれでもありません。レガシーシステムを一度に書き直すものでもありません。人の判断が不要になるとも主張しません。何が正しいかの決定、方針書の承認、挙動を変える変更の取り込み、取り消せない操作の承認は、人に残ります。このスキルがやめるのは、「誰かが差分を読む」ことを安全性の保証にすることです。

## クイックスタート

リポジトリ全体ではなく、スキルのフォルダを、使うツールのスキル用ディレクトリへコピーします。

**Claude Code**（個人向け。このマシンのローカルなプロジェクトすべてで使える）:

```bash
git clone --depth 1 https://github.com/tkc310/legacy-to-add.git /tmp/legacy-to-add
mkdir -p ~/.claude/skills
cp -r /tmp/legacy-to-add/skills/legacy-to-ai-ready ~/.claude/skills/legacy-to-ai-ready
```

**Cursor**（プロジェクト向け。プロジェクトのルートで実行する。チームと共有できる）:

```bash
git clone --depth 1 https://github.com/tkc310/legacy-to-add.git /tmp/legacy-to-add
mkdir -p .cursor/skills
cp -r /tmp/legacy-to-add/skills/legacy-to-ai-ready .cursor/skills/legacy-to-ai-ready
```

コピーしたら、新しいセッションを始めて `/legacy-to-ai-ready` で呼び出すか、スキルの説明に合う作業を依頼します。

ほかの置き場所（プロジェクト向けの Claude Code、ユーザー向けの Cursor、ほかの Agent Skills 対応ツール）、読み込まれたことの確認、インストール済みのコピーの更新は、[インストール](docs/ja/installation.md) に書いてあります。

## 使い方

スキルは、エージェントがコードや資料を外部の AI サービスへ送る前、および変更を始める前に、次の3点の確認を求めます。

1. **契約と機密**: その資料を外部の AI サービスへ送ってよいか。了承がなければ、許可された範囲に縮小します。
2. **書き込みの範囲**: エージェントが書き込んでよいブランチと環境はどこまでか。本番環境と本番データには、明示の許可なく触りません。
3. **判断を担う人**: 仕様、挙動を変える変更の取り込み、リリース、ガードレールの変更を誰が判断するかを、役割名で確認します。

開始時のプロンプトの例:

> 文脈が失われたレガシープロジェクトを、安全に AI 駆動開発できる状態まで進めたいです。`legacy-to-ai-ready` スキルに従って、段階0の現状診断と方針書の初版から始めてください。方針書が承認されるまで、振る舞いは変えないでください。

依頼を受けたエージェントは、次のように動きます。

- [`templates/policy-document.md`](templates/policy-document.md)（英語訳は [`templates/policy-document.en.md`](templates/policy-document.en.md)）をもとに方針書を作り、段階が進むたびに更新する。
- 人の判断が必須の作業で止まる。
- 完了の報告に、証拠（実行したコマンドと出力、または記録の場所）を付ける。

ほかのプロンプトの例、エージェントに求める動きの一覧、人に残る判断、対象プロジェクトでの文書の置き場所の例は、[使い方](docs/ja/usage.md) にあります。

## 段階

作業は6つの段階で進みます。厳密な直列ではありません。規模が大きい場合は、領域ごとに段階1〜2を回し、予定した領域に行き渡ってから段階3に進みます。

| 段階 | 目的 | 主な成果物 |
|---|---|---|
| 0. 現状診断 | どこに力を使うかを決め、方針書の初版を書く | 診断の記録（コマンドと出力を含む）、方針書の初版 |
| 1. 仕様の復元 | コードに書かれていない文脈を取り戻し、用語と決定を正本に残す | 体制の一覧、用語集、ADR、質問票と回答、保留リスト、未決事項 |
| 2. 振る舞いの固定と修正 | 現状をテストで固定し、決定者が「仕様どおりかバグか」を判定してから直す | 特性テストと仕様のテスト、判定の台帳、修正、あとの検査の証明に使うバグの事例 |
| 3. ガードレールの整備 | ルールを設計・型・lint・テストで強制し、エージェントが使う検証スキルを置く | フォーマッタ・linter・型の設定、テスト、検証スキル、形式検証（対象を選んだ場合）、エージェント向けのルールファイル、ルールと強制手段の対応表、自動検証 |
| 4. 完了条件の確認と運用 | 安全に AI 駆動開発を回せることを確認し、運用の流れを決める | 具体化した完了条件、AI 作業の流れ、失敗と訂正の記録の置き場所 |
| 5. 改善ループ | 繰り返す誤りを構造に落とし、次のエージェントが同じ誤りを起こせないようにする | 失敗と訂正の記録と種類の一覧、自動で変えてよい範囲の合意、新しい検査が過去の誤りで失敗する証明、指標 |

段階ごとの作業、完了条件、次の段階に進む判定と、段階をまたぐ原則は、[段階](docs/ja/stages.md) にあります。

## リポジトリの構成

```text
LICENSE
README.md                          # 英語版 README（英語の入口）
README.ja.md                       # このファイル
skills/legacy-to-ai-ready/SKILL.md # スキル本文（日本語。エージェントが読み込むファイル）
docs/installation.md
docs/usage.md
docs/stages.md
docs/faq.md
docs/ja/                           # 上記ドキュメントの日本語版
templates/policy-document.md       # 方針書のテンプレート（日本語。スキル本文と一致）
templates/policy-document.en.md    # テンプレートの英語訳
```

## よくある質問

[よくある質問](docs/ja/faq.md) で、次のような質問に答えています。

- なぜ `SKILL.md` は日本語なのですか
- リポジトリ名は `legacy-to-add` ですが、これがスキル名ですか
- このスキルは、レガシーシステムを代わりに書き直しますか
- 人のレビューは、もう不要ですか
- 使うために pstack や Lauren Tan（`@poteto`）氏のスキルが必要ですか

## 出典

このスキルの「人のレビューに頼らない」構成は、Lauren Tan（[@poteto](https://x.com/poteto)）氏の公開投稿、pstack の公式ガイド、pstack に同梱されたスキルを参考にしています。資料の取得日は 2026-11-19 です。リンクつきの出典は、スキル本文の「出典」にあります。段階の構成、人の判断の線引き、レガシープロジェクトへの当てはめは、このスキル独自のものであり、出典の主張ではありません。

同氏の長文記事（Loops You Can Trust、The Complete Guide to pstack の Pt. 1 と Pt. 2）は、スキルを書いた時点で原文の全文を取得していないため、スキル本文では引用していません。

## ライセンス

[MIT](LICENSE)。Copyright (c) 2026 Takashi Sato / tkc310.
