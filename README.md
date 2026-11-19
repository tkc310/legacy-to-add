# legacy-to-ai-ready

[English](README.md) | [日本語](README.ja.md)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Agent Skills](https://img.shields.io/badge/Agent_Skills-compatible-555555)](https://agentskills.io/specification)
[![Claude Code](https://img.shields.io/badge/Claude_Code-compatible-d97757)](https://code.claude.com/docs/en/skills)
[![Cursor](https://img.shields.io/badge/Cursor-compatible-000000)](https://cursor.com/docs/skills)
[![Skill language](https://img.shields.io/badge/skill_language-Japanese-red)](skills/legacy-to-ai-ready/SKILL.md)

An [Agent Skill](https://agentskills.io/specification) for taking a legacy project whose context has been lost, and moving it — stage by stage — to a state where AI-assisted development is safe because of structure, verification, and artifacts, not because a person reads every diff.

The installable skill is [`skills/legacy-to-ai-ready/SKILL.md`](skills/legacy-to-ai-ready/SKILL.md). It is written in Japanese; this README and [`docs/`](docs/) are the English entry point. The GitHub repository that hosts the skill is [`tkc310/legacy-to-add`](https://github.com/tkc310/legacy-to-add).

## What this is, and what it is not

This repository ships a procedure for an agent, plus documentation and a policy-document template. The agent applies that procedure to a *target* project (the legacy codebase).

It is not a code generator, a linter, or a hosted app. It does not rewrite a legacy system in one pass. It does not claim that human judgment goes away. People still decide what is correct, approve the policy, accept behavior-changing changes, and approve irreversible operations. What the skill argues against is using “someone will read the diff” as the guarantee of safety.

## Contents

```text
LICENSE
README.md                          # this file (English)
README.ja.md                       # Japanese README
skills/legacy-to-ai-ready/SKILL.md # the skill (Japanese; the artifact an agent loads)
docs/installation.md
docs/usage.md
docs/stages.md
docs/faq.md
docs/ja/                           # Japanese versions of the docs
templates/policy-document.md       # policy template (Japanese; matches the skill)
templates/policy-document.en.md    # English translation of the template
```

## Quick start

Copy the skill folder into the skills directory of the tool you use. Do not copy the whole repository.

**Claude Code** (personal install, every local project on this machine):

```bash
git clone --depth 1 https://github.com/tkc310/legacy-to-add.git /tmp/legacy-to-add
mkdir -p ~/.claude/skills
cp -r /tmp/legacy-to-add/skills/legacy-to-ai-ready ~/.claude/skills/legacy-to-ai-ready
```

**Cursor** (project install, shareable with the team):

```bash
git clone --depth 1 https://github.com/tkc310/legacy-to-add.git /tmp/legacy-to-add
mkdir -p .cursor/skills
cp -r /tmp/legacy-to-add/skills/legacy-to-ai-ready .cursor/skills/legacy-to-ai-ready
```

Then start a new session and invoke `/legacy-to-ai-ready`, or ask for work that matches the skill’s description. Paths for project-level Claude Code, user-level Cursor, other Agent Skills tools, and how to confirm the skill loaded are in [Installation](docs/installation.md).

## Usage

Before the agent sends code or documents to an external AI service, or starts making changes, confirm three things (the skill requires this):

1. Whether the contract allows sending this material to an external AI service. If not, shrink the work to what is allowed.
2. Which branches and environments the agent may write to. Production systems and production data stay off limits without explicit permission.
3. Who owns judgments, by role name: the specification, acceptance of behavior-changing changes, releases, and guardrail changes.

A starting prompt:

> We have a legacy project whose original authors are gone, and the documents no longer match the code. Follow the `legacy-to-ai-ready` skill. Start at stage 0: diagnose the repository and draft a policy document. Do not change behavior until the policy is approved.

The agent should draft a policy document from [`templates/policy-document.md`](templates/policy-document.md) (or the English translation in [`templates/policy-document.en.md`](templates/policy-document.en.md)), update it as stages progress, stop when a human judgment is required, and attach evidence (commands actually run and their output, or the location of recordings) to any “done” report.

More prompts, the agent’s expected behavior, what humans still decide, and a suggested document layout in the target project: [Usage](docs/usage.md).

## Stages

Work goes through six stages. They are not a rigid line: on a large project, cycle stages 1 and 2 per area, then move to stage 3 when those areas are covered.

| Stage | Aim | Main artifacts |
|---|---|---|
| 0. Diagnose | Decide where to spend effort, and write the first policy document | Diagnosis record (including commands and output); first policy document |
| 1. Recover the specification | Put terms and decisions back into a source of truth | Role list; glossary; ADRs; questionnaires and answers; deferred and open items |
| 2. Pin behavior and fix | Characterize what the system does now, judge “as specified or a bug”, then fix | Characterization and specification tests; judgment ledger; fixes; bug cases kept as proof for later checks |
| 3. Build guardrails | Encode rules in design, types, lint, and tests; add a verification skill | Formatter, linter, and type settings; tests; verification skill; optional formal verification; agent rules; rules-to-enforcement mapping; CI |
| 4. Confirm done criteria and operate | Confirm that AI-assisted work can run safely, and define the operating loop | Concrete done criteria; the AI-work flow; a place to record failures and corrections |
| 5. Improvement loop | Turn recurring mistakes into structure, and adjust the guardrails and harness | Failure catalog; agreed automatic-change scope; proofs that new checks fail on past mistakes; metrics |

Done criteria, gates to the next stage, and the principles that apply in every stage: [Stages](docs/stages.md).

## FAQ

- Why is `SKILL.md` in Japanese?
- The repository name is `legacy-to-add`. Is that the skill name?
- Does this skill rewrite a legacy system?
- Does this mean human review is no longer needed?
- Do I need pstack, or skills by Lauren Tan (`@poteto`)?

Answers: [FAQ](docs/faq.md).

## Sources

The skill’s “do not lean on human review” structure draws on public posts by Lauren Tan ([@poteto](https://x.com/poteto)), pstack’s official guides, and skills that ship with pstack. The sources were retrieved on 2026-11-19. Citations, with links, are in the skill under 「出典」. The stage structure, the line between human and agent judgments, and the application to legacy projects are this skill’s own and are not claims of those sources.

Longer essays by the same author (including “Loops You Can Trust” and “The Complete Guide to pstack” parts 1 and 2) are not quoted in the skill, because the full text of those essays was not retrieved when the skill was written.

## License

[MIT](LICENSE). Copyright (c) 2026 Takashi Sato / tkc310.
