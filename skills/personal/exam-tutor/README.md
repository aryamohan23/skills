# Exam Tutor — setup on a new machine

You've just cloned this repo onto a fresh Mac (or Linux box). Here's how to get the exam tutor running in about 5 minutes.

## 1. Prerequisites

- **VS Code** — [download](https://code.visualstudio.com/).
- **GitHub Copilot subscription** — Individual, Business, or the free trial (this is what pays for the underlying LLM).
- Two VS Code extensions, both by GitHub:
  - **GitHub Copilot**
  - **GitHub Copilot Chat**

  Install from the Extensions pane (Cmd+Shift+X), sign in when prompted.

## 2. Clone and switch to the branch

```bash
git clone https://github.com/aryamohan23/skills.git
cd skills
git checkout exam-tutor   # until this branch is merged
```

## 3. Install PDF tools

The tutor uses `poppler` (for reading PDFs) and `ocrmypdf` (for OCR-ing scanned textbooks).

- **macOS:**
  ```bash
  brew install poppler ocrmypdf
  ```
- **Debian / Ubuntu:**
  ```bash
  sudo apt install poppler-utils ocrmypdf
  ```
- **Windows:** poppler via `choco install poppler` or `scoop install poppler`; ocrmypdf via `pip install ocrmypdf` (needs Tesseract separately).

Verify:
```bash
pdftotext -v && pdfimages -v && ocrmypdf --version
```

## 4. Register the chat mode with VS Code

VS Code's workspace-scoped `chat.modeFilesLocations` setting doesn't always fire (workspace trust and version quirks). The reliable install is a **symlink** into your user-level prompts folder.

- **macOS:**
  ```bash
  mkdir -p ~/Library/Application\ Support/Code/User/prompts
  ln -sf "$PWD/.github/chatmodes/exam-tutor.chatmode.md" \
     ~/Library/Application\ Support/Code/User/prompts/exam-tutor.chatmode.md
  ```
- **Linux:**
  ```bash
  mkdir -p ~/.config/Code/User/prompts
  ln -sf "$PWD/.github/chatmodes/exam-tutor.chatmode.md" \
     ~/.config/Code/User/prompts/exam-tutor.chatmode.md
  ```
- **Windows (PowerShell, run as admin):**
  ```powershell
  $target = "$PWD\.github\chatmodes\exam-tutor.chatmode.md"
  $link   = "$env:APPDATA\Code\User\prompts\exam-tutor.chatmode.md"
  New-Item -ItemType Directory -Force -Path (Split-Path $link) | Out-Null
  New-Item -ItemType SymbolicLink -Path $link -Target $target
  ```

Because it's a symlink, editing the file in the repo automatically updates what VS Code sees — no re-copy needed.

## 5. Open the repo in VS Code and reload

```bash
open -a "Visual Studio Code" .   # macOS
# or: code .   (if you've installed the CLI)
```

Then Cmd+Shift+P → **Developer: Reload Window**. If prompted, **trust the workspace** (adds workspace `.vscode/settings.json` to trusted list).

## 6. Verify the mode is available

Open Copilot Chat (Cmd+Ctrl+I). In the chat input, click the mode picker (currently says `Ask` / `Agent` / etc.). You should see **exam-tutor** in the list. Pick it.

If it's not there, run Cmd+Shift+P → **Chat: Configure Chat Modes** — that opens the discovery UI so you can confirm VS Code sees the file.

## 7. Drop in your source material

```
skills/personal/exam-tutor/sources/past-papers/   ← PDFs or images of past papers
skills/personal/exam-tutor/sources/textbooks/     ← Rogers, Fuhrman & Zimmerman PDFs
```

Both folders are `.gitignore`d — nothing gets pushed to git, so PDFs stay local to each machine. You need to copy them over separately (AirDrop, USB, Drive) on each machine.

## 8. Smoke test

In Copilot Chat with **exam-tutor** mode selected:

```
list what's in skills/personal/exam-tutor/sources/
```

The agent should run `ls` and describe your folders. Then try:

```
there's a past paper in past-papers/. read page 1 and tell me what topic it covers.
```

It should run `pdfinfo` and `pdftoppm`, render page 1 as a PNG, and describe both text and any figures.

Finally, the real test:

```
quiz me on [some topic from your sources]
```

It'll grill you first (topic → format → difficulty → count → interleaving), then write an HTML file into `skills/personal/exam-tutor/quizzes/`. Right-click the file → **Reveal in Finder** → open in a browser.

## Troubleshooting

- **`exam-tutor` doesn't appear in the picker** — reload the window, then check the symlink exists (`ls -la ~/Library/Application\ Support/Code/User/prompts/`).
- **`pdftotext: command not found` in chat** — poppler isn't installed on this machine; go back to step 3.
- **Model can't see PDF content** — most likely a scanned PDF with no text layer. Say "OCR it" and the tutor will run `ocrmypdf`. Takes a few minutes for big books, one-off cost.
- **Model can't see figures on a scanned page** — say "render that page as an image" — the tutor will use `pdftoppm` and read the PNG.

## What to do next

- Editing the skill's behavior: see [EDITING.md](./EDITING.md).
- The generated quiz files land in [quizzes/](./quizzes/README.md) — self-contained HTML, open in any browser.
