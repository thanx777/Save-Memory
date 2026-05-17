---
name: save-memory
description: >
  Extract key lessons from conversations and persist them to the project memory system so future sessions automatically load them. Use this skill whenever the user says "remember this", "save to memory", "记住这个", "保存到记忆", "下次别忘了", "存下来", "保存经验", "/save-memory", or any phrase asking to preserve knowledge. Also proactively suggest using this skill at the end of debugging sessions, when the user expresses relief ("终于解决了", "原来是这里的问题", "坑死我了"), or when they discover a non-obvious solution. If the conversation involved troubleshooting, discovering hidden constraints, or establishing a new workflow, ask the user if they'd like to save it — do not wait for an explicit command.
---

# Save Memory

Extract actionable knowledge from conversations and persist it to the project memory system so future sessions benefit from past experience.

## Memory system overview

Each project has a memory directory at `~/.claude/projects/<project-slug>/memory/`. The `project-slug` is the full working-directory path with separators replaced by `--` (e.g., `D--Vibecoding-TraeSolo-api-gateway-manager`).

```
memory/
├── MEMORY.md          # Index (loaded every session, keep under 200 lines)
└── <topic-slug>.md    # Individual memory files with YAML frontmatter
```

User-level cross-project memories live at `~/.claude/memory/`.

## Autonomous saving

Do not ask the user whether to save — if the conversation contains knowledge worth preserving, save it automatically. The user expects the AI to learn and build memory without being prompted. At the end of each session, or whenever a significant insight surfaces, run the save-memory workflow pro-actively.

A Stop hook writes the session timestamp to `~/.claude/.last-session` on exit. Use this to detect session gaps and trigger autonomous saving when reconnecting after a substantial break.

## Workflow

### 1. Scan the conversation

Review the current conversation for knowledge worth preserving.

**Worth saving:**
- Troubleshooting where the root cause was non-obvious (especially after multiple failed attempts)
- User preferences about tools, workflow, or coding style
- Hidden constraints discovered (e.g., "this library doesn't support X on Windows")
- Architectural decisions made after considering alternatives
- External system references (dashboards, issue trackers, documentation URLs)

**Skip:**
- Single-line typo fixes or syntax errors the user would catch immediately
- Information already obvious from reading the codebase
- Trivia the user asked about in passing without follow-up
- Ephemeral state: current branch names, in-progress work, temporary debug logs

### 2. Classify each piece of knowledge

| Type | Purpose | Storage |
|------|---------|---------|
| `user` | User's role, preferences, habits, knowledge level | `~/.claude/memory/` |
| `feedback` | User's guidance on HOW to work — corrections AND confirmed approaches | Project `memory/` |
| `project` | Project facts: bugs, architecture decisions, constraints, goals | Project `memory/` |
| `reference` | Pointers to external systems (dashboards, Linear projects, Slack channels) | Project `memory/` |

When unsure, default to `project`.

### 3. Check for existing memories

Check the project's `memory/MEMORY.md` index for relevant existing entries. If a memory already covers the topic, **update** it instead of creating a duplicate. If updating would make the file unfocused, split it into two files.

### 4. Write memory files

Format each file exactly as specified in `references/schemas.md`. Read that file for the complete format specification, field requirements, and content structure patterns.

### 5. Update the index

Add an entry to `memory/MEMORY.md`:
```
- [Title](file.md) — one-line hook (under ~150 characters)
```

`MEMORY.md` is loaded into every future session. Keep it concise — if approaching 200 lines, consolidate or archive less-relevant entries.

### 6. Cross-project knowledge

If a piece of knowledge is user-specific, also record it in `~/.claude/memory/`. This covers user role, preferences, and habits that apply regardless of which project the user is working in.

### 7. Validate

Run the bundled validation script to catch format errors:
```bash
python <path-to-this-skill>/scripts/validate_memory.py <project-memory-dir>
```

Fix any issues before reporting to the user.

### 8. Report

One sentence per saved memory — what was saved and where. End with a brief summary: what changed and what to expect in future sessions. Match the user's communication style: concise, no emojis, no filler.
