# Markitdown Reader

A skill for converting binary and token-heavy files to markdown before reading.

Built on [**markitdown**](https://github.com/microsoft/markitdown) — Microsoft's open-source CLI for converting files to Markdown.

## What this is

Reading PDFs, Word docs, and spreadsheets directly burns tokens and produces unreliable output. This skill teaches Claude to convert them to clean markdown first using `markitdown`, then read the lightweight result.


## Skill Structure

```
markitdown-skill/
├── SKILL.md              # Core instructions
├── README.md
└── LICENSE
```

## Quick start

**Claude Code:** Add this folder as a skill via `--plugin-dir` or add as a marketplace source.

**Claude Projects:** Upload `SKILL.md` to project knowledge.

**Custom instructions:** Copy core rules from `SKILL.md` into your system prompt.

**API calls:** Include `SKILL.md` content in your system prompt.

## Prerequisites

```bash
# All formats (recommended)
pip install markitdown[all]

# Audio support also needs ffmpeg
brew install ffmpeg  # macOS
```

## What it handles

| Format | Extensions |
|--------|-----------|
| PDF | `.pdf` |
| Word | `.docx` |
| PowerPoint | `.pptx` |
| Excel | `.xlsx`, `.xls` |
| HTML | `.html`, `.htm` |
| Outlook | `.msg` |
| Audio | `.wav`, `.mp3` |
| YouTube | URL (transcript) |

## How it works

1. Detects binary/token-heavy file in user request
2. Converts to markdown via `markitdown` CLI
3. Validates output (non-empty, readable)
4. Reads the lightweight `.md` instead of raw binary
5. Falls back gracefully if conversion fails

## Author

[Kyrylo Alieksieiev](https://github.com/kiryafn)

## License

MIT
