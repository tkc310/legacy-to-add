# Stages

日本語: [段階](ja/stages.md)

This page summarizes stages 0 to 5 of [`skills/legacy-to-add/SKILL.md`](../skills/legacy-to-add/SKILL.md). The skill is the source of truth for the procedure, the checklists, and the wording.

The skill does not fix numbers such as a coverage target, a cap on questions per questionnaire, or a recurrence threshold. Set a provisional value per project, mark it “needs confirmation”, and revise it after you measure.

## How the stages relate

```text
0 Diagnose and write the policy
        ↓  (policy priorities are approved; build and run are reproducible)
1 Recover the specification   ←→  2 Pin behavior and fix
        ↓  (stages 1–2 have covered the planned areas; tests are stable)
3 Build guardrails
        ↓  (a small trial: mistakes are stopped, evidence is produced,
        ↓   the agent stops for human judgments)
4 Confirm done criteria and operate
        ↓  (the same kind of mistake is recurring)
5 Improvement loop (turn recurring mistakes into structure)
```

The stages are not a strict sequence. On a large project, cycle stages 1 and 2 area by area, and move to stage 3 once they have covered the planned areas.

Minimal automation, such as running tests in CI, may be added during stage 2. Strict repo-wide conventions wait until stage 3: applied before behavior is settled, they produce a wave of mechanical changes that gets mixed up with judgments and fixes.

## What stands in for human review

The skill does not rely on a person reading every diff to keep changes safe. Agents produce changes faster than people can read them carefully. It relies on five kinds of assurance instead:

1. **Make recurring mistakes impossible through structure.** Choose the strongest enforcement that works, in this order: design and data structures; types, then lint or CI checks; tests; documents and agent rules. Documents come last because nothing fails when they are not followed. Human review is not a level in this order.
2. **Exercise the product the way a user would,** and keep evidence. The steps live in a project-local verification skill.
3. **Formally verify the highest-risk properties.** For properties that cause serious harm when broken and that tests reach poorly, check every case within a stated scope.
4. **Build in small, verifiable units.** Each unit ends in a check. Do not move on until it passes.
5. **Prove with artifacts.** “It compiled” is not evidence. Show the result of running the real thing: commands and their output, a test that failed and then passed, recordings of the screen.

This approach has limits. Checks tell you whether the system does what was decided. They cannot decide what is correct, and in a legacy project that answer is missing from the code to begin with. So judging the specification and approving the policy stay with people.

In stages 0 to 2 the structural guarantees are still thin, and more human confirmation remains. Remove human review only for kinds of change whose checks have been shown to stop real mistakes — never before.

## Stage 0: Diagnose the current state

**Purpose.** Decide how much effort each stage should get, and write the first policy document.

**Inputs.** The repository; the build and deploy setup; tickets and documents, if any; interviews with the requester.

**Work, in brief.**

- Investigate size and structure, tests, types, build and deploy, the means of exercising the system, who holds domain knowledge and for how long they can be reached, operations, recurring mistakes, and existing documents.
- Record separately what you ran, what you only read, and what you were told. Keep the commands and output for anything you ran.
- Set priorities from what the diagnosis found. For example: if the expert is about to leave, interview first; if deploys are manual with no way back, write a rollback procedure before any behavior-changing change.
- Draft the policy document and ask the requester to approve it.

**Artifacts.** Diagnosis record (including commands and output); first policy document.

**Done when.** Each aspect is marked confirmed or unconfirmed; priorities and their reasons are written down; the people who own judgments are named by role.

**Gate to stage 1.** The requester agrees with the priorities in the policy document. If build and run cannot be reproduced, fix that first.

## Stage 1: Recover the specification

**Purpose.** Recover the context that is not in the code, and record terms and decisions in a single source of truth. Later stages use it to judge “as specified or a bug”.

**Inputs.** Code, commit history, review records, tickets, and existing documents; past conversations, as far as permitted; the stage-0 diagnosis.

**Work, in brief.**

- List decision-makers and experts by role, per functional area.
- Dig before asking: do not ask what the code, history, or tickets can tell you. What you find goes into the glossary as a hypothesis, with its evidence.
- Filter the questions before sending. Classify them and merge duplicates. Drop any that the code, the history, or a run can answer, and any whose answer would not change the implementation or a judgment. Defer any that are neither blocking nor high-priority to a later questionnaire. Send only blocking or high-priority questions.
- Ask one thing per question. Keep As-Is (confirming a hypothesis about current behavior) separate from To-Be (asking the decision-maker what should be true).
- Bundle the questions into a multiple-choice questionnaire and send it asynchronously. The respondent sees only a short summary (what is needed, why, the deadline, and the estimated time) and, for each question, one or two short sentences in the respondent’s own words, a one-line hypothesis to confirm, and about two to four options plus “I don’t know (who would?)”. Evidence, classification, and the blocking and priority tags stay in an agent-side ledger, or are folded away so the respondent can answer without reading them.
- Before sending, check that the questionnaire is within its caps on question count and time (values need confirmation), and that each question can be answered in about ten seconds or less. If one cannot, split it or simplify it.
- Before the first questionnaire goes out, propose ways to collect the answers and let the user choose. One option is GitHub as the source of truth, Slack as the input, and a Claude artifact as the display; simpler ones are documents in the repository, GitHub Issue forms, or a static site. The agent states the concerns when it proposes, and if the user picks the GitHub + Slack + artifact option, walks them through the build step by step. Whatever the method, the questionnaire rules above still apply, and there is one source of truth with a history of changes. See [Answer collection](answer-collection.md).
- Write the answers back into the glossary and ADRs. Answers received through an input channel such as Slack go into the answer log in the source of truth first. An entry becomes confirmed when the respondent or decision-maker confirms it.
- Keep hypotheses and confirmed entries visibly distinct. The agent does not change behavior on the strength of a hypothesis.

**Artifacts.** Role list; glossary; ADRs; questionnaires and answers; deferred list; list of open questions.

**Done when, for the area in scope.** Every blocking question is confirmed, or the decision-maker has explicitly deferred it; the main terms are in the glossary as confirmed; the remaining hypotheses are listed.

**Gate to stage 2.** The area has enough confirmed terms and decisions to judge whether behavior is correct. Do not wait for the whole system: go area by area, and send questions that come up in stage 2 back to a stage-1 questionnaire.

## Stage 2: Pin behavior and fix toward the intended shape

**Purpose.** Pin current behavior with tests, then fix it toward the intended shape, bugs included. Keep a record of each judgment.

**Inputs.** The stage-1 glossary, ADRs, and list of open questions; the code in scope; bug reports and logs, if any.

**Work, in brief.**

- Start with high-risk paths and paths you already plan to change. Keep each target small enough to verify on its own.
- Before fixing anything, write characterization tests that pin current behavior as it is, even where it looks wrong. Write them at observable boundaries (public functions, APIs, screens, output files), not against internal call order. Mark each one “pins current behavior; correctness not yet judged”.
- Check that the tests protect something: breaking the behavior should fail the test, and changing only the internals should not.
- For each pinned behavior, the decision-maker judges: as specified; a bug; not the specification, but depended upon; or unknown. The agent supplies the material and a recommendation. Record each judgment in the judgment ledger. “Unknown” goes back to a stage-1 questionnaire, and the behavior stays as it is until then.
- For a bug, first write a test for the intended behavior and confirm that it fails; then fix. Commit the two separately.
- Do not mix behavior changes with refactoring. Refactor only within what the tests protect, and confirm the test results are the same before and after.
- Besides tests and build, run the real thing, and report the results as they are.
- Keep each fixed bug as a case that later checks must fail on. Note candidates for formal verification as you find them.

**Artifacts.** Characterization tests and specification tests; judgment ledger; fixes and refactorings; updated glossary and ADRs; fixed-bug cases for later proofs; list of formal-verification candidates.

**Done when, for the area in scope.** High-risk paths are protected by tests; every pinned behavior has a judgment, with “unknown” ones listed; each bug is fixed or has a scheduled response; the places still marked “not yet judged” are known.

**Gate to stage 3.** Stages 1 and 2 have covered all the planned areas, and the tests pass reliably, with no flaky tests left.

## Stage 3: Build guardrails

**Purpose.** Reach a state where, whether a person or an agent makes the change, a mistake is either impossible by construction or caught early and mechanically. Also give the agent a way to check behavior for itself.

**Inputs.** The tests and records from stages 1–2, the fixed-bug cases, and the formal-verification candidates; how the code is actually written today; the official documentation of the tools in use.

**Work, in brief.**

- Base conventions on what the code mostly does already and on what stage 2 fixed, not on an ideal.
- Before adding prose or checks, try to design the mistake away: one owner per piece of state, one right way per task, hidden internals, a single source instead of hand-synced lists, and no stale patterns for an agent to copy.
- Add mechanical checks one at a time: formatter, then linter (rules that find errors first), then types (from the boundaries inward), then more tests around risk, past bugs, and boundary values. Where existing violations are many, consider stopping new ones first and reducing the recorded rest. Every exception carries a reason, a deadline, and a human approval.
- Create a verification skill that drives the product as a user would and stores evidence. Run it end to end once before relying on it.
- Consider formal verification for the candidates from stage 2. State the scope, and show that the check fails on a deliberately broken version. If you skip a candidate, write down why.
- Write prose conventions only for what needs judgment. Move anything mechanically checkable into lint, types, or tests.
- Write short agent rule files that point at the sources of truth. File names and locations depend on the tool; confirm them.
- Keep a mapping from each rule to what enforces it: design, types, lint, tests, or nothing.
- Make the checks run automatically on every change, with the same commands locally and in CI.
- Prove each new check: show that it fails on a real past mistake, or on a deliberately broken change that you then discard.

**Artifacts.** Formatter, linter, and type settings; expanded tests; verification skill and its feature list; formal verification and its scope, where chosen; naming and coding conventions; agent rule files and the rules-to-enforcement mapping; automated-check settings; list of existing violations and a plan to reduce them.

**Done when.**

- Checks run automatically, and a failing change cannot be merged. The same commands run locally and in CI.
- Conventions and rule files have a single source of truth.
- Existing violations and exceptions are known, and each exception has a reason, a deadline, and an approval.
- Each check has been shown to stop a real mistake.
- The verification skill has been run end to end at least once, and the evidence is kept.
- Each formal-verification candidate was either carried out or declined with a written reason.

**Gate to stage 4.** Give an agent a small real task, relying only on the rule files and the verification skill. Move on when (1) the guardrails stop its mistakes, (2) it can check behavior with the verification skill and produce evidence, and (3) it stops where a human judgment is required.

## Stage 4: Confirm done criteria and operate

**Purpose.** Confirm that “AI-assisted development can run safely here” is true, and decide how to keep it true. Verification is the step most likely to wait on a person, so make it something agents can do.

**Inputs.** The artifacts of stages 1–3; records of trial AI work.

**Work, in brief.**

- Make the skill’s done-criteria checklist specific to the project. For any item that is not met, write the reason and when it will be addressed. The checklist covers:
  - build, test, and run reproducible from the documented steps alone;
  - high-risk paths protected by stable tests;
  - automatic checks that block failing changes;
  - a verification skill;
  - formal verification carried out, or declined with a reason;
  - a glossary and ADRs that keep hypotheses and confirmed entries distinct;
  - a judgment ledger that lists what is not yet judged;
  - agent rule files that name the work requiring a human judgment;
  - the rules-to-enforcement mapping, with unenforced rules known;
  - agent permissions limited to what is needed;
  - a release rollback procedure that has been tried;
  - named owners for each judgment;
  - trial AI work in which mistakes were caught by checks before merge.
- Define the AI-work flow:
  1. A request that states its done criteria as executable checks.
  2. A plan from the agent, split into verifiable units.
  3. Implementation unit by unit, passing each unit’s check before the next.
  4. Automatic checks.
  5. A check of the real thing, in a way that fits the kind of change.
  6. Independent verification by an agent *other than* the author, by running it.
  7. Hand-off to a person for changes that require human judgment.
  8. Merge.
- Widen what is left to agents step by step, starting with low-risk work. Remove human review for a kind of change only when a means of assurance exists and has a record of stopping mistakes. A person makes that call.
- Record AI failures, human corrections, CI failures, and reverts in a form that can be classified later. This is the input to stage 5.
- Keep the means of verification current. Inspect the verification skill regularly (the frequency needs confirmation per project), and fix only the skill itself: a product regression is reported as a regression, not hidden by editing the document. Revisit formal verification when the code it covers changes.
- Keep knowledge current: new decisions go into ADRs, new terms into the glossary, and remaining hypotheses and the deferred list are reviewed regularly.
- Follow the contract for handover and for deleting materials kept locally when the work ends.

**Artifacts.** Project-specific done criteria and the result of checking them; a document describing the AI-work flow; a place to record failures and corrections; results of inspecting the verification skill.

**Done when.** Every checklist item is met or is an approved exception; real work has gone through the agreed flow several times (how many is left as “needs confirmation”).

**Gate to stage 5.** Start stage 5 when the same kind of mistake appears repeatedly in the record of failures and corrections. Recurring mistakes found in stage 0 can serve as the first target. Do not start while all you have is one-off cases: rules made from single cases become over-regulation.

## Stage 5: Improvement loop

**Purpose.** Stop catching the same mistake by human correction or review every time. For each kind of mistake, change the repository so the next agent cannot make it.

**Inputs.** Corrections people gave the agent; records of AI failures; recent commits, reverts, review comments, and comments explaining workarounds; CI results; growth in lint suppressions, exceptions, and duplicated code; defects found after merge; the current rules-to-enforcement mapping, tests, lint settings, and conventions.

**Work, in brief.**

- The agent collects, classifies, drafts the measure, and proves it. People approve loosening changes and the settings of the loop itself.
- Record cases in a fixed format, with no secrets or personal information.
- Group cases that share a cause into kinds. Leave one-off cases alone unless their impact is large; the threshold is a per-project value marked “needs confirmation”. Some causes are not handled by adding a rule: missing specification knowledge (add to the glossary or ADRs), an ambiguous or contradictory rule (rewrite it), a false positive or excessive constraint (a loosening change, so it goes to a person), and slow or flaky verification (fix the mechanism).
- Fix each kind at the highest level that works: design first, then types and lint or CI checks, then tests, then documents or rules. If structure fixes it, add no prose instruction.
- Fix one kind per change, most frequent first.
- Prove each measure: the new check fails on a real past mistake; examples of correct changes still pass; the same command runs locally and in CI; the effect on verification time is known.
- Update the rules-to-enforcement mapping.
- Apply changes according to the agreed scope of automatic changes. Nothing that loosens a guardrail is ever automatic, however many cases support it: deleting or disabling a rule, adding an exception, weakening types, deleting or skipping a test or changing its expected value, dropping a required check, or weakening a formally verified property or model.
- Measure the effect before and after. Withdraw or fix a change that does not work or has large side effects.
- Limit the loop itself: no rule from a single odd case; every rule has a triggering case, a date, and an enforcement; cap the volume of prose rules and the number of changes per cycle; take regular inventory of rules that never fire, overlap, or contradict; keep changes small and individually reversible; decide in advance what stops the loop.
- Track metrics: recurrence, where mistakes are caught, how much is enforced by structure, human burden, guardrail quality, rule health, and the loop itself. Changes made to improve the numbers (weakening tests, not recording failures) are forbidden and treated as loosening.

**Artifacts.** Records of failures and corrections, and the list of kinds; the written agreement on what may change automatically; the changes, their proofs, and approval records; the updated rules-to-enforcement mapping; evaluation cases; regular metric reports; inventory results.

**Done when.**

- The loop has gone around at least once, from collection to measuring the effect.
- Each adopted measure has evidence that its check fails on a past mistake.
- The mapping is current, and rules with no enforcement are listed.
- The automatic scope and the approval scope are written down.
- Stop conditions and a rollback procedure exist.
- Metrics are reported regularly.
- Rules are not only added: moves into structure, merges, and deletions have actually happened.

**After that.** Whether to widen the automatic scope, or to remove human review for more kinds of change, is decided by people from the metrics and the record. If the metrics get worse, narrow the scope.

## Principles that apply in every stage

The skill lists twelve cross-stage principles. In short:

1. Build in small units, each of which can be verified and reverted on its own.
2. Build in an order that proves something: the failing test before the fix, the deletion before the replacement, the baseline measurement before the change.
3. Keep behavior changes and refactoring in separate commits and separate units of verification.
4. Prove with artifacts from the real thing, not with proxies such as “it compiled”.
5. The author does not judge their own change. A different agent verifies it by running the real thing. Reading without running does not count as verification.
6. Replace incrementally (strangler pattern, branch by abstraction, a flag that switches between old and new, running both in parallel). Decide up front the condition for deleting the old path, and delete it once the migration is done.
7. Start where the risk is highest. Set priority by the impact of a failure, how often the code changes, and how poorly it is understood — not by how easy it is to tidy.
8. Treat what the AI read out of the code as a hypothesis until the decision-maker confirms it, and keep the two distinct in the records.
9. Keep one source of truth, under version control. Chat and calls are inboxes; an entry is confirmed once it has been written back.
10. Do not fill gaps with guesses. Record what is unknown and stop.
11. Confirm how to roll back before making a change. Irreversible operations need a human approval.
12. During long or unattended work, append one line per decision: time, stage, what was decided, why, a pointer to evidence, and the result.

## Policy document

Draft the policy document in stage 0 and update it as the stages progress. Write “needs confirmation” for anything unconfirmed; do not leave it blank.

- Japanese template (matches the skill): [`templates/policy-document.md`](../templates/policy-document.md)
- English translation: [`templates/policy-document.en.md`](../templates/policy-document.en.md)
