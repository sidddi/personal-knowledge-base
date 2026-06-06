---
name: meeting
description: Process a meeting note or transcript and update the vault — project file, TODOs, pending from others, people directory, and meeting archive.
---

# Skill: meeting

Process a meeting note or transcript and update the vault: project file, active TODOs, pending from others, people directory, and meeting archive.

## When to use
Run `/meeting inbox/<filename>.md` after dropping a meeting note or transcript in `inbox/`. You can also paste content directly and run `/meeting`.

## Vault structure expected
```
inbox/        ← input files land here
meetings/     ← processed notes archived here
projects/     ← one .md file per project
people.md     ← people directory
todo.md       ← your active tasks only
todo-old.md   ← completed tasks, append-only
```

---

## Instructions

### Step 1 — Read the input
If a file path is given: read the file.
If content is pasted directly: use that.

Detect input type:
- **Notes**: short, organized, written by a human
- **Transcript**: long, verbatim, with speaker labels or timestamps

For transcripts: extract a clean summary first (key topics, decisions, action items) before proceeding. Do not lose information — err on the side of keeping more.

### Step 2 — Archive the raw content
Save the original content unmodified to `meetings/YYYY-MM-DD_<slug>.md` with this frontmatter:

```markdown
---
date: YYYY-MM-DD
attendees: [list of names]
project: <project name or "general">
type: notes | transcript
---
```

The slug should be descriptive: `meetings/2026-05-31_kickoff-projecte-alpha.md`.

### Step 3 — Identify the project
Find which project this meeting belongs to. Look for project names mentioned in the content. Check `projects/` to see what exists.

- If the project file exists: use it
- If not: create `projects/<slug>.md` using the schema from CLAUDE.md
- If the meeting spans multiple projects: update each one
- If unclear: ask the user before proceeding

### Step 4 — Update the project file

Add or update these sections:

**Meetings table** — add one row:
```
| YYYY-MM-DD | Name1, Name2 | One-line summary of what was discussed |
```

**Status** — update if the meeting changed the project's current state.

**My TODOs** — add action items assigned to the user:
```
- [ ] Task description *(from YYYY-MM-DD)*
```
Only include tasks that belong to the user — not tasks assigned to others.

**Pending from others** — add commitments made by other people:
```
- Name → what they committed to *(by date, if mentioned)*
```
Look for phrases like "Joan will send", "Maria is checking", "waiting on", "they'll come back with".

**Key decisions** — add any decisions taken:
```
- YYYY-MM-DD: What was decided
```

### Step 5 — Update todo.md
Prepend the user's new action items to `todo.md`. Use the same format as the project file:
```
- [ ] Task description *(projecte-alpha, YYYY-MM-DD)*
```

Do NOT add other people's tasks here.

### Step 6 — Update people.md
For each person mentioned in the meeting:

- If they already exist in `people.md`: add any new context (new role info, new project association, anything relevant)
- If they are new: add an entry:

```markdown
## Full Name
- **Role**: (if mentioned)
- **Company / Team**: (if mentioned)
- **First seen**: YYYY-MM-DD
- **Projects**: [project names]
- **Notes**: anything relevant — personality, context, history
```

Only add people who are relevant (attendees, or people mentioned as dependencies). Skip generic references.

### Step 7 — Move processed file
If the input came from `inbox/`, delete or move the file after archiving. The archive in `meetings/` is the canonical copy.

### Step 8 — Report what changed
Summarize what was updated:
- Which project file(s) were touched
- How many TODOs added to todo.md
- How many pending-from-others added
- How many people added or updated
- Where the archive was saved
