# legacy-to-add

[English](README.md) | [日本語](README.ja.md)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Agent Skills](https://img.shields.io/badge/Agent_Skills-compatible-555555)](https://agentskills.io/specification)
[![Claude Code](https://img.shields.io/badge/Claude_Code-compatible-d97757)](https://code.claude.com/docs/en/skills)
[![Cursor](https://img.shields.io/badge/Cursor-compatible-000000)](https://cursor.com/docs/skills)
[![Skill language](https://img.shields.io/badge/skill_language-Japanese-red)](skills/legacy-to-add/SKILL.md)

An [Agent Skill](https://agentskills.io/specification) that takes a legacy project whose context has been lost and moves it, stage by stage, to a state where AI-assisted development is safe. Safety comes from structure, verification, and artifacts, not from a person reading every diff.

The installable skill is [`skills/legacy-to-add/SKILL.md`](skills/legacy-to-add/SKILL.md). It is written in Japanese. This README and the pages under `docs/` ([Installation](docs/installation.md), [Usage](docs/usage.md), [Stages](docs/stages.md), [Answer collection](docs/answer-collection.md), [FAQ](docs/faq.md)) are the English entry point.

## What this is, and what it is not

This repository ships a procedure for an agent, plus documentation and templates (policy document, feature map, feature specification, glossary term). The agent applies the procedure to a *target* project: the legacy codebase.

It is not a code generator, a linter, or a hosted app. It does not rewrite a legacy system in one pass. It does not claim that human judgment goes away: people still decide what is correct, approve the policy, accept behavior-changing changes, and approve irreversible operations. What the skill rejects is “someone will read the diff” as the guarantee of safety.

## Quick start

Copy the skill folder, not the whole repository, into the skills directory of the tool you use.

**Claude Code** (personal install: every local project on this machine):

```bash
git clone --depth 1 https://github.com/tkc310/legacy-to-add.git /tmp/legacy-to-add
mkdir -p ~/.claude/skills
cp -r /tmp/legacy-to-add/skills/legacy-to-add ~/.claude/skills/legacy-to-add
```

**Cursor** (project install: run from the project root; shareable with the team):

```bash
git clone --depth 1 https://github.com/tkc310/legacy-to-add.git /tmp/legacy-to-add
mkdir -p .cursor/skills
cp -r /tmp/legacy-to-add/skills/legacy-to-add .cursor/skills/legacy-to-add
```

Then start a new session and invoke `/legacy-to-add`, or ask for work that matches the skill’s description.

[Installation](docs/installation.md) covers the other paths (project-level Claude Code, user-level Cursor, other Agent Skills tools), how to confirm that the skill loaded, and how to update an installed copy.

## Usage

The skill requires three confirmations before the agent sends code or documents to an external AI service, or changes anything:

1. **Contract and confidentiality.** May this material be sent to an external AI service? If not, shrink the work to what is allowed.
2. **Write scope.** Which branches and environments may the agent write to? Production systems and production data stay off limits without explicit permission.
3. **People who own judgments**, by role name: the specification, acceptance of behavior-changing changes, releases, and guardrail changes.

A starting prompt:

> We have a legacy project whose original authors are gone, and the documents no longer match the code. Follow the `legacy-to-add` skill. Start at stage 0: diagnose the repository and draft a policy document. Do not change behavior until the policy is approved.

From there, the agent should:

- draft a policy document from [`templates/policy-document.md`](templates/policy-document.md) (English translation: [`templates/policy-document.en.md`](templates/policy-document.en.md)) and update it as the stages progress;
- in stage 1, draft a feature map ([`templates/feature-map.md`](templates/feature-map.md)), feature specifications ([`templates/feature-spec.md`](templates/feature-spec.md)), and glossary terms ([`templates/glossary-term.md`](templates/glossary-term.md)) from the code and from running the app, and call features and screens by those names from then on (each template has an `.en.md` translation);
- stop when a human judgment is required;
- attach evidence to every “done” report: the commands it actually ran and their output, or the location of recordings. A UI change is done only after the app has been run and the evidence attached.

[Usage](docs/usage.md) has more prompts, the full list of expected agent behavior, the judgments that stay with people, and a suggested document layout for the target project.

## Stages

The work goes through six stages. They are not a strict sequence: on a large project, cycle stages 1 and 2 area by area, and move to stage 3 once they have covered the planned areas.

| Stage | Aim | Main artifacts |
|---|---|---|
| 0. Diagnose | Decide where to spend effort, and write the first policy document | Diagnosis record (including commands and output); first policy document |
| 1. Recover the specification | Recover context that is not in the code, and record features, terms, and decisions in one source of truth | Role list; feature map; feature specifications; glossary; ADRs; questionnaires and answers; deferred list; open questions |
| 2. Pin behavior and fix | Pin current behavior with tests, have the decision-maker judge “as specified or a bug”, then fix | Characterization and specification tests; judgment ledger; fixes; bug cases kept to prove later checks |
| 3. Build guardrails | Enforce rules through design, types, lint, and tests, and give the agent a verification skill | Formatter, linter, and type settings; tests; verification skill (driven by the stage-1 feature map); property-based UI tests and formal verification where chosen; agent rule files; rules-to-enforcement mapping; automated checks |
| 4. Confirm done criteria and operate | Confirm that AI-assisted work can run safely, and define how it runs day to day | Project-specific done criteria; the AI-work flow; a place to record failures and corrections |
| 5. Improvement loop | Turn recurring mistakes into structure, so the next agent cannot make them | Failure and correction records, grouped by kind; agreed scope of automatic changes; proof that new checks fail on past mistakes; metrics |

[Stages](docs/stages.md) describes the work, done criteria, and gate for each stage, and the principles that apply in all of them. In stage 1 the agent proposes how to collect questionnaire answers and you choose; [Answer collection](docs/answer-collection.md) describes the methods, including GitHub + Slack + a Claude artifact.

The feature map and the glossary act as a ubiquitous language: people and agents refer to features and screens by the same names, and the stage-3 verification skill uses the same map as its feature list. Stage 3 also has criteria for deciding whether to adopt property-based UI tests, and stage 1 says when to express a complex workflow as a state machine or statechart (XState, for example).

## Repository layout

```text
LICENSE
README.md                          # this file (English)
README.ja.md                       # Japanese README
skills/legacy-to-add/SKILL.md # the skill (Japanese; the file an agent loads)
docs/installation.md
docs/usage.md
docs/stages.md
docs/answer-collection.md
docs/faq.md
docs/ja/                           # Japanese versions of the docs
templates/policy-document.md       # policy template (Japanese; matches the skill)
templates/policy-document.en.md    # English translation of the template
templates/feature-map.md           # feature map template (Japanese; .en.md is the translation)
templates/feature-spec.md          # feature specification template, one file per feature (.en.md too)
templates/glossary-term.md         # glossary term template, one file per term (.en.md too)
```

## FAQ

The [FAQ](docs/faq.md) answers these and other questions:

- Why is `SKILL.md` in Japanese?
- Does this skill rewrite my legacy system for me?
- Does this mean human review is no longer needed?
- Do I need pstack, or Lauren Tan’s (`@poteto`) skills, to use this?

## Sources

The skill’s “do not lean on human review” structure draws on public posts by Lauren Tan ([@poteto](https://x.com/poteto)), pstack’s official guides, and skills that ship with pstack. Those sources were retrieved on 2026-11-19. The verification layer, runtime verification, and the feature map also draw on an excerpt of a talk by Lauren Tan that the requester supplied; the talk’s title, date, and URL are not in the excerpt and need confirmation. For property-based tests and statecharts, the skill cites the fast-check and XState documentation, retrieved on 2026-11-20; API details can change, so check the current documentation before applying them. The citations, with links, are in the skill under 「出典」. The stage structure, the line between human and agent judgments, and the application to legacy projects are this skill’s own, not claims made by those sources.

Longer essays by the same author (including “Loops You Can Trust” and “The Complete Guide to pstack” parts 1 and 2) are not quoted in the skill, because their full text was not retrieved when the skill was written.

## License

[MIT](LICENSE). Copyright (c) 2026 Takashi Sato / tkc310.
