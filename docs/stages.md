# Stages

This page summarizes stages 0 to 5 of [`skills/legacy-to-ai-ready/SKILL.md`](../skills/legacy-to-ai-ready/SKILL.md). The skill is the source of truth for procedure, checklists, and wording. Numbers such as coverage targets or “how many questions per questionnaire” are not fixed by the skill; set a temporary value per project, mark it as needing confirmation, and revise it after you measure.

日本語の説明は [stages（日本語）](ja/stages.md) を見てください。

## How the stages relate

```text
0 Diagnose and write the policy
        ↓  (policy priorities are approved; build and run are reproducible)
1 Recover the specification   ←→  2 Pin behavior and fix
        ↓  (stages 1–2 have covered the planned areas; tests are stable)
3 Build guardrails
        ↓  (a small trial: mistakes are caught, evidence is produced, the agent stops for human judgments)
4 Confirm done criteria and operate
        ↓  (the same kind of mistake is recurring)
5 Improvement loop (encode recurring mistakes into structure;
   adjust the guardrails and harness)
```

Stages are not a strict sequence. On a large project, cycle stages 1 and 2 per area, then move to stage 3 when those areas are covered. Minimal automation (for example, running tests in CI) may be added during stage 2. Strict, repo-wide lint or type rules wait until stage 3, so a wave of mechanical changes does not mix with judgment and fixes.

## What stands in for human review

Safety is not guaranteed by having a person read every diff. Agents produce changes faster than that can keep up. The skill uses five kinds of assurance instead:

1. **Make recurring mistakes impossible in the structure**, in this order: design and data structures; types, then lint or CI; tests; documents and agent rules (last, because an unread rule fails at nothing). Human review is not a step in this order.
2. **Exercise the product the way a user would**, and keep evidence, as a project-local verification skill.
3. **Formally verify the highest-risk properties** whose tests are hard to make complete, with the scope written down.
4. **Stack work in small, verifiable units.** Each unit ends in a check. Do not move on until it passes.
5. **Prove with artifacts.** “It compiled” is not evidence. Show commands and output, tests that failed and then passed, recordings of the running system.

This has limits. Checks can tell you whether the system matches what you decided. They cannot decide what is correct. In a legacy project that missing decision is the starting point, so judging the specification and approving the policy stay with people. In stages 0 to 2 the structural guarantees are still thin, so more human confirmation remains. Remove human review only for kinds of change whose checks have already been shown to catch real mistakes.

## Stage 0: Diagnose the current state

**Purpose.** Decide how much effort each later stage should get, and write the first policy document.

**Inputs.** The repository; build and deploy setup; tickets and documents if they exist; interviews with the requester.

**Work, in brief.** Investigate size and structure, tests, types, build and deploy, means of exercising the system, who holds domain knowledge and for how long, operations, recurring mistakes, and existing documents. Record what you ran, what you only read, and what you were told. Keep commands and output for anything you ran. Adjust the order of work to the situation (for example, interview first if the expert is about to leave; write a rollback path before behavior-changing changes if deploy is manual and there is no way back). Draft the policy document and ask the requester to approve it.

**Artifacts.** Diagnosis record (including commands and output); first policy document.

**Done when.** Each aspect is marked confirmed or unconfirmed; priorities and reasons are written; people who own judgments are named by role.

**Gate.** The requester has agreed to the priorities in the policy document. If build and run cannot be reproduced, fix that first.

## Stage 1: Recover the specification

**Purpose.** Recover context that is not in the code, and keep terms and decisions in a source of truth. This is what later stages use to judge “as specified or a bug”.

**Inputs.** Code, history, review records, tickets, existing documents, permitted past conversations, and the stage-0 diagnosis.

**Work, in brief.** List decision-makers and experts by role and area. Dig in the code and history before asking; do not ask what you can find out. Classify questions, and separate As-Is (confirm a hypothesis about current behavior) from To-Be (ask the decision-maker what should be true). Send bundled, multiple-choice questionnaires that include a hypothesis, evidence, options, and a “I don’t know (who does?)” way out. Write answers back into the glossary and ADRs. Confirmation by the respondent or decision-maker is what makes an entry confirmed. Keep hypotheses and confirmed facts visibly distinct.

**Artifacts.** Role list; glossary; ADRs; questionnaires and answers; deferred list; list of open questions.

**Done when, for the area in scope.** Every blocking question is confirmed, or the decision-maker has explicitly deferred it; main terms are in the glossary as confirmed; remaining hypotheses are listed.

**Gate.** The area has enough confirmed terms and decisions to judge whether behavior is correct. Do not wait for the whole system. Questions that appear in stage 2 go back to a stage-1 questionnaire.

## Stage 2: Pin behavior and fix toward the intended shape

**Purpose.** Pin what the system does today, have a decision-maker judge each behavior, and fix what is a bug — without turning an unverified guess into a rewrite.

**Work, in brief.** Start from high-risk paths and paths you already plan to change. Write characterization tests that lock current behavior *before* fixing, at observable boundaries, without tying them to internal call order. Mark them as “pins the current behavior; correctness is not yet judged.” For each pinned behavior, the decision-maker judges: as specified; a bug; not the specification but depended upon; or unknown. Record the judgment in a ledger. For bugs, write the failing test for the intended behavior first, then the fix, as separate commits. Do not mix behavior changes with refactoring. Keep fixed bugs as cases that later checks must fail on.

**Artifacts.** Characterization tests and specification tests; judgment ledger; fix and refactoring changes; updated glossary and ADRs; bug cases for later proofs; a list of formal-verification candidates.

**Done when, for the area in scope.** High-risk paths are protected by tests; every pinned behavior has a judgment (including “unknown”, listed); bugs are fixed or have a scheduled response; remaining “unguarded / not yet judged” marks are known.

**Gate.** Stages 1 and 2 have covered the planned areas, and tests are stable (no flaky tests left). Then move to stage 3.

## Stage 3: Build guardrails

**Purpose.** Once behavior is pinned and the intended shape is in place across the planned areas, put repo-wide guardrails in place so recurring mistakes cannot land, and so an agent can check work the way a user would.

**Work, in brief.** Add formatter, linter, and types gradually (often “deny new violations first”). Expand tests around risk and past bugs. Add a verification skill that drives the product as a user would and stores evidence. Consider formal verification for high-harm, hard-to-test properties, and write down the scope — or write down why it was skipped. Move mechanically checkable conventions out of prose and into lint, types, or tests. Write short agent rule files that point at those sources of truth. Keep a mapping of each rule to what enforces it (design, types, lint, tests, or nothing). Make checks run automatically, with the same commands locally and in CI. Prove each new check by showing that it fails on a past mistake or on a deliberate broken change (then discard that change).

**Artifacts.** Formatter, linter, and type settings; expanded tests; verification skill and a feature list; formal verification and its scope, if chosen; naming and coding conventions; agent rules and the rules-to-enforcement mapping; automated checks; a list of existing violations and a plan to reduce them.

**Done when.** Checks run automatically and a failing change cannot be merged; local and CI commands match; conventions and rule files have a single source of truth; existing violations and exceptions are known, and exceptions have a reason, a deadline, and an approval; each check has been shown to catch a real mistake; the verification skill has been run end to end at least once, with evidence; every formal-verification candidate was either done or explicitly declined, with a reason.

**Gate.** Give an agent a small real task that depends only on the rule files and the verification skill. Move to stage 4 when (1) mistakes are stopped by the guardrails, (2) the agent can exercise the product and produce evidence, and (3) the agent stops when a human judgment is required.

## Stage 4: Confirm done criteria and operations

**Purpose.** Confirm that you can honestly say “AI-assisted development can run safely here,” and define the operating loop so verification does not stall on a person.

**Work, in brief.** Specialize the skill’s done-criteria checklist to the project (reproducible build and run; high-risk paths tested and stable; automatic checks that block merges; a verification skill; formal verification done or declined; glossary and ADRs with hypotheses and confirmed facts kept distinct; a judgment ledger; agent rules that name work requiring a human; the rules-to-enforcement mapping; limited agent permissions; a tried rollback path; named decision owners; trial AI work in which mistakes were caught before merge). Define the AI-work flow: a request with executable done criteria; a plan in verifiable units; implementation unit by unit; automatic checks; exercising the real system in a way that matches the kind of change; independent verification by a *different* agent than the author; hand-off of work that needs a human; then merge. Widen the scope of work left to agents only after checks have been shown to catch mistakes for that kind of change. Keep a record of AI failures, human corrections, CI failures, and reverts — this is the input to stage 5.

**Artifacts.** Specialized done criteria and the result of checking them; a document for the AI-work flow; a place to record failures and corrections; results of reviewing the verification skill.

**Done when.** Every checklist item is met, or is an approved exception; real work has gone through the agreed flow more than once (the suggested count is left as “needs confirmation”).

**Gate.** Start stage 5 only when the same kind of mistake is recurring in the failure record, or when stage 0 already found recurring mistakes you can use as the first input. Do not start stage 5 from one-off stories; that produces too many rules.

## Stage 5: Improvement loop

**Purpose.** When the same kind of mistake keeps appearing, encode it into the highest-leverage structure that prevents it, and keep adjusting the guardrails and harness. Do not add a new prose rule every time something fails.

**Work, in brief.** Collect and classify failures and repeated human corrections. Propose a fix at the highest level that works (design first, then types or lint or CI, then tests, then documents or rules). Prove that the new check fails on past cases of that mistake. Loosen nothing automatically: deletions, disabled checks, weaker types, skipped tests, or weaker formal properties always need a human. Put limits on the loop itself (do not invent a rule from a single odd case; every rule needs a triggering case and an enforcement; cap the volume of prose rules and the number of changes per cycle; inventory unused or contradictory rules; keep changes small and reversible). Track metrics such as recurrence, where mistakes are caught, how much is encoded in structure, human burden, false positives, and the health of the loop — but do not game the metrics.

**Artifacts.** Failure and correction records and a catalog of kinds; a written agreement of what may change automatically; the changes, their proofs, and approval records; an updated rules-to-enforcement mapping; evaluation cases; regular metric reports; inventory results.

**Done when.** The loop has gone around at least once from collection to measuring effect; each adopted measure has evidence that the check fails on a past mistake; the mapping is current and rules with no enforcement are listed; automatic scope and approval scope are written down; stop conditions and a rollback path exist; metrics are reported regularly; the work includes migrating rules into structure, merging them, and deleting them — not only adding them.

**After that.** Whether to widen automatic application, or to remove human review for more kinds of change, is a human decision based on the metrics and the record. If the metrics get worse, narrow the scope.

## Principles that apply in every stage

The skill lists twelve cross-stage principles. The short version:

1. Stack work in small units that can be checked and reverted on their own.
2. Stack in an order that proves something (failing test before the fix; deletion before the replacement; baseline measurement before the change).
3. Do not mix behavior changes and refactoring in the same unit.
4. Prove with artifacts from a running system, not proxy signals such as “it compiled.”
5. The author of a change does not accept that change. A different agent exercises the real system. Reading a diff without running it does not count as verification.
6. Replace incrementally (strangler, branch by abstraction, feature flags, running old and new in parallel). Decide up front when the old path will be deleted, and delete it when the migration is done.
7. Start from high-risk, frequently changed, or poorly understood areas — not from the places that are easiest to tidy.
8. Keep hypotheses and confirmed facts distinct until a decision-maker confirms them.
9. Keep one source of truth, under version control. Chat and calls are inboxes; an entry is confirmed when it has been written back.
10. Do not fill gaps with guesses. Record what is unknown and stop.
11. Keep a way back before you change behavior. Irreversible operations need a human.
12. During long or unattended work, append a one-line decision record with time, stage, decision, reason, and a pointer to evidence.

## Policy document

Draft the policy in stage 0 and update it as you go. Unconfirmed items are written as “needs confirmation,” not left blank.

- Japanese template (matches the skill): [`templates/policy-document.md`](../templates/policy-document.md)
- English translation: [`templates/policy-document.en.md`](../templates/policy-document.en.md)
