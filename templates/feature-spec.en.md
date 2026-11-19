<!--
  English translation of templates/feature-spec.md. One file per feature.
  If this translation drifts, treat the Japanese template and
  skills/legacy-to-add/SKILL.md as the source of truth.
-->

# (Feature name: a glossary term)

- ID: (the ID in the feature map)
- Area:
- Status: (hypothesis / confirmed / retired)
- Decided by: (role name)  Decided on:
- Last exercised (date and commit): (or “not checked”)

Write the current behavior that was checked (As-Is). When the intended behavior (To-Be) is decided to differ, record the decision in an ADR and list the gap under “Open gaps” below. Each business rule and each exception carries its own status and evidence. Do not write an item that has no evidence.

## Purpose

(Whom this feature serves, and what for)

## Users

(Role names. Note differences by permission, if any)

## How to reach it

(Screen, menu, URL, command, and so on. Keep it consistent with the feature map)

## Operation

1. (What the user does, in order)

## Observable results

(What must be visible to say that it works: what the screen shows, responses, stored values, notifications sent)

| Operation | Observable result | Evidence |
|---|---|---|
| | | |

## Business rules

| No. | Rule | Status (hypothesis / confirmed) | Evidence (code location, commit, ticket, result of a run, answer log) |
|---|---|---|---|
| R1 | | | |

## Exceptions

| No. | Condition | Behavior | Status (hypothesis / confirmed) | Evidence |
|---|---|---|---|---|
| E1 | | | | |

## States and transitions

For a simple feature, use the table below. For a complex one, keep a state machine or statechart model (XState, for example) in the source of truth and refer to it from here. A diagram is a copy of the model.

| Current state | Event | Condition | Next state | Status (hypothesis / confirmed) | Evidence |
|---|---|---|---|---|---|
| | | | | | |

- Location of the model or diagram: (if any)

## Open gaps (As-Is versus To-Be)

| Current behavior | Intended behavior | Decision (ADR) | Progress |
|---|---|---|---|
| | | | |

## Related

- Terms:
- ADRs:
- Code locations:
- Tests:
- Verification evidence:

## Open questions

- (to go on the next questionnaire)

## Change history

- (date) (what changed, and why)
