---
name: write
description: "Strips AI writing patterns and rewrites prose to sound natural in Chinese or English. Only activates on explicit writing or editing requests. Not for code comments, commit messages, or inline docs."
when_to_use: "帮我写, 改稿, 润色, 去AI味, 写一段, draft, edit text, proofread, sound natural, polish, rewrite"
metadata:
  version: "3.15.0"
---

# Write: Cut the AI Taste

Prefix your first line with 🥷 inline, not as its own paragraph.


Strip AI patterns from prose and rewrite it to sound human. Do not improve vocabulary; remove the performance of improvement.

## Pre-flight

1. **Text present?** If the user gave only an instruction with no actual prose to edit, ask for the text in one sentence. Do not proceed.
2. **Audience locked?** If the intended audience is unclear and cannot be inferred from the text (blog reader vs RFC vs email), ask before editing. Junior engineer and senior architect prose should read completely different.
3. **Language detected from the text being edited**, not the user's command:
   - Contains Chinese characters → load `references/write-zh.md`
   - Otherwise → load `references/write-en.md`

Read the loaded reference file. Then edit. No summary, no commentary, no explanation of changes unless explicitly asked.

## Hard Rules

- **Meaning first, style second.** If removing an AI pattern would change the author's intended meaning, keep the original. A failed rewrite sounds better but says something different.
- **Preserve unless told to cut.** Keep every sentence and paragraph unless the user explicitly asks to remove or replace specific parts. Flag unnecessary sections; do not delete them silently.
- **No silent restructuring.** Do not reorganize headings, reorder paragraphs, or merge sections unless structural changes are explicitly requested. Edit in place.
- **Match naming conventions.** Before creating new content files, check existing patterns in the target directory and follow them.
- **Never guess the target text.** If the request refers to "the paragraph above" or "what you just said" without quoting it, ask which exact text to edit. Do not rewrite conversation history without explicit permission.
- **Verify the change list against disk first.** When the user hands a list of changes to apply, diff each item against the actual file before editing. Common silent failures: stale-version residue, stray characters from earlier drafts, items already reverted, items that were never there. Report mismatches before applying.
- **Re-read 5 lines around each edit.** After every change, scan 5 lines above and below for orphan single-sentence paragraphs, stray characters, image alt drifting from prose, or term inconsistency. Fix before the next edit.
- **Stop after output.** Deliver the rewritten text. Then stop. Do not append a list of changes, a justification, or a "hope this helps" closer.
- **No emoji in edited text.** Remove any emoji from the output unless the user explicitly asks to keep them.

## Bilingual Review Mode

Activate when: mixed Chinese/English, "Chinese copywriting", "bilingual consistency", "release notes"

**Chinese rules** (from https://github.com/mzlogin/chinese-copywriting-guidelines):
- Space between Chinese and English characters (CN文字EN → CN 文字 EN)
- No mixing of punctuation (Chinese uses 、。？！；：, not commas/periods)
- Consistent terminology across all instances

**English in Chinese documents**: Flag unexplained English, suggest translation or add context.

**Bilingual pairs**: Confirm EN and CN versions convey the same meaning; mark translation loss.

## Release Note Template Mode

Activate when: "release", "changelog", "version", "release notes"

Generate from commit messages:
- **Breaking Changes**
- **New Features**
- **Fixes & Improvements**
- **Deprecations**

Format: tw93/Mole style (numbered list, bold label, one sentence on user effect, bilingual).

## Output

Return only the edited prose. If the text was truncated or if multiple versions were possible, note that in one sentence after the body. Otherwise, no wrapper, no preamble, no postscript.
