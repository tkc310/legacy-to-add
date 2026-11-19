# FAQ

日本語の FAQ は [faq（日本語）](ja/faq.md) を見てください。

## Why is SKILL.md in Japanese?

The skill was written and tested in Japanese. This repository keeps that file unchanged so that installers load the same artifact, not a translation that could drift. English readers should start at [README.md](../README.md) and the pages under [`docs/`](.).

A full English translation of the skill is not included, on purpose: two long bodies would become two sources of truth.

## The repository is named `legacy-to-add`. Is that the skill name?

No. The installable skill’s `name` is `legacy-to-ai-ready`, and the folder you copy is `skills/legacy-to-ai-ready/`. The GitHub repository that hosts it is [`tkc310/legacy-to-add`](https://github.com/tkc310/legacy-to-add). Use the skill name when you invoke it (`/legacy-to-ai-ready`).

## Does this skill rewrite my legacy system for me?

No. It is a procedure for an agent (and the person directing it). The agent may change the *target* project according to the stages — diagnosis, characterization tests, guarded fixes, and so on — but this repository itself contains no product code, and the skill forbids a big-bang rewrite.

## Does this mean human review is no longer needed?

No. The skill stops treating “a person reads every diff” as the way safety is guaranteed. People still decide what is correct, approve the policy, accept behavior-changing changes, approve relaxed checks, and approve irreversible operations. See [Usage](usage.md#what-humans-still-decide).

## Can I skip a stage?

You can cycle stages 1 and 2 per area, and you can add minimal test automation during stage 2. You should not jump to repo-wide strict lint or type rules before behavior in the planned areas is pinned (stage 3 waits for that). You should not start the improvement loop (stage 5) from one-off incidents. You should not remove human review for a kind of change before checks have been shown to catch real mistakes of that kind.

## Do I need pstack, or Lauren Tan’s (`@poteto`) skills, to use this?

No. The skill’s “do not lean on human review” structure draws on Lauren Tan’s public posts, pstack’s official guides, and skills that ship with pstack. Those sources are cited in the skill. The bundled skill names and slash commands are written for a particular toolchain; whether they exist in *your* environment is something to check. If they are not available, build something that meets the requirements in the skill, fitted to the project.

Longer essays by the same author (including “Loops You Can Trust” and “The Complete Guide to pstack” parts 1 and 2) were not quoted in the skill, because the full text of those essays was not retrieved when the skill was written.

## The skill says some numbers are “needs confirmation.” What should I put there?

The skill deliberately does not fix values such as a questionnaire length, a coverage target, or a recurrence threshold. Put a temporary value in the policy document, mark it as needing confirmation, and change it after you measure how the project actually behaves.

## Where should the policy document and glossary live?

In one version-controlled place in the *target* project, named in the policy document. This repository only ships templates. A suggested layout is in [Usage](usage.md#suggested-layout-in-the-target-project).

## The agent is working in English. Will the Japanese skill still work?

Usually yes. The agent loads the Japanese instructions and can still report to you in English if you ask it to. Keep hypotheses, glossary entries, and ADRs in the language the project already uses, so the source of truth stays readable for the people who approve it.

## How do I update an installed copy?

Copy the `skills/legacy-to-ai-ready/` folder again, over the install path you chose. See [Installation](installation.md#updating).
