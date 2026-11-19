# FAQ

日本語: [よくある質問](ja/faq.md)

## Why is SKILL.md in Japanese?

The skill was written and tested in Japanese. The repository ships that file as it is, so that everyone who installs the skill loads the procedure that was tested, not a translation that could drift.

A full English translation is left out on purpose: two long bodies would become two sources of truth. English readers should start at the [README](../README.md) and continue with [Installation](installation.md), [Usage](usage.md), and [Stages](stages.md).

## The repository is named `legacy-to-add`. Is that the skill name?

No. The skill’s `name` is `legacy-to-ai-ready`, and the folder you copy is `skills/legacy-to-ai-ready/`. The GitHub repository that hosts it is [`tkc310/legacy-to-add`](https://github.com/tkc310/legacy-to-add). Use the skill name when you invoke it (`/legacy-to-ai-ready`).

## Does this skill rewrite my legacy system for me?

No. It is a procedure for an agent and the person directing it. Following the stages, the agent may change the *target* project — diagnosis, characterization tests, fixes within what the tests protect, and so on. This repository contains no product code, and the skill forbids a big-bang rewrite.

## Does this mean human review is no longer needed?

No. The skill stops treating “a person reads every diff” as the guarantee of safety. People still decide what is correct, approve the policy, accept behavior-changing changes, approve relaxed checks, and approve irreversible operations. See [What humans still decide](usage.md#what-humans-still-decide).

## Can I skip a stage?

Some flexibility is built in. You can cycle stages 1 and 2 area by area, and you can add minimal test automation during stage 2.

Three shortcuts are ruled out:

- Applying strict repo-wide lint or type rules before behavior in the planned areas is pinned. That waits for stage 3.
- Starting the improvement loop (stage 5) from one-off incidents.
- Removing human review for a kind of change before checks have been shown to stop real mistakes of that kind.

## Do I need pstack, or Lauren Tan’s (`@poteto`) skills, to use this?

No. The skill’s “do not lean on human review” structure draws on Lauren Tan’s public posts, pstack’s official guides, and skills that ship with pstack, all cited in the skill. The skill names and slash commands from those sources were made for a particular tool; whether they exist in *your* environment needs checking. If they do not, build something that meets the requirements written in the skill, fitted to the project.

Longer essays by the same author (including “Loops You Can Trust” and “The Complete Guide to pstack” parts 1 and 2) are not quoted in the skill, because their full text was not retrieved when the skill was written.

## The skill marks some numbers as “needs confirmation”. What should I put there?

The skill deliberately does not fix values such as a questionnaire length, a coverage target, or a recurrence threshold. Put a provisional value in the policy document, mark it as needing confirmation, and revise it after you measure how the project actually behaves.

## Where should the policy document and glossary live?

In one version-controlled place in the *target* project, named in the policy document. This repository ships only the templates. For an example, see [Suggested layout in the target project](usage.md#suggested-layout-in-the-target-project).

## The agent is working in English. Will the Japanese skill still work?

Usually, yes. The agent reads the Japanese instructions and can report to you in English if you ask. Keep hypotheses, glossary entries, and ADRs in the language the project already uses, so the people who approve them can read the source of truth.

## How do I update an installed copy?

Copy the `skills/legacy-to-ai-ready/` folder again, over the install path you chose. See [Updating](installation.md#updating).
