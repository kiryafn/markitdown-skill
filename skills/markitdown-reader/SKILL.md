---
name: markitdown-reader
description: Use when asked to read, analyze, or reference a binary or token-heavy file (PDF, DOCX, PPTX, XLSX, XLS, HTML, ZIP, audio, Outlook MSG, YouTube transcript). Convert to markdown first with markitdown CLI to save tokens and improve parsing quality.
---

# Markitdown Reader

Convert binary and token-heavy files to markdown before reading. Uses the `markitdown` CLI to produce clean `.md` output that is cheaper and easier to parse than raw binary formats.

**Core principle:** Never read a binary or large-format file directly when a lightweight markdown conversion exists.

## When to Use

- User provides or references a PDF, DOCX, PPTX, XLSX, XLS, HTML, ZIP, MSG, audio file, or YouTube link
- File is too large or token-expensive to read natively
- Format cannot be read natively (PPTX, DOCX, XLS, MSG)
- User asks to read/summarize/analyze a document in a supported format

**Do NOT use when:**
- File is already plain text, markdown, or source code
- File is a small image (use native image reading)
- User explicitly asks for raw/native reading
- PDF is under 5 pages (native PDF reading is fine for small files)

## Supported Formats

| Format | Extension | Notes |
|--------|-----------|-------|
| PDF | `.pdf` | Best for large/complex PDFs |
| Word | `.docx` | |
| PowerPoint | `.pptx` | Extracts slide text and notes |
| Excel | `.xlsx`, `.xls` | Converts tables to markdown |
| HTML | `.html`, `.htm` | Strips tags, keeps structure |
| ZIP | `.zip` | Extracts and converts contents |
| Outlook | `.msg` | Headers + body + attachment names |
| Audio | `.wav`, `.mp3` | Requires `ffmpeg` |
| YouTube | URL | Requires transcript availability |

## Procedure

```
1. Check markitdown installed  →  which markitdown || pip install markitdown[all]
2. Convert to markdown          →  markitdown "<path>" > /tmp/<name>.md 2>/tmp/<name>_err.log
3. Validate output              →  wc -l /tmp/<name>.md (must be > 0 lines)
4. Check output size            →  wc -c /tmp/<name>.md (if > 500KB, read with offset/limit)
5. Read converted file          →  Read tool on /tmp/<name>.md
6. On failure                   →  check /tmp/<name>_err.log, see Error Handling below
```

### Conversion Command

```bash
# Always redirect stderr to catch errors
markitdown "/path/to/file.pdf" > /tmp/file.md 2>/tmp/file_err.log

# Check conversion succeeded
if [ ! -s /tmp/file.md ]; then
  cat /tmp/file_err.log
fi
```

### Large Output Handling

If converted markdown exceeds ~500KB or ~10,000 lines:
- Use `Read` with `offset` and `limit` to read in chunks
- Summarize sections as you go, don't load everything into context
- Tell user about file size before reading: "Document converted to X lines of markdown. Reading relevant sections."

## Quick Reference

| Scenario | Command |
|----------|---------|
| PDF → MD | `markitdown doc.pdf > /tmp/doc.md 2>/tmp/doc_err.log` |
| DOCX → MD | `markitdown doc.docx > /tmp/doc.md 2>/tmp/doc_err.log` |
| PPTX → MD | `markitdown slides.pptx > /tmp/slides.md 2>/tmp/slides_err.log` |
| XLSX → MD | `markitdown data.xlsx > /tmp/data.md 2>/tmp/data_err.log` |
| Audio → MD | `markitdown audio.mp3 > /tmp/audio.md 2>/tmp/audio_err.log` |
| YouTube → MD | `markitdown "https://youtube.com/watch?v=ID" > /tmp/yt.md 2>/tmp/yt_err.log` |
| HTML → MD | `markitdown page.html > /tmp/page.md 2>/tmp/page_err.log` |

## Error Handling

| Error | Cause | Fix |
|-------|-------|-----|
| `command not found` | Not installed | `pip install markitdown[all]` |
| `ModuleNotFoundError` | Missing format extra | `pip install markitdown[pdf]` (match format) |
| Empty output file | Corrupted/encrypted file, scanned PDF | Tell user file cannot be parsed; suggest OCR for scanned PDFs |
| `UnicodeDecodeError` | Encoding mismatch | Try: `markitdown --charset utf-8 <file>` or convert encoding first |
| `ffmpeg not found` | Audio without ffmpeg | `brew install ffmpeg` (macOS) / `apt install ffmpeg` (Linux) |
| Timeout / hang | Huge file (>100MB) | Split file first, or extract specific pages: `pdftk input.pdf cat 1-20 output chunk.pdf` |
| YouTube fails | No transcript available | Inform user — not all videos have transcripts; no API key fixes this |
| Garbled table output | Complex merged cells in Excel | Fall back to reading with `openpyxl` directly in Python |

### Fallback Strategy

If markitdown fails and no fix works:
1. **PDF** → try native `Read` tool (works for small PDFs up to ~10 pages)
2. **XLSX** → try `python3 -c "import openpyxl; ..."` to extract specific sheets
3. **DOCX** → try `python3 -c "import docx; ..."` to extract raw text
4. **Audio** → suggest user provide a transcript manually
5. Always inform user of the failure and what you tried

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Reading large PDF natively | Convert first — markdown is cheaper |
| Ignoring stderr | Always capture `2>err.log` — silent failures waste time |
| Writing temp files to project dir | Use `/tmp/` to avoid polluting workspace |
| Not validating output | Check `wc -l` — empty output = failed conversion |
| Using on plain text / source code | Skip — already token-efficient |
| Loading entire huge output into context | Use `offset`/`limit` on Read for files >500KB |
| Assuming YouTube always works | Transcripts depend on video settings, not on your setup |

## Installation

```bash
# All formats (recommended)
pip install markitdown[all]

# Audio also needs ffmpeg
brew install ffmpeg  # macOS
```
