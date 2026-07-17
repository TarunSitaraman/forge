# Obsidian Config

*Minimal, deliberate Obsidian setup for Forge. Not the live `.obsidian/`
folder — that stays untracked (see `.gitignore`). This is the reference
copy of what to configure on a fresh vault open.*

## Core plugins to enable

- **Quick switcher** — primary navigation. Learn the shortcut, use it
  instead of the file tree.
- **Backlinks** — free with wikilinks; no setup needed.
- **Graph view** — useful occasionally to spot orphaned files; not a
  daily-use panel.

## Community plugins (the entire list)

None by default. Forge starts with zero community plugins. Add one only
when it removes real friction from navigation or capture, and record the
decision in `Systems/` (a decision record: what friction it solves, what
it costs).

Do not install: task managers, daily-note automation, database/table
plugins that produce non-portable content, or anything that stores state
Markdown can't represent. If a plugin would make a file meaningless
outside Obsidian, it violates the Markdown-only principle — don't use it.

## Settings worth setting explicitly

- **New file location:** same folder as current file (keeps captures
  where you are, not scattered to vault root).
- **Default view:** Source mode, not Live Preview — keeps you honest
  about what the raw Markdown looks like.
- **Attachment folder:** per top-level folder (`Projects/<name>/assets`),
  not one global dump.
