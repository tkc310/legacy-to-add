# Usage

Install the skill first: see [Installation](installation.md). The procedure itself is in [`skills/legacy-to-ai-ready/SKILL.md`](../skills/legacy-to-ai-ready/SKILL.md). This page is a guide for the person who asks an agent to follow it.

日本語の手順は [usage（日本語）](ja/usage.md) を見てください。

## When to use it

Use this skill when a project has lost the context that used to live in people’s heads or in documents, and you want to reach a state where AI-assisted development is safe because of structure, checks, and artifacts — not because a person reads every diff.

Typical signs:

- The original authors are gone, or will be gone soon.
- Documents and the code disagree, or documents are missing.
- Tests are missing, failing, or do not protect the paths that matter.
- The team wants agents to change the code, but does not yet have a way to catch recurring mistakes automatically.

Do not use it as a one-shot rewrite of the system. The skill refuses big-bang rewrites and asks the agent to replace incrementally.

## Before any code is sent to an external AI service

The skill requires three confirmations before the agent sends code or documents to an external AI service, or makes changes:

1. **Contract and confidentiality.** May this code and these documents be sent to an external AI service? If not, shrink the work to what is allowed (for example, on-device processing only).
2. **Write scope.** Which branches and environments may the agent touch? Production systems and production data are off limits without explicit permission.
3. **People who own judgments.** Who decides the specification, who accepts behavior-changing changes, who approves releases, and who approves guardrail changes? Record them by role name. Missing roles belong in the policy document as risks.

## Example prompts

You can invoke the skill by name (`/legacy-to-ai-ready` in Claude Code or Cursor) and then state the request, or include the skill name in the prompt.

Start at stage 0:

> We have a legacy project whose original authors are gone, and the documents no longer match the code. Follow the `legacy-to-ai-ready` skill. Start at stage 0: diagnose the repository and draft a policy document. Do not change behavior until the policy is approved.

Resume in a later stage:

> Continue `legacy-to-ai-ready` on this repository. We already approved the policy document at `docs/ai-ready/policy.md`. We are in stage 2 for the billing area. Pin the current behavior with characterization tests before any fix.

Ask the agent to stop at a human decision:

> Follow `legacy-to-ai-ready`. Recover the specification for the authentication area (stage 1). Draft a multiple-choice questionnaire from the code and history. Do not treat your hypotheses as confirmed.

Japanese examples are in [usage（日本語）](ja/usage.md).

## What the agent should do

The skill tells the agent to:

1. Start with the pre-flight checks and a stage-0 diagnosis, then produce the first policy document.
2. Say, for each piece of work, which stage, area, and step it is in, against the policy document.
3. Split work into small units that can be checked on their own, and not move on until the check for that unit passes.
4. Attach evidence to a “done” report: commands actually run and their output, or the location of recordings. Write down what was not checked.
5. Report whether the stage’s done criteria and the gate to the next stage are met, with reasons. Unmet items stay unmet; they are not papered over.
6. Stop when a human judgment is required, and hand over short decision material, evidence, and a recommendation.
7. When the same correction appears a second time, propose a change to the repository (design, types, lint, or tests) rather than memorizing the instruction.
8. Not mark its own change as accepted. Not weaken tests, expectations, or properties under verification just to make a check pass.
9. Write new findings, decisions, and open questions back into the glossary, ADRs, judgment ledger, rules-to-enforcement mapping, and policy document.
10. State numbers and tool behavior only after checking them. Unchecked items are marked “needs confirmation” (`要確認` in the skill text).

A longer list of failure modes and how the skill avoids them is in the skill, under 「よくある失敗と回避策」.

## Stages and artifacts

The work goes through stages 0 to 5. Stages are not a rigid line: on a large project, run stages 1 and 2 per area, then move to stage 3 once those areas are covered.

| Stage | Aim | Main artifacts |
|---|---|---|
| 0. Diagnose | Decide where to spend effort, and write the first policy document | Diagnosis record (including commands and output); first policy document |
| 1. Recover the specification | Put terms and decisions back into a source of truth | Role list; glossary; ADRs; questionnaires and answers; deferred list; open questions |
| 2. Pin behavior and fix | Characterize what the system does now, judge “as specified or a bug”, then fix | Characterization tests and specification tests; judgment ledger; fixes; bug cases kept as proof for later checks |
| 3. Build guardrails | Encode rules in design, types, lint, and tests, and add a verification skill | Formatter, linter, and type settings; tests; verification skill; optional formal verification; agent rules; rules-to-enforcement mapping; CI |
| 4. Confirm done criteria and operate | Confirm that AI-assisted work can run safely, and define the operating loop | Concrete done criteria; the AI-work flow; a place to record failures and corrections |
| 5. Improvement loop | Turn recurring mistakes into structure, and adjust the guardrails and harness | Failure catalog; agreed automatic-change scope; proofs that new checks fail on past mistakes; metrics |

Details, done criteria, and gates: [Stages](stages.md).

The agent updates the policy document as stages progress. Start from the template in [`templates/policy-document.md`](../templates/policy-document.md) (Japanese, matches the skill) or [`templates/policy-document.en.md`](../templates/policy-document.en.md) (English translation). If the two disagree, the Japanese template and the skill are the source of truth.

## What humans still decide

This skill does not try to remove human judgment. It tries to stop using “a person reads the diff” as the way safety is guaranteed.

Humans still:

- Confirm hypotheses, judge “as specified or a bug”, and decide the intended (To-Be) specification.
- Approve the policy document and its priorities.
- Accept behavior-changing changes (including the impact on users and existing data).
- Approve relaxations of tests or checks, and exceptions.
- Approve irreversible operations (production releases, data deletion or migration, sending data outside).
- Change agent permissions and the scope of work left to agents.

The agent should bring short decision material and evidence, not a request to read every changed line.

## Suggested layout in the target project

The skill does not mandate path names. Pick one place under version control and write it in the policy document. A layout that stays easy to find:

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

Use whatever already exists in the project if it can serve as the single source of truth. Do not keep a second copy in chat.
