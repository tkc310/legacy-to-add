# Answer collection

日本語: [回答の集め方](ja/answer-collection.md)

In stage 1 the agent sends a questionnaire and writes the answers back into the source of truth. This page covers how the questionnaire is sent and how the answers come back: the methods the agent proposes, the concerns it states, and the build guide for one of them — GitHub as the database, Slack as the input, and a Claude artifact as the display.

The procedure itself is in [`skills/legacy-to-add/SKILL.md`](../skills/legacy-to-add/SKILL.md), under 「回答を集める方法の提案」. The skill is the source of truth; this page adds detail.

What this page says about Claude artifacts comes from two Claude Help Center articles retrieved on 2026-11-19 (see [Sources](#sources)). Details of Slack, GitHub, and the Claude interface were not verified for this page. They are marked “needs confirmation” (`要確認` in the skill text): confirm them in the current official documentation before applying them.

## What does not change

Whichever method is chosen, these stay as the skill states them:

- **Two conditions.** There is one source of truth, and it keeps a history of changes.
- **The lean-questionnaire rules** of stage 1:
  - One thing per question.
  - Filter before sending. Only blocking or high-priority questions go out.
  - The respondent sees a short summary, a short question, a one-line hypothesis, and a few options plus “I don’t know”.
  - Each question can be answered in about ten seconds.
  - Evidence and classification stay in the agent-side ledger, or are folded away.
- **Write-back.** An answer received through an input channel becomes confirmed only when it has been written back to the source of truth and the respondent or decision-maker has confirmed the change.

## The agent proposes; the user chooses

Before the first questionnaire goes out, the agent proposes the methods below with a recommendation, and waits. It does not send or build anything until the user has chosen. The chosen method and the location of the source of truth go into the policy document.

| Method | Input | Source of truth | Display | What the respondent needs | Fits when |
|---|---|---|---|---|---|
| A. Documents in the repository only | The questionnaire is sent as a document; the agent or the user writes the answers back | The repository | The documents in the repository | A way to receive the questionnaire | There are few respondents, or no time to build anything |
| B. GitHub Issue forms | Issues (form features and syntax need confirmation) | The repository; answers in issues are written back | GitHub | A GitHub account | The respondents already use GitHub |
| C. Static site | Decided separately (A or B, for example) | The repository | A site generated from the repository | Depends on the input method | The glossary should be easy to read |
| D. GitHub + Slack + Claude artifact | Slack | A GitHub repository | A Claude artifact | A Slack account only | The respondents use Slack every day and have no GitHub or Claude account |

Another method that fits the project can be added, as long as it meets the two conditions.

## Method D: GitHub, Slack, and a Claude artifact

```text
agent ──posts questions──▶ Slack ──button or reply──▶ write-back path ──commit──▶ GitHub (source of truth)
                                                                                      │
respondents ◀──────────── view only ◀──────────── Claude artifact ◀── generated from ─┘
```

- **GitHub is the single source of truth.** The glossary (one term per file), ADRs, questionnaires, and an answer log live in a repository with history. This can be the target project’s own repository or a dedicated documentation repository. Name the place in the policy document.
- **Slack is the input.** The agent posts questions in a channel or a DM, one question per message, with buttons for the options and for “I don’t know”. Answers go through a Slack app (Block Kit interactivity) or a workflow to something that writes to GitHub: a small bot, a GitHub Action started by `repository_dispatch`, or the agent reading the replies.
- **The Claude artifact is display only.** It shows the glossary and the status of each question (unanswered, answered, written back). It is generated from the contents of the repository and regenerated when the source changes.
- **Respondents need neither a GitHub account nor a Claude account,** provided that the display content is in the artifact itself and the artifact is shared as “Anyone with the link”. Check concerns 2 and 4 below first.

## Concerns to state when proposing method D

The agent states all of these when it proposes method D, not after the user has chosen it.

### From the Claude Help Center (retrieved 2026-11-19)

1. **The artifact must not be the input or the database.** Data in an artifact’s shared storage can be read by people outside the creator’s organization once they sign in to Claude, but they cannot change it. On Pro, Max, and Ultra plans, “outside your organization” means anyone other than the creator ([Share artifacts](#sources)). Respondents could not write to it, so input goes through Slack and the database is GitHub.
2. **Put the display content in the artifact itself, not in storage.** People who are not signed in cannot see stored data ([Share artifacts](#sources)). With the content in the page itself, respondents can view an artifact shared as “Anyone with the link” without a Claude account. Most artifacts shared this way open without signing in, but some show part or all of their content only after sign-in, so build step 7 checks this on the real artifact. Three related points from the same articles:
   - Connected apps are per person: everyone connects their own apps, even on a shared artifact ([What are artifacts](#sources)). Do not make the display depend on a connector.
   - Docs can be viewed only by signed-in visitors, so a Doc does not fit respondents without Claude accounts.
   - On Team and Enterprise plans, if an owner turns off External sharing, “Anyone with the link” links stop working until it is turned back on, unless an owner has allowed that artifact individually.
3. **Do not keep anything you cannot lose in artifact storage.** For a legacy artifact (made in a chat before 16 September 2026) that has been published, unpublishing permanently deletes its stored data, and that artifact cannot be published again; to share it later you create a new artifact ([What are artifacts](#sources), [Share artifacts](#sources)). This is a rule for legacy artifacts, not for all current ones. Current artifacts use “Share”, and stopping sharing is documented separately; that documentation does not say that stored data is deleted or that the artifact cannot be shared again. New work should assume current artifacts. Anyone still using a published legacy artifact should know the risk.
4. **Anyone with the link can open it.** An artifact shared as “Anyone with the link” can be opened by anyone who has the link, including people outside the organization and, for most such artifacts, people who are not signed in ([Share artifacts](#sources)). Before putting terms or answers on the display, check whether client-confidential wording may be shown to that audience at all. If it may not, do not use an “Anyone with the link” artifact. Use a display with view permissions instead, such as a Notion page with view permissions or a private static site.

### Design requirement of this skill

5. **The display is a copy of the source.** The artifact is generated from the GitHub source. Regenerate or republish it after each source update, and show the source commit or date on it. This is how this design works; it is not a statement from the Help Center.

### Contract, project, and Slack concerns

These are not Help Center statements either.

6. **Customer data ends up in the user’s Claude and Slack.** Before proposing or building this, check the contract and the first of the skill’s three confirmations (sending material to an external AI service). Without consent, do not use this method.
7. **Installing a Slack app in the client’s workspace may need admin approval.** Confirm this in that workspace. If the app is not approved, fall back to Workflow Builder, or to the agent reading thread replies.

## Build guide for method D

When the user picks method D, the agent goes through the steps below one at a time.

Ground rules:

- For each step, the agent separates what it produces (files, draft settings) from what the user does on screen (creating the app, issuing tokens, setting sharing). It confirms that the user’s part is done before moving on.
- The agent does not write Slack, GitHub, or Claude details from memory: scope names, API details, setting names, and plan conditions are confirmed in the current official documentation first. Anything unconfirmed is marked “needs confirmation”.
- Code or settings that have not been run are not handed over as if they had been tested.
- Tokens are never pasted into chat or written into the repository.

### Step 1: Confirm the preconditions

- Record the results for concerns 4, 6, and 7 in the policy document.
- If confidential wording may not be shown to anyone with the link, choose a different display before building anything.
- If a Slack app will not be approved, plan for one of the alternatives in step 4.

**Check.** The policy document states the chosen method, the location of the source of truth, and the result of each confirmation.

### Step 2: Create the place for the source of truth

Create the structure in the repository: a directory in the target project, or a dedicated documentation repository. One possible layout, extending the [suggested layout](usage.md#suggested-layout-in-the-target-project):

```text
docs/ai-ready/
  glossary/              # one term per file
  adr/
  questionnaires/        # one file per questionnaire
  answers/               # the answer log, append-only
```

The path names are an example. The skill does not prescribe them.

**Check.** The location is written in the policy document, and the directory is committed.

### Step 3: Define the answer format

The answer log is append-only. Each answer records:

- the questionnaire and question identifiers;
- the chosen option, including “I don’t know” and the name of the person who would know;
- the respondent, by role name;
- the date and time of the answer;
- a pointer to the Slack message;
- the status: not yet written back, or written back.

An example of one questionnaire’s log (the format is the project’s choice):

```markdown
| Question | Answer | Respondent (role) | Answered at | Slack message | Status |
|---|---|---|---|---|---|
| Q1 | Yes | (role name) | (date and time) | (link) | Written back |
| Q2 | I don't know (ask: ...) | (role name) | (date and time) | (link) | Not yet written back |
```

Make each Slack button value correspond one-to-one to a question identifier and an option, so that an answer can be recorded without interpretation.

**Check.** The format is documented next to the answer log, and one hand-written sample entry passes whatever format check the project uses.

### Step 4: Set up the Slack input

Choose one:

- **A Slack app.** The app posts messages with buttons and receives the button actions at a request URL. Keep the scopes to the minimum the app needs. Scope names, the setup steps, and what the receiving endpoint must do need confirmation in the Slack documentation.
- **Workflow Builder.** The alternative when an app cannot be installed. What it can do here, and under which plans, needs confirmation.
- **No app.** The agent, or the user, posts the questions, and respondents reply in the thread with the number of an option. The agent reads the replies. How the agent gets access to Slack depends on the environment and needs confirmation.

Get the user’s approval for where the questions are posted and under whose name.

What the respondent sees for one question:

```text
Q1  (short question about current behavior)
Hypothesis: (one line)
[ Yes ]  [ No ]  [ I don't know ]
```

Someone who chooses “No” or “Other” adds a word or two in the thread. Someone who chooses “I don’t know” names the person who would know.

**Check.** A test message with buttons appears in the agreed place, and the user has confirmed how the app was approved.

### Step 5: Set up the write-back path

Connect the input from step 4 to GitHub with one of: a small bot, a GitHub Action started by `repository_dispatch`, or the agent reading replies and committing. The event format and the permissions required need confirmation in the GitHub and Slack documentation.

- Keep tokens in a secret store. Limit their permissions to writing to the one repository.
- The path only appends to the answer log. Changes to the glossary and ADRs are made as the skill’s write-back step says: proposed as a change, and confirmed by the respondent or the decision-maker.
- Record who pressed the button in the answer log. If that person does not hold the role the question was addressed to, do not treat the answer as confirmed, and tell the user. How to map a Slack account to a role needs confirmation.

**Check.** A button press produces exactly one new entry in the answer log, as a commit.

### Step 6: Generate the artifact

Generate an artifact from the repository that shows the glossary and the status of each question. How to create and share an artifact depends on the Claude interface in use and needs confirmation there.

- Put the content in the artifact itself. Do not use storage, connectors, or input fields.
- Keep hypotheses and confirmed entries visibly distinct, as the skill requires for any display.
- Show the source commit or date.
- Set sharing according to the result for concern 4.

**Check.** The artifact shows the source commit, and its content matches the repository at that commit.

### Step 7: Test end to end with one question

Post one test question and check, in order:

1. Pressing a button appends one entry to the answer log, and the commit is there.
2. After regenerating the artifact, that answer appears in the status, and the source commit shown matches.
3. The link opens, and the content is visible, in a browser that is not signed in to Claude.
4. A respondent can answer and view without a GitHub or Claude account.

Keep the evidence: the Slack message, the commit, and a recording of the screen. Mark the test answer as a test. Until every check passes, do not send a real questionnaire.

### Step 8: Operate

For each questionnaire:

1. Post the summary as one message, then the questions, one per message, as one batch.
2. When answers arrive, append them to the answer log.
3. Write them back into the glossary and ADRs, and have the change confirmed.
4. Regenerate the artifact, and check the source commit it shows. Whether an updated artifact keeps the same link needs confirmation; if the link changes, send the new one.

Do not leave an answer in Slack without writing it back.

## Sources

Claude Help Center, retrieved 2026-11-19:

- What are artifacts and how do I use them? — https://support.claude.com/en/articles/17153992-what-are-artifacts-and-how-do-i-use-them
- Share artifacts — https://support.claude.com/en/articles/9547008-share-artifacts

The articles may change. Before building, read the current versions.
