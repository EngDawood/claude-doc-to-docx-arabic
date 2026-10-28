# Claude Doc to Word (Arabic RTL and APA)

> A Claude plugin that turns a Claude Doc into a Word file ready to hand in: Arabic docs come out right-to-left in a Saudi thesis style, English docs in APA 7, pictures and charts included. Arabic version: [README.md](./README.md)

## Why

Claude Docs can export to `.docx`, but that export carries no right-to-left settings and drops pictures and charts. Arabic paragraphs come out left-aligned, tables read from the wrong side, and every chapter needs manual reformatting in Word. This plugin replaces that export with two scripts that build the file in the right direction and the right style from the start.

## Recommended workflow: write in Claude Docs, convert to Word at the end

When you work on a paper or report with Claude, ask it to write in a Claude Doc rather than straight into a Word file:

- **History is kept:** the doc records every edit, so you can go back to any earlier version.
- **Edits land in place:** Claude changes the paragraph or table you asked about inside the doc itself, and you can comment on any part for it to revise.
- **Word files are heavy to edit:** working in a Word file means that every change has Claude open the file, edit it, rebuild it and send it again, which is slower and more error-prone.

When the doc is done, this plugin turns it into a Word file in the right style with one request, using a script inside it that runs automatically. If you change the doc afterwards, run the conversion again for an updated Word file.

## What it does

Give Claude a Claude Doc link and ask for Word. It:

1. Reads the doc and exports each tab as markdown.
2. Fetches every picture in the doc and screenshots every chart.
3. Picks the script for the doc's language and builds the `.docx`.
4. Renders a body page, a table page, a list page and a picture page to check the layout.
5. Delivers the file named after the doc title.

If something goes wrong along the way, it names the cause, proposes a fix to the plugin itself, and applies it only after you approve and confirm the new output.

## Installation

### Claude Code

```
/plugin marketplace add EngDawood/claude-doc-to-docx-arabic
/plugin install doc-to-word-rtl@doc-to-docx-arabic
```

To update later:

```
/plugin marketplace update doc-to-docx-arabic
/plugin update doc-to-word-rtl@doc-to-docx-arabic
```

### Any agent that supports `skills`

```bash
npx skills add EngDawood/claude-doc-to-docx-arabic --skill doc-to-word-rtl
```

### Manual

Copy `skills/doc-to-word-rtl` into `.claude/skills/` (project) or `~/.claude/skills/` (user).

Requires Python 3 with `python-docx` (`pip install python-docx`). LibreOffice and `pdftoppm` are used only for the visual check.

## Usage

```
Convert this to Word: https://claude.ai/artifact/XXXX
```
```
حوّله إلى ورد بخط 14 وتباعد 1.5: https://claude.ai/artifact/XXXX
```

The scripts also run on their own, on any markdown file:

```bash
python3 skills/doc-to-word-rtl/scripts/md_to_docx_rtl.py chapter.md chapter.docx --images images.json
python3 skills/doc-to-word-rtl/scripts/md_to_docx_en.py paper.md paper.docx --paper a4
```

`images.json` lists the pictures in document order: `[{"alt": "...", "path": "..."}]`. Plain markdown images `![alt](path/to/image.png)` work too.

## House styles

### Arabic: `md_to_docx_rtl.py`

| Element | Default |
|---|---|
| Page | A4, 2.54 cm margins, centered page number |
| Body | Traditional Arabic 16, justified, line spacing 1.08 |
| Headings | `#` 22, `##` 18, `###` 16, bold. Centered when they open with المحور / الفصل / المراجع, right-aligned otherwise |
| Tables | Right-to-left columns, 14 pt, grey header row repeated on every page, rows never split |
| Captions | `جدول (n): ...` bold and centered, kept with its table |
| Lists | Each numbered list restarts at 1 |
| References | Hanging indent 0.635 cm |
| Pictures and charts | Centered, at most the text width, caption below |

Options: `--ar-font`, `--en-font`, `--size`, `--table-size`, `--color`, `--line`, `--margins`, `--paper a4|letter`, `--images`, `--keep-byline`, `--dir rtl|ltr|auto`.

### English: `md_to_docx_en.py` (APA 7)

| Element | Default |
|---|---|
| Page | US Letter, 2.54 cm margins, page number top right |
| Body | Times New Roman 12, double-spaced, first-line indent 1.27 cm |
| Headings | `#` title; `##` to `#####` map to APA levels 1 to 4 |
| Tables | `Table n` bold, title italic below; horizontal rules only |
| Notes | `Note.` in italics under the table |
| References | New page, hanging indent 1.27 cm |
| Pictures and charts | Centered, at most the text width, caption below |

Options: `--font`, `--size`, `--table-size`, `--line`, `--margins`, `--paper a4|letter`, `--images`, `--keep-byline`.

## Limits

- A chart arrives in Word as a static picture, not an editable chart.
- Comments in the doc are not carried over; footnotes become plain text.
- The visual check runs in LibreOffice, which may not have Traditional Arabic; it judges layout, not typeface.

## Repository layout

```
claude-doc-to-docx-arabic/
├── .claude-plugin/
│   ├── plugin.json              plugin manifest and version
│   └── marketplace.json         one-plugin marketplace for Claude Code
├── skills/doc-to-word-rtl/
│   ├── SKILL.md                 steps and house-style tables for Claude
│   └── scripts/
│       ├── md_to_docx_rtl.py    Arabic, right-to-left
│       └── md_to_docx_en.py     English, APA 7
├── README.md / README.en.md
└── LICENSE
```

## Contributing

Open a pull request, and bump `version` in `.claude-plugin/plugin.json` with every change to the scripts or `SKILL.md`; that is how Claude Code knows there is an update.

## License

MIT
