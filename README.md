# markitdown-reader

Claude Code skill that converts binary and token-heavy files (PDF, DOCX, PPTX, XLSX, audio, etc.) to markdown before reading, saving tokens and improving parsing quality.

## Install

```bash
claude plugins add github:YOUR_USERNAME/markitdown-skill
```

## Prerequisites

```bash
pip install markitdown[all]
```

## What it does

When you ask Claude Code to read a PDF, Word doc, PowerPoint, Excel file, or other supported format, this skill tells it to first convert the file to markdown using the `markitdown` CLI, then read the lightweight markdown version instead of the raw binary.

This saves significant tokens and produces cleaner, more reliable output.

## Supported formats

- PDF, DOCX, PPTX, XLSX, XLS
- Outlook MSG files
- Audio files (WAV, MP3) — requires ffmpeg
- YouTube video transcripts
