# Usage

日本語: [使い方](ja/usage.md)

This page is for the person who asks an agent to follow the skill. Install the skill first ([Installation](installation.md)). The procedure itself is in [`skills/legacy-to-add/SKILL.md`](../skills/legacy-to-add/SKILL.md).

## When to use it

Use this skill when a project has lost the context that used to live in people’s heads or in documents, and you want AI-assisted development on it to be safe because of structure, checks, and artifacts — not because a person reads every diff.

Typical signs:

- The original authors are gone, or will be gone soon.
- Documents and code disagree, or documents are missing.
- Tests are missing, failing, or do not protect the paths that matter.
- The team wants agents to change the code, but has no way yet to catch recurring mistakes automatically.

Do not use it for a one-shot rewrite. The skill rules out big-bang rewrites and has the agent replace code incrementally.

## Before you start

The skill requires three confirmations before the agent sends code or documents to an external AI service, or changes anything:

1. **Contract and confidentiality.** May this code and these documents be sent to an external AI service? If not, shrink the work to what is allowed (for example, on-device processing only).
2. **Write scope.** Which branches and environments may the agent touch? Production systems and production data are off limits without explicit permission.
3. **People who own judgments.** Who decides the specification, who accepts behavior-changing changes, who approves releases, and who approves guardrail changes? Record them by role name. A role nobody fills goes into the policy document as a risk.

## Example prompts

Invoke the skill by name (`/legacy-to-add` in Claude Code or Cursor) and then state the request, or name the skill in the prompt.

Start at stage 0:

> We have a legacy project whose original authors are gone, and the documents no longer match the code. Follow the `legacy-to-add` skill. Start at stage 0: diagnose the repository and draft a policy document. Do not change behavior until the policy is approved.

Resume in a later stage:

> Continue `legacy-to-add` on this repository. We already approved the policy document at `docs/ai-ready/policy.md`. We are in stage 2 for the billing area. Pin the current behavior with characterization tests before any fix.

Keep the agent from deciding what only a person can decide:

> Follow `legacy-to-add`. Recover the specification for the authentication area (stage 1). Draft a short multiple-choice questionnaire with only the blocking or high-priority questions. Show the respondent just the question, a one-line hypothesis, and a few options; keep the evidence in your own ledger. Do not treat your hypotheses as confirmed.

Choose how answers are collected before the questionnaire goes out:

> Follow `legacy-to-add`. Before you send the stage-1 questionnaire, propose the ways to collect answers, including GitHub as the source of truth with Slack for input and a Claude artifact for display. State the concerns for each and wait for my choice. If I choose that option, walk me through the build one step at a time.

The methods, the concerns, and the build guide are in [Answer collection](answer-collection.md).

Japanese versions of these prompts are in [使い方](ja/usage.md).

## What the agent should do

The skill tells the agent to:

1. Start with the three confirmations above and a stage-0 diagnosis, then produce the first policy document.
2. Say, for each piece of work, which stage, area, and step it belongs to in the policy document.
3. Split work into small units that can each be verified, and pass the check for one unit before starting the next.
4. Attach evidence to every “done” report: the commands actually run and their output, or the location of recordings. State plainly what was not checked.
5. Report, with reasons, whether the stage’s done criteria and the gate to the next stage are met. An unmet item is reported as unmet.
6. Stop when a human judgment is required, and hand over short decision material, evidence, and a recommendation.
7. On receiving the same correction a second time, propose a change to the repository itself, at the highest level that works (design, then types or lint, then tests), instead of trying to remember the instruction.
8. Never judge its own change as passing. Never weaken tests, expected values, or the properties under verification to make a check pass.
9. Write new findings, decisions, and open questions back into the glossary, ADRs, judgment ledger, rules-to-enforcement mapping, and policy document.
10. State numbers and tool behavior only after checking them. Anything unchecked is marked “needs confirmation” (`要確認` in the skill text).
11. Before the first questionnaire goes out, propose ways to collect the answers, with their concerns. Send or build nothing until the user has chosen.

The skill also lists common failures and how to avoid each one, under 「よくある失敗と回避策」.

## Stages and the policy document

The work goes through stages 0 to 5: diagnose, recover the specification, pin behavior and fix, build guardrails, confirm done criteria and operate, and run the improvement loop. The [README](../README.md#stages) has a one-table summary; [Stages](stages.md) describes the work, done criteria, and gate for each stage.

The agent drafts the policy document in stage 0 and updates it as the stages progress. Two templates are provided:

- [`templates/policy-document.md`](../templates/policy-document.md) — Japanese; matches the skill.
- [`templates/policy-document.en.md`](../templates/policy-document.en.md) — English translation.

If the two disagree, the Japanese template and the skill are the source of truth.

## What humans still decide

The skill does not try to remove human judgment. It stops using “a person reads the diff” as the guarantee of safety.

People still:

- Confirm hypotheses, judge “as specified or a bug”, and decide the intended (To-Be) specification.
- Approve the policy document and its priorities.
- Accept behavior-changing changes, including their impact on users and existing data.
- Approve changes that relax tests or checks, and approve exceptions.
- Approve irreversible operations: production releases, data deletion or migration, sending data outside.
- Change agent permissions and the scope of work left to agents.

For these, the agent should bring short decision material and evidence, not a request to read every changed line.

## Suggested layout in the target project

The skill does not prescribe path names. Pick one place under version control and name it in the policy document. One layout that is easy to navigate:

```text
docs/ai-ready/
  policy.md              # the policy document, updated each stage
  diagnosis.md           # stage 0 record
  glossary.md
  adr/
  questionnaires/
  judgment-ledger.md
  rules-to-enforcement.md
  failures/
```

If the project already has a place that can serve as the single source of truth, use it. Chat is not a second source of truth: a decision made there counts once it has been written back.

How the answers reach this place is a separate choice. In stage 1 the agent proposes collection methods and waits for yours; whichever you choose, the answers are written back here. If you choose Slack for input, add an append-only answer log (for example `answers/`). See [Answer collection](answer-collection.md).
