# Sources

Put Aditi's study material here. The `exam-tutor` chat mode reads from these subfolders.

## Layout

```
sources/
  past-papers/   # past papers, question banks — PDFs or images
  textbooks/     # Rogers, Fuhrman & Zimmerman, etc. — PDFs
```

## Reading PDFs

The chat mode uses **poppler** (`pdftotext`, `pdftoppm`, `pdfinfo`) and **ocrmypdf** to read PDFs on demand. Install once per machine:

- **macOS:** `brew install poppler ocrmypdf`
- **Debian/Ubuntu:** `sudo apt install poppler-utils ocrmypdf`
- **Windows:** poppler via `choco install poppler` or `scoop install poppler`; ocrmypdf via `pip install ocrmypdf` (needs Tesseract separately)

Verify: `pdftotext -v` and `ocrmypdf --version` should both print a version.

### How different PDFs are handled

| Kind of PDF | What the tutor does |
|---|---|
| Born-digital (real text layer, e.g. modern textbook PDF) | `pdftotext` — fast, low-token |
| Scanned textbook (photos of pages, text-heavy) | `ocrmypdf` once to add a text layer, then `pdftotext` forever after |
| Scanned past paper / atlas with figures, X-rays, ECGs, waveforms | `pdftoppm` renders the page as a PNG, vision model reads text + images together |

Rule of thumb: **anything with clinical images on the page gets rendered as PNG** (never OCR alone — OCR loses figures). OCR is only for making bulk printed text searchable.

You don't have to think about which one to use — just drop the PDF in and reference it by name. The chat mode picks the right tool.

## Images

PNG/JPG work directly if you're on a vision-capable model (Claude Sonnet, GPT-4o). If the model says it can't see one, attach it to the chat message with the paperclip icon.

## Privacy

This folder is `.gitignore`d — nothing here gets committed.
