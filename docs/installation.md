# Installation

日本語: [インストール](ja/installation.md)

This repository is documentation plus one Agent Skill. The file an agent loads is [`skills/legacy-to-add/SKILL.md`](../skills/legacy-to-add/SKILL.md). To install the skill, copy its folder — not the whole repository — into the skills directory of the tool you use.

The skill body is written in Japanese. An agent working in English can usually still follow it (see the [FAQ](faq.md)); these pages are the English entry point for people.

## What to copy

```text
skills/legacy-to-add/
└── SKILL.md
```

Keep the folder name `legacy-to-add`. It is the skill’s `name` in the frontmatter, and the command many tools expose (for example `/legacy-to-add`).

## Get the repository

Every command on this page copies from a clone at `/tmp/legacy-to-add`:

```bash
git clone --depth 1 https://github.com/tkc310/legacy-to-add.git /tmp/legacy-to-add
```

`git clone` fails if that directory already exists and is not empty. Delete a leftover clone first, or clone somewhere else and adjust the paths below.

The `cp` commands below are for a first install. If the destination folder already exists, use the command under [Updating](#updating) instead; repeating the install command would put a second copy inside the existing folder.

## Claude Code

Claude Code loads skills from a personal directory or from a project directory. See the official page [Extend Claude with skills](https://code.claude.com/docs/en/skills).

### Personal install (every project on this machine)

```bash
mkdir -p ~/.claude/skills
cp -r /tmp/legacy-to-add/skills/legacy-to-add ~/.claude/skills/legacy-to-add
```

Personal skills are available in local Claude Code sessions on that machine. They are not automatically available in Claude Code cloud sessions or Cowork.

### Project install (one repository, shareable with the team)

From the root of the project where you want the skill:

```bash
mkdir -p .claude/skills
cp -r /tmp/legacy-to-add/skills/legacy-to-add .claude/skills/legacy-to-add
```

Commit `.claude/skills/legacy-to-add/` so teammates get the skill when they pull. Project skills load in sessions started in that repository.

### Confirm it loaded

1. Start a new Claude Code session in a project (or restart, if your build does not watch for new skills).
2. Type `/`. The command menu should list `legacy-to-add`. `/skills` lists skills with their descriptions.
3. Invoke the skill directly with `/legacy-to-add`.

If it does not appear, check three things: the file sits at `…/skills/legacy-to-add/SKILL.md`; the file starts with YAML frontmatter between `---` markers; and the frontmatter includes `name` and `description`.

## Cursor

Cursor loads skills from project and user directories. It also reads Claude Code and Codex skill directories for compatibility. See the official page [Agent Skills](https://cursor.com/docs/skills).

| Scope | Directories |
|---|---|
| Project (everyone who pulls the repo) | `.cursor/skills/`, `.agents/skills/` |
| User (this machine) | `~/.cursor/skills/`, `~/.agents/skills/` |
| Also loaded, for compatibility | `.claude/skills/`, `~/.claude/skills/`, and the Codex equivalents |

### Project install

From the root of the project where you want the skill:

```bash
mkdir -p .cursor/skills
cp -r /tmp/legacy-to-add/skills/legacy-to-add .cursor/skills/legacy-to-add
```

Commit the copied folder if the rest of the team should have the skill.

### User install

```bash
mkdir -p ~/.cursor/skills
cp -r /tmp/legacy-to-add/skills/legacy-to-add ~/.cursor/skills/legacy-to-add
```

User-level skills under `~/.cursor/skills/` stay on the machine until you sync them for Cloud Agents or publish them to your team. Cursor does not automatically copy unsynced local user skills into Cloud Agents.

### Confirm it loaded

Open **Customize** in the sidebar and go to **Skills**. You can also type `/legacy-to-add` in Agent chat.

### No plugin install

Cursor’s packaged route for installing skills from GitHub is a plugin that ships a `.cursor-plugin/marketplace.json`. This repository does not include that file, so the supported install path is to copy the skill folder as shown above.

## Other tools that implement Agent Skills

The skill follows the usual layout: a directory named after the skill, containing a `SKILL.md` with YAML frontmatter (`name` and `description`) and a Markdown body. A tool that implements the [Agent Skills](https://agentskills.io/specification) layout can load it the same way: place `legacy-to-add/SKILL.md` in that tool’s skills directory. Check the tool’s documentation for the exact path.

## Updating

To replace an installed copy with a newer revision of this repository, clone again (see [Get the repository](#get-the-repository)) and copy the folder’s contents over the installed folder:

```bash
cp -r /tmp/legacy-to-add/skills/legacy-to-add/. /path/to/skills/legacy-to-add/
```

Replace `/path/to/skills/legacy-to-add/` with the install path you chose earlier (`~/.claude/skills/legacy-to-add`, `.cursor/skills/legacy-to-add`, and so on). Then start a new session, or confirm that the running session has picked up the change.

An installed copy does not follow this repository on its own, and edits made only to that copy do not flow back. If you change the process, change it here, or record the difference in the target project’s policy document.
