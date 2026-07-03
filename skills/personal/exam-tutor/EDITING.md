# Editing the exam tutor skill

Everything the tutor does — how it grills you, how it reads PDFs, how it formats quizzes, what it refuses to do — is controlled by **one file**:

**[.github/chatmodes/exam-tutor.chatmode.md](../../../.github/chatmodes/exam-tutor.chatmode.md)**

That's the single source of truth. Because your user-level installation is a symlink to it, editing this file updates what VS Code sees immediately — no copying, no re-installing.

## Workflow

1. Open [.github/chatmodes/exam-tutor.chatmode.md](../../../.github/chatmodes/exam-tutor.chatmode.md).
2. Edit the section you want to change (see map below).
3. Save.
4. **Start a new chat.** Existing chat sessions keep the old prompt in memory until you reload the window or open a fresh chat. New chats pick up your edit immediately.
5. Commit and push if the change is worth keeping across machines.

## File structure — what controls what

The file has two parts: YAML frontmatter (top) and the prompt body (everything after the second `---`).

### Frontmatter

```yaml
---
description: 'Aditi PICU fellowship exam tutor — grill-first, quiz as HTML file, textbook-cited.'
---
```

- `description` — what shows up in the mode picker tooltip. Change if you rename the mode or want a shorter label.
- Don't rename the file (`exam-tutor.chatmode.md`) without also updating the symlink.

### Prompt body — section map

| Section | What to edit here |
|---|---|
| **Opening paragraphs** (top) | The exam name, the 3 testing dimensions. Change if you're adapting this for a different specialty. |
| **## Sources of truth** | Where the tutor looks for material and how it handles each file type. |
| **### PDF routing rules** | The decision tree for scanned vs digital PDFs, when to OCR vs render as PNG. Tweak if you find figures being missed or OCR being trusted when it shouldn't be. |
| **## Clinical style** | Vignette rules, MCQ format (A–E), drug dose unit conventions. Change to match a different exam's style. |
| **## Before generating any practice set, grill me** | The 6 grill questions and their defaults. Add / remove questions here. Change defaults if you find the tutor asking things you never care about. |
| **## How to build the question bank** | Retrieval-practice rules, interleaving, no-leakage rules. |
| **## Output format** and **### The template** | The HTML quiz template. **Careful here** — the CSS and JS in the code block are load-bearing; only the four "swap points" (title, h1, meta, `Q` array) should ever change per quiz. Tweak the template itself only if the quiz UX genuinely needs to change. |
| **### Filling the Q array** | The three question shapes (mcq / short / long) and the `accept` array rules for short-answer normalization. |
| **## When I come back with my score** | How the tutor responds to your results — misconception flagging, follow-up questions. |
| **## Tracking my weak spots** | How the tutor keeps an informal tally across a conversation. |
| **## After each set** | The three next-step options (harder / mixed review / new topic) and the bias toward review after wrong answers. |
| **## What you should never do** | Hard prohibitions. Add to this list when the tutor does something you don't want. |
| **## What you should do every time** | Non-negotiables. Add to this list when the tutor forgets something important. |

## Common tweaks

**"It keeps skipping the grilling and going straight to a quiz."**
→ Under **## What you should never do**, the first bullet already forbids this. If it keeps happening, strengthen the language: change *"unless I explicitly say 'skip the questions'"* to a more restrictive trigger phrase.

**"The MCQ distractors are too obvious."**
→ Under **## Clinical style**, the *Single best answer for MCQs* bullet describes distractor quality. Add examples of the *kind* of subtlety you want (e.g. "distractors should be based on the top 3 most common misdiagnoses for this presentation, not obviously wrong physiology").

**"It's not citing chapters — just saying 'per Rogers' vaguely."**
→ Under **## Sources of truth**, add: *"Every citation must include a specific chapter number and page range, not just the book name."*

**"I want it to also generate flashcards, not just quizzes."**
→ New section between **## How to build the question bank** and **## Output format**. Introduce a new command word ("flashcards" vs "quiz") and describe the alternate output shape.

**"Change the tutor's tone."**
→ The tone comes from the whole document but is most explicit in **## What you should do every time** ("Tell me what I'm weak on, kindly but plainly."). Adjust that line and any adjectives elsewhere.

## Testing your changes

- **New chat, always.** Existing chat sessions cache the system prompt.
- Give it a genuine request, not a meta-question. Say *"quiz me on ARDS management"* rather than *"do you remember your new rule?"* — the LLM's answer to the meta-question is usually a hallucination about its instructions.
- If a change didn't stick, re-read the section — LLMs sometimes weight later instructions more heavily, so a contradicting statement further down can override yours.

## Don't touch (unless you know why)

- **The YAML frontmatter format** — must be exactly `---` on lines 1 and 3 with nothing else between them but valid YAML. A stray character breaks the whole file and VS Code silently drops the mode.
- **The HTML template code block** — the CSS, `norm()`, `render()`, `check()`, `reveal()` functions are load-bearing. The `check()` scoring math and the `accept` normalization are used every quiz. Change only if you want the quiz UX itself to change.
- **The symlink** — the file at `~/Library/Application Support/Code/User/prompts/exam-tutor.chatmode.md` is a symlink into this repo. Deleting it breaks the mode's discovery on this machine; don't `cp` over it (that turns it into a real file that then diverges).

## Reverting a bad edit

Everything is in git:

```bash
git diff .github/chatmodes/exam-tutor.chatmode.md          # see what changed
git checkout .github/chatmodes/exam-tutor.chatmode.md      # discard local edits
git log --oneline .github/chatmodes/exam-tutor.chatmode.md # find a known-good commit
git show <commit>:.github/chatmodes/exam-tutor.chatmode.md # inspect a past version
```
