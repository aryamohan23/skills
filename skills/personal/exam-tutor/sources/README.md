# Sources

Put Aditi's study material here. The `exam-tutor` chat mode reads from these subfolders.

## Layout

```
sources/
  past-papers/   # past papers, question banks — PDFs or images
  textbooks/     # Rogers, Fuhrman & Zimmerman, etc. — PDFs
```

## Reading PDFs

The chat mode uses **poppler** (`pdftotext`, `pdftoppm`, `pdfinfo`) to read PDFs on demand. Install it once per machine:

- **macOS:** `brew install poppler`
- **Debian/Ubuntu:** `sudo apt install poppler-utils`
- **Windows:** `choco install poppler` or `scoop install poppler`

Verify: `pdftotext -v` should print a version.

Once installed, the tutor extracts pages itself — you just drop the PDF into `textbooks/` or `past-papers/` and reference it by name in chat.

## Images

PNG/JPG work directly if you're on a vision-capable model (Claude Sonnet, GPT-4o). If the model says it can't see one, attach it to the chat message with the paperclip icon.

## Privacy

This folder is `.gitignore`d — nothing here gets committed.
