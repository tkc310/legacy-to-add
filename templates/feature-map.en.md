<!--
  English translation of templates/feature-map.md.
  If this translation drifts, treat the Japanese template and
  skills/legacy-to-add/SKILL.md as the source of truth.
-->

# Feature map

A document that maps every feature of the app from the user’s point of view. People and agents use it to call features and screens by the same names. The stage-3 verification skill uses this map as its feature list.

- Version: (date of this update)
- Target app and scope:
- Location of the glossary:
- Location of the feature specifications:

## Writing rules

- Use glossary terms for area names, feature names, and screen names. Do not write a name that is not in the glossary; register it as a hypothesis term first.
- IDs never change. To rename something, change the glossary first, then this map and the specifications in the same change.
- A row whose status is “hypothesis” must have evidence. It becomes “confirmed” only after the decision-maker confirms it.
- Steps to operate a feature and its observable results go in the feature specification, not here.
- Write “not checked” for a feature that has not been exercised.

## Areas

| Area (glossary term) | What it is (one line) | Decision-maker (role name) | Consulted expert (role name) |
|---|---|---|---|
| | | | |

## Features

The table may be split per area.

| ID | Feature name (glossary term) | Area | What it is (one line) | Users (role names) | How to reach it (screen, menu, URL, command) | Specification | Status (hypothesis / confirmed / retired) | Evidence | Last exercised (date and commit) |
|---|---|---|---|---|---|---|---|---|---|
| (e.g. F-001) | | | | | | (link, or “not written”) | | | (or “not checked”) |

## Screens and entry points (if needed)

Fill this in when one screen holds several features, or one feature has several entry points.

| Screen or entry point (glossary term) | How to reach it | Features it contains (IDs) |
|---|---|---|
| | | |

## Change history

- (date) (what changed, and why)
