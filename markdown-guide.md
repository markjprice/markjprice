# Markdown Guide for AI Instruction Files

A practical guide to the Markdown you need for writing **SKILL.md** files (Agent Skills), **AGENTS.md** files (instructions for coding assistants), and **BRIEF.md** files (product briefs for coding assistants).

Markdown is plain text with light punctuation that adds structure. People can read it as it is, and it renders neatly in editors and on GitHub. AI models read it well too. When you write for an AI, the structure carries meaning: a heading marks a topic, a numbered list is a sequence of steps, and a code block holds text to copy exactly.

---

## Contents

1. [Headings](#1-headings)
2. [Paragraphs and line breaks](#2-paragraphs-and-line-breaks)
3. [Emphasis](#3-emphasis)
4. [Lists](#4-lists)
5. [Task lists](#5-task-lists)
6. [Links](#6-links)
7. [Images](#7-images)
8. [Inline code](#8-inline-code)
9. [Code blocks](#9-code-blocks)
10. [Blockquotes](#10-blockquotes)
11. [Horizontal rules](#11-horizontal-rules)
12. [Tables](#12-tables)
13. [YAML frontmatter](#13-yaml-frontmatter)
14. [Escaping special characters](#14-escaping-special-characters)
15. [Comments](#15-comments)
16. [Collapsible sections](#16-collapsible-sections)
17. [Footnotes](#17-footnotes)
18. [Worked example: SKILL.md](#18-worked-example-skillmd)
19. [Worked example: AGENTS.md](#19-worked-example-agentsmd)
20. [Worked example: BRIEF.md](#20-worked-example-briefmd)
21. [Style rules for AI-readable Markdown](#21-style-rules-for-ai-readable-markdown)
22. [Quick reference card](#22-quick-reference-card)

---

## 1. Headings

Start a line with one to six `#` characters, then a space.

```markdown
# Heading level 1
## Heading level 2
### Heading level 3
#### Heading level 4
##### Heading level 5
###### Heading level 6
```

**Rules**

- Always put a space after the `#`. `#Heading` is not a heading in most renderers.
- Use one `#` heading per file, as the title.
- Don't skip levels (`##` straight to `####`). AI models and people both read the levels as an outline.
- Leave a blank line before and after each heading.

**Why it matters for AI files:** headings divide instructions into named sections such as `## Setup`, `## Testing` and `## Do not`. An assistant can then find the rule that applies to the job in front of it.

---

## 2. Paragraphs and line breaks

A blank line separates paragraphs. A single line break in the source is usually joined into the same paragraph.

```markdown
This is the first paragraph.
This line joins the first paragraph when rendered.

This is a second paragraph.
```

To force a line break without starting a new paragraph, end the line with a backslash or with two spaces:

```markdown
Line one\
Line two
```

> **Tip:** Trailing spaces are invisible and editors often delete them. Use the backslash, or just start a new paragraph.

---

## 3. Emphasis

| You type | You get |
|---|---|
| `*italic*` or `_italic_` | *italic* |
| `**bold**` or `__bold__` | **bold** |
| `***bold italic***` | ***bold italic*** |
| `~~strikethrough~~` | ~~strikethrough~~ |

```markdown
Run the tests **before** every commit.
Use *British* spelling in user-facing text.
~~Use Jest~~ We now use Vitest.
```

**For AI files:** use bold sparingly, for words that change what the assistant does: **never**, **always**, **must**. If everything is bold, nothing stands out. Writing rules in CAPITALS ("NEVER DO THIS") seldom helps. A plain explanation of *why* the rule exists usually works better.

---

## 4. Lists

### Unordered (bulleted) lists

Start each item with `-`, `*` or `+` and a space. Pick one marker and stick with it. `-` is the most common.

```markdown
- Python 3.12
- PostgreSQL 16
- Redis
```

### Ordered (numbered) lists

Start each item with a number, a full stop and a space.

```markdown
1. Read the input file.
2. Validate each row.
3. Write the report.
```

The numbers don't have to be in order. Most renderers count for you, so `1.` on every line still gives 1, 2, 3. Writing the real numbers makes the source easier to read.

### Nested lists

Indent child items with spaces: two or three for a bullet parent, three or four for a numbered parent. Line the child up with the parent's text.

```markdown
1. Set up the project
   - Install dependencies
   - Copy `.env.example` to `.env`
2. Run the app
   - Development: `npm run dev`
   - Production: `npm run build && npm start`
```

### Paragraphs and code inside list items

Indent continuation content to line up with the item's text.

````markdown
1. Install the package:

   ```bash
   pip install requests
   ```

2. Import it in your script.
````

**For AI files:** use a **numbered list** when order matters (a workflow, a procedure). Use **bullets** when it doesn't (a set of rules or facts). The assistant treats a numbered list as a sequence to follow.

---

## 5. Task lists

A task list is a bullet list with `[ ]` (open) or `[x]` (done). It's a GitHub Flavored Markdown (GFM) extension, but almost every modern tool supports it.

```markdown
- [x] Create database schema
- [x] Build login page
- [ ] Add password reset
- [ ] Write end-to-end tests
```

**For AI files:** task lists suit acceptance criteria in a BRIEF.md and checklists in a SKILL.md ("Before finishing, confirm..."). You can ask a coding assistant to tick items as it completes them.

---

## 6. Links

### Inline links

```markdown
See the [project README](README.md).
Read the [Markdown spec](https://spec.commonmark.org/).
```

### Links with a title (shown on hover)

```markdown
[CommonMark](https://commonmark.org "The CommonMark standard")
```

### Reference-style links

These keep long URLs out of the text.

```markdown
Follow the [style guide][style] and the [API docs][api].

[style]: docs/style-guide.md
[api]: https://example.com/api
```

### Automatic links

```markdown
<https://example.com>
```

### Links to headings in the same file

Headings become anchors: lower case, spaces turned into hyphens, most punctuation removed.

```markdown
See [Testing](#testing) below.
```

**For AI files:** relative links such as `[form guide](references/forms.md)` are how a SKILL.md points to its supporting files. The assistant opens the linked file only when it needs it. Keep the paths relative to the file doing the linking.

---

## 7. Images

An image is a link with `!` in front. The text in square brackets is the alt text.

```markdown
![Architecture diagram](docs/architecture.png)
![Logo](assets/logo.svg "Company logo")
```

**For AI files:** many assistants can't see an image referenced from a Markdown file. Describe what matters in words as well, and make the alt text informative.

---

## 8. Inline code

Wrap code, commands, file names, paths, variable names and literal values in single backticks.

```markdown
Run `npm test` before committing.
Configuration lives in `config/settings.yaml`.
Set `DEBUG=false` in production.
```

If the code itself contains a backtick, wrap it in double backticks with spaces:

```markdown
`` Use `backticks` like this ``
```

**For AI files:** backticks tell the assistant "this is exact". Always put commands, file paths and identifiers in backticks so they're copied character for character.

---

## 9. Code blocks

### Fenced code blocks

Put three backticks on a line before the code and three after. Add a language name straight after the opening backticks to get syntax highlighting.

````markdown
```python
def greet(name: str) -> str:
    return f"Hello, {name}!"
```
````

Common language tags: `bash`, `shell`, `powershell`, `python`, `javascript`, `typescript`, `csharp`, `java`, `go`, `rust`, `sql`, `json`, `yaml`, `toml`, `html`, `css`, `markdown`, `text`.

### Showing a code block inside a code block

To show a fence inside a code block, make the outer fence longer: four backticks around three, or use tildes (`~~~`).

`````markdown
````markdown
```bash
echo "inner block"
```
````
`````

This comes up a lot when a SKILL.md or BRIEF.md includes an example Markdown file.

### Indented code blocks

Lines indented by four spaces also become a code block. Avoid this style: it can't take a language tag and is easy to trigger by accident inside lists.

**For AI files:** use code blocks for:

- commands to run
- templates the assistant should fill in
- examples of input and the output you expect
- file structures (use `text` as the language)

```text
my-skill/
├── SKILL.md
├── scripts/
│   └── validate.py
└── references/
    └── style-guide.md
```

---

## 10. Blockquotes

Start a line with `>`.

```markdown
> This is a quotation or a highlighted note.
>
> It can run over several paragraphs.
```

Blockquotes can nest and can contain other Markdown:

```markdown
> **Note:** Run the migration first.
>
> > Nested quote.
```

### GitHub alerts

GitHub (and some other tools) render special blockquotes as coloured callouts:

```markdown
> [!NOTE]
> Useful background information.

> [!TIP]
> A helpful suggestion.

> [!IMPORTANT]
> Key information the reader needs.

> [!WARNING]
> Something that could cause problems.

> [!CAUTION]
> An action with risky consequences.
```

Where alerts aren't supported, they still appear as ordinary blockquotes, so they're safe to use.

---

## 11. Horizontal rules

Three or more hyphens, asterisks or underscores on a line of their own:

```markdown
---
```

Leave a blank line above `---`. Otherwise the line of text above it can turn into a heading (a "setext" heading).

> **Caution:** `---` at the very top of a file starts YAML frontmatter (see section 13). Don't put a horizontal rule on the first line.

---

## 12. Tables

Tables are a GFM extension that nearly every tool supports. Use pipes `|` to separate columns, with a line of hyphens under the header row.

```markdown
| Command         | Purpose                 |
|-----------------|-------------------------|
| `npm run dev`   | Start the dev server    |
| `npm test`      | Run unit tests          |
| `npm run lint`  | Check code style        |
```

The columns don't need to line up in the source. This is equally valid:

```markdown
| Command | Purpose |
|---|---|
| `npm test` | Run unit tests |
```

### Column alignment

Put colons in the separator row:

```markdown
| Left | Centre | Right |
|:-----|:------:|------:|
| a    |   b    |     c |
```

### Limits

- No line breaks inside a cell (some renderers accept `<br>`).
- To show a literal pipe in a cell, write `\|`.
- No merged cells and no lists inside cells.

**For AI files:** tables are good for lookups such as command → purpose, error → fix, or field → type → required. They're poor at holding long explanations. Use lists or headings for those.

---

## 13. YAML frontmatter

Frontmatter is a block of YAML at the **very top** of a file, between two `---` lines. Markdown itself doesn't define it, but many tools read it, including Agent Skills, where it's **required**.

```markdown
---
name: weekly-report
description: Builds a weekly status report from meeting notes. Use when the user asks for a weekly report, status update or progress summary.
---

# Weekly Report

The body of the file starts here.
```

### YAML basics you'll need

```yaml
---
# A comment
name: my-skill                 # a string (quotes are optional)
version: 1.2                   # a number
enabled: true                  # a boolean
description: "Text with: a colon needs quotes"
tags:                          # a list
  - reports
  - writing
metadata:                      # nested keys
  author: Jane Smith
  updated: 2026-11-25
long_text: >                   # folded: lines join with spaces
  This long description is
  written across several lines.
literal_text: |                # literal: line breaks are kept
  Line one
  Line two
---
```

### Common frontmatter mistakes

| Mistake | Fix |
|---|---|
| Blank line or text before the first `---` | The `---` must be on line 1 |
| Tabs for indentation | Use spaces only |
| A colon followed by a space in an unquoted value, e.g. `description: Use when: X` | Put the value in quotes |
| Missing the closing `---` | Always close the block |
| Smart quotes (`“ ”`) copied from a word processor | Use straight quotes (`" "`) |

---

## 14. Escaping special characters

To show a Markdown character literally, put a backslash before it:

```markdown
\*not italic\*
\# not a heading
1\. not a list item
Use the \| pipe in a table cell.
```

Characters you can escape: `` \ ` * _ { } [ ] ( ) # + - . ! | < > ``

Inside inline code and code blocks nothing needs escaping. Everything is literal. That's usually the simplest fix.

---

## 15. Comments

Markdown has no comment syntax of its own, but HTML comments are hidden when the file is rendered:

```markdown
<!-- TODO: add the deployment section -->
```

> **Warning for AI files:** a comment is hidden from human readers **but not from the AI**, which reads the raw text. Don't leave stale notes in comments, and never use comments to hide instructions from people reviewing the file.

---

## 16. Collapsible sections

GitHub and many viewers support the HTML `<details>` element:

```markdown
<details>
<summary>Full list of environment variables</summary>

| Variable | Default |
|---|---|
| `PORT` | `3000` |
| `LOG_LEVEL` | `info` |

</details>
```

Leave a blank line after `<summary>` and before `</details>` so that the Markdown inside is rendered.

As with comments, an AI sees collapsed content in full. Collapsing only tidies the view for people.

---

## 17. Footnotes

Supported on GitHub and in many tools, though not part of core Markdown:

```markdown
The API is rate-limited.[^1]

[^1]: 100 requests per minute per key.
```

Footnotes are rarely worth using in AI instruction files. Put the detail inline where the assistant will see it.

---

## 18. Worked example: SKILL.md

A **skill** is a folder containing a `SKILL.md` file, plus optional scripts, references and assets. The open Agent Skills format is supported by a growing number of AI apps and coding assistants.

### Required structure

1. **YAML frontmatter** with at least:
   - `name`: lower-case letters, numbers and hyphens, up to 64 characters, matching the folder name.
   - `description`: what the skill does **and when to use it**, up to 1,024 characters. The app reads only the name and description to decide whether to load the skill, so this is the most important line in the file.
2. A **Markdown body**: the instructions the AI follows once the skill is loaded.

Some apps accept extra optional fields such as `license`, `compatibility`, `metadata` or `allowed-tools`. Check the documentation of each app you target.

### Example

````markdown
---
name: meeting-minutes
description: Turns raw meeting notes or a transcript into formatted minutes with decisions and action items. Use when the user shares meeting notes, a transcript or a recording summary and asks for minutes, a recap or action items.
---

# Meeting Minutes

Turn rough notes into clear, consistent minutes.

## When to use

- The user pastes notes or a transcript and asks for minutes.
- The user asks "what did we decide?" about a meeting.

## Workflow

1. Read the whole input before writing anything.
2. Identify the meeting title, date and attendees. If any are missing, ask once.
3. Extract **decisions** (agreed outcomes) and **action items** (owner + task + due date).
4. Write the minutes using the template below.
5. Check every action item has an owner. Mark missing owners as `UNASSIGNED`.

## Output template

```markdown
# <Meeting title>: <YYYY-MM-DD>

**Attendees:** <names>

## Summary
<two or three sentences>

## Decisions
- <decision>

## Action items
| Owner | Action | Due |
|---|---|---|
| <name> | <task> | <date> |
```

## Rules

- Keep the summary under 60 words.
- Use the attendees' names exactly as written in the notes.
- **Never** invent a due date. Write `TBC` instead.

## Example

**Input:**

```text
sync w/ Priya + Tom. agreed to ship v2 on 3 Dec. Tom to fix login bug by Fri.
```

**Output:**

```markdown
## Decisions
- Ship v2 on 3 December.

## Action items
| Owner | Action | Due |
|---|---|---|
| Tom | Fix login bug | Friday |
```

## Additional resources

- For board meetings, follow [references/board-format.md](references/board-format.md).
- To check the output, run `python scripts/check_minutes.py <file>`.
````

### What each Markdown element is doing

| Element | Job in the skill |
|---|---|
| Frontmatter | Lets the app find and select the skill |
| `#` title | Names the skill for the reader |
| `## When to use` | Confirms the skill fits the request |
| Numbered list | A sequence the AI follows in order |
| Code block with template | Exact output shape to copy |
| Bold **Never** | Marks a hard rule |
| Input/output example | Shows rather than tells |
| Relative links | Supporting files loaded only when needed |

### SKILL.md tips

- Write the description in the third person and include the words a user would actually say ("minutes", "recap", "action items").
- Keep SKILL.md reasonably short. A common guideline is under about 500 lines. Move long reference material into linked files.
- Give one strong example rather than many weak ones.
- Say *why* a rule exists. The AI applies a reason more sensibly than a bare command.

---

## 19. Worked example: AGENTS.md

**AGENTS.md** is a plain Markdown file at the root of a code repository that tells coding assistants how to work on the project. Think of it as a README for agents. Many coding tools read it automatically. Some products use their own file name for the same job; you can often keep one AGENTS.md and point the other files at it.

There are **no required fields and no frontmatter**. It's ordinary Markdown, so clear headings and exact commands are what matter.

In a large repository (a monorepo), you can put further AGENTS.md files in subfolders. Tools usually use the one nearest to the file being edited.

### Example

````markdown
# AGENTS.md

## Project overview

Recipe-sharing web app. Frontend in `web/` (React + TypeScript),
API in `api/` (ASP.NET Core 10, C#). PostgreSQL database.

## Setup

```bash
npm install --prefix web
dotnet restore api
cp .env.example .env
```

## Build and run

| Task | Command |
|---|---|
| Run the frontend | `npm run dev --prefix web` |
| Run the API | `dotnet run --project api` |
| Run all tests | `./scripts/test-all.sh` |
| Lint | `npm run lint --prefix web` |

## Code style

- TypeScript: strict mode, no `any`.
- C#: file-scoped namespaces, nullable reference types enabled.
- Name React components in PascalCase, one component per file.
- Prefer small functions. Split anything over 40 lines.

## Testing

1. Add or update tests for every change.
2. Run `./scripts/test-all.sh` and make sure it passes.
3. Never delete a failing test to make the suite pass. Fix the code or ask.

## Git and pull requests

- Branch names: `feature/<short-name>` or `fix/<short-name>`.
- Commit messages: imperative mood, e.g. `Add recipe search`.
- PR title format: `[web]` or `[api]` prefix, then a summary.

## Boundaries

- **Do not** edit files in `api/Migrations/` by hand. Generate them with `dotnet ef migrations add <Name>`.
- **Do not** commit `.env` or any secrets.
- **Ask first** before adding a new dependency.

## Useful context

- Design decisions are recorded in `docs/adr/`.
- The API contract is in `api/openapi.yaml`. Keep it in sync.
````

### AGENTS.md tips

- **Commands in code blocks or backticks, every time.** The assistant will run them exactly as written, so test them.
- **Put boundaries in their own section.** "Do not" and "Ask first" rules are easy to find under one heading.
- **Be concrete.** "Use small functions" is vague. "Split anything over 40 lines" can be followed and checked.
- **Keep it current.** An out-of-date command is worse than none: the assistant will trust it.
- **Don't duplicate the README.** Link to it, and keep AGENTS.md about *how to work* on the code.

---

## 20. Worked example: BRIEF.md

A **BRIEF.md** is a product brief written for a coding assistant: *what* to build and *why*, before anyone writes code. AGENTS.md covers *how to work* in the repo. BRIEF.md covers *what the product should do*. Unlike SKILL.md and AGENTS.md, there's no standard format, so the structure below is a recommended starting point.

A good brief answers the questions the assistant would otherwise guess at: who it's for, what's in scope, what's out of scope, and how you'll know it's done.

### Example

````markdown
# BRIEF: Habit Tracker MVP

> **Status:** Approved · **Owner:** Sam Lee · **Last updated:** 2026-11-25

## Problem

People who want to build daily habits lose track after a week.
Existing apps are cluttered with features they don't use.

## Goal

A minimal web app where a user can track up to five daily habits
and see their current streak at a glance.

## Target users

- Adults starting one to five new habits.
- Use it on a phone, once or twice a day, for under a minute.

## Core features (in scope)

1. **Create a habit**: name (max 40 characters) and optional emoji.
2. **Check in**: one tap marks today as done.
3. **Streak display**: current streak and longest streak per habit.
4. **History**: a 30-day grid per habit.

## Out of scope (do not build)

- User accounts or login. Store data locally in the browser.
- Reminders or notifications.
- Social or sharing features.

## User stories

- As a user, I want to add a habit in under ten seconds so that setup doesn't put me off.
- As a user, I want to see my streak so that I stay motivated.

## Technical constraints

| Area | Decision |
|---|---|
| Framework | React 19 + Vite |
| Storage | `localStorage`, no backend |
| Styling | Plain CSS modules |
| Browser support | Latest Chrome, Safari, Firefox |

## Data model

```json
{
  "id": "uuid",
  "name": "Read 10 pages",
  "emoji": "📚",
  "checkIns": ["2026-11-24", "2026-11-25"]
}
```

## Acceptance criteria

- [ ] A user can create, rename and delete a habit.
- [ ] Checking in twice on the same day has no extra effect.
- [ ] A streak resets if a day is missed.
- [ ] The app works with no network connection after first load.
- [ ] All features usable on a 375-pixel-wide screen.

## Open questions

- Should a user be able to check in for yesterday? *(Decision needed before build.)*

## References

- Wireframes: [docs/wireframes.png](docs/wireframes.png)
- Colour palette: see [docs/design.md](docs/design.md)
````

### BRIEF.md tips

- **"Out of scope" matters as much as "in scope".** Without it, assistants tend to add features you didn't ask for.
- **Write acceptance criteria as a task list.** Each item should be testable. You can ask the assistant to tick them off and to write a test for each.
- **Number the core features** so you can refer to them ("implement feature 3 next").
- **List open questions openly.** Tell the assistant to ask rather than guess.
- **Put a status and date at the top** so it's clear whether the brief is current.

---

## 21. Style rules for AI-readable Markdown

1. **One idea per section.** Headings act as an index for the AI. Clear names like `## Testing` beat clever ones like `## Keeping it green`.
2. **Lists over long paragraphs** for rules and steps. Prose is better for explaining reasons and context.
3. **Numbered lists only for sequences.** If order doesn't matter, use bullets.
4. **Exact text in backticks or code blocks**: commands, paths, names, templates.
5. **Show examples.** One realistic input/output pair often teaches more than a page of description.
6. **Explain the reason** behind important rules. "Use `TBC`, because invented dates cause missed deadlines" generalises better than "Use `TBC`."
7. **Be specific and checkable.** Swap "keep it short" for "under 60 words".
8. **Keep files lean.** Every line costs the AI attention. Move detailed reference material into linked files.
9. **Use plain, standard Markdown.** Headings, lists, code blocks, tables and links work everywhere. Fancy extensions may not.
10. **Save as UTF-8 plain text** with the `.md` extension. Don't write these files in a word processor, because it inserts smart quotes and hidden formatting.
11. **Check the rendering.** Preview the file in your editor or on GitHub. If it looks broken to you, it may confuse the AI too.

---

## 22. Quick reference card

| Element | Syntax |
|---|---|
| Heading | `# H1` `## H2` `### H3` |
| Bold | `**bold**` |
| Italic | `*italic*` |
| Strikethrough | `~~text~~` |
| Bullet list | `- item` |
| Numbered list | `1. item` |
| Nested item | indent with spaces under the parent's text |
| Task list | `- [ ] todo` / `- [x] done` |
| Link | `[text](url)` |
| Heading link | `[text](#heading-name)` |
| Image | `![alt text](path.png)` |
| Inline code | `` `code` `` |
| Code block | ` ``` ` + language, code, ` ``` ` |
| Nested code block | four backticks around three |
| Blockquote | `> text` |
| Alert | `> [!NOTE]` |
| Horizontal rule | `---` (not on line 1) |
| Table | `\| a \| b \|` then `\|---\|---\|` |
| Frontmatter | `---` YAML `---` at the very top |
| Escape | `\*` |
| Comment | `<!-- hidden from readers, not from AI -->` |
| Collapsible | `<details><summary>Title</summary> … </details>` |
| Line break | end the line with `\` |
