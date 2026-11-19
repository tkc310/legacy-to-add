<!--
  English translation of templates/policy-document.md.
  The Japanese template matches the skill text. If translations drift,
  treat the Japanese template and skills/legacy-to-ai-ready/SKILL.md as
  the source of truth.
-->

# Delivery policy

- Version: (date of this update)
- Author: (role name)  Approver: (role name)

## 1. Scope

- System and scope in scope:
- Out of scope:
- Goal (what “safe for AI-assisted development” means on this project):
- Constraints (deadlines, contracts, whether code may be sent to external AI services, places that must not be touched):

## 2. Diagnosis

| Aspect | Current state | How it was checked (ran / read / asked) | Evidence location | Status (confirmed / needs confirmation) |
|---|---|---|---|---|
| Size and structure | | | | |
| Tests | | | | |
| Types | | | | |
| Build and deploy | | | | |
| Means to exercise the system | | | | |
| People who hold domain knowledge | | | | |
| Operational situation | | | | |
| Recurring mistakes | | | | |
| Existing documents | | | | |

## 3. Priorities

- Area to start with, and why (impact, change frequency, how well it is understood):
- What is deferred, and why:
- Current stage:

## 4. Plan per stage

| Stage | Area | Work | Artifacts | What stands in for human review | Judgments that remain human | Done criteria | Gate to the next stage | Status |
|---|---|---|---|---|---|---|---|---|
| 1. Recover the specification | | | | | | | | |
| 2. Pin and fix | | | | | | | | |
| 3. Guardrails | | | | | | | | |
| 4. Done criteria and operations | | | | | | | | |
| 5. Improvement loop | | | | | | | | |

## 5. Means of assurance

- How automated checks (tests, lint, types) are run (the same commands locally and in CI):
- Location of the verification skill, and the list of features it covers:
- Formal-verification targets, tools, scope and limits (or the reason for skipping):
- Location of the rules-to-enforcement mapping:
- Where evidence and decision records are stored:

## 6. What may be left to AI

- Work that may be left to an agent:
- Kinds of change where human review has been removed, and the basis (means of assurance, and a record that they actually caught mistakes):
- Work that still requires a human judgment:
- Agent permissions:

## 7. Risks

| Risk | Impact | Signs | Response | Owner (role name) |
|---|---|---|---|---|

## 8. People who own judgments

| Role | Owner (role name) | What they decide |
|---|---|---|
| Specification decision-maker | | Confirming hypotheses, judging “as specified or a bug”, deciding the To-Be |
| Consulted expert | | History and background |
| Change-acceptance decision | | Behavior-changing changes; changes that touch data or permissions |
| Release approval | | Production rollout; data changes |
| Guardrail-change approval | | Relaxations, exceptions, rules, permissions, and the improvement-loop settings |

## 9. Open questions and deferred items

- Open questions (to go on the next questionnaire):
- Deferred items (and when to revisit them):

## 10. Change history

- (date) (what changed, and why)
