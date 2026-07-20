---
name: document
description: "Use when user mentions docx, xlsx, pdf, word document, excel spreadsheet, document conversion, or file format conversion. Covers creating, reading, converting DOCX/XLSX/PDF files using npm packages."
---

# Document Processing Skill

You can process Microsoft Office documents (DOCX, XLSX) and PDFs programmatically. The available packages and their purposes:

## Reading Documents

| Format | Package | Code |
|--------|---------|------|
| Any format | `officeparser` | `OfficeParser.parseOffice(path) → ast.toText()` |
| DOCX | `mammoth` | `mammoth.extractRawText({path})` |
| XLSX | `xlsx` | `XLSX.readFile(path)` |
| PDF | `pdf-lib` | `PDFDocument.load(buffer)` |

## Creating Documents

### DOCX (using `docx` package)
```javascript
import { Document, Packer, Paragraph, TextRun, HeadingLevel, Table, TableRow, TableCell, WidthType } from 'docx';
import { writeFileSync } from 'fs';

const doc = new Document({
  sections: [{
    children: [
      new Paragraph({ text: 'Title', heading: HeadingLevel.HEADING_1 }),
      new Paragraph({ children: [new TextRun({ text: 'Bold text', bold: true })] }),
      new Table({
        columnWidths: [3000, 3000],
        rows: [
          new TableRow({ children: [
            new TableCell({ children: [new Paragraph('Header')], width: { size: 3000, type: WidthType.DXA } }),
            new TableCell({ children: [new Paragraph('Value')], width: { size: 3000, type: WidthType.DXA } }),
          ]}),
        ]
      })
    ]
  }]
});
const buffer = await Packer.toBuffer(doc);
writeFileSync('output.docx', buffer);
```

**Gotchas:**
- Page size defaults to A4. For US Letter: `page: { size: { width: 12240, height: 15840 } }`
- `Packer.toBuffer()` is async — must `await`
- `ImageRun` requires `type:` (`"png"`, `"jpg"`, etc.)
- `PageBreak` must be inside a `Paragraph`
- Use separate `Paragraph` elements instead of `\n`
- Tables need `columnWidths` on table AND `width` on every cell (`WidthType.DXA`)
- Table shading: use `ShadingType.CLEAR`, never `SOLID`
- Lists: use `numbering` config with `LevelFormat.BULLET`, never literal `•`

### XLSX (using `xlsx` / SheetJS)
```javascript
import * as XLSX from 'xlsx';
import { writeFileSync } from 'fs';

const data = [['Name', 'Value'], ['Item', 100]];
const ws = XLSX.utils.aoa_to_sheet(data);
const wb = XLSX.utils.book_new();
XLSX.utils.book_append_sheet(wb, ws, 'Sheet1');
XLSX.writeFile(wb, 'output.xlsx');
```

**Features:** Formulas (`=B2+C2`), multiple sheets, JSON input via `json_to_sheet`
**Note:** Formulas are written but not calculated — results appear when opened in Excel.

## Converting DOCX to PDF

Uses `@matbee/libreoffice-converter` (WASM, no LibreOffice install needed):
```javascript
import { createWorkerConverter } from '@matbee/libreoffice-converter/server';
import { readFileSync, writeFileSync } from 'fs';
import { fileURLToPath } from 'url';
import { dirname, join } from 'path';

const __dirname = dirname(fileURLToPath(import.meta.url));
const wasmPath = join(__dirname, 'node_modules', '@matbee', 'libreoffice-converter', 'wasm');
const converter = await createWorkerConverter({ wasmPath });
const result = await converter.convert(readFileSync('input.docx'), { outputFormat: 'pdf' });
writeFileSync('output.pdf', result.data);
await converter.destroy();
```

Supports: doc, docx, xls, xlsx, ppt, pptx, odt, ods, odp, rtf, txt, html, csv → pdf, docx, odt, xlsx, and more.

## Quick Commands

```bash
# Read any file
npx officeparser file.docx --to=text

# Convert DOCX to Markdown
npx officeparser report.docx --to=md --output=report.md
```

## Installation

```bash
npm install docx xlsx pdf-lib officeparser mammoth @matbee/libreoffice-converter
```
