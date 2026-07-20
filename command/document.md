---
description: Setup document processing workflow for read/create DOCX, Excel, PDF. Creates temp folder, installs packages, runs tests, then cleans up.
agent: build
---

You are a document processing assistant. Follow these steps precisely:

## Step 1: Create temp folder

```powershell
$tempDir = "$env:LOCALAPPDATA\Temp\docs-temp"
New-Item -ItemType Directory -Path $tempDir -Force
Set-Location $tempDir
```

## Step 2: Init and install packages

```powershell
npm init -y
npm install docx xlsx pdf-lib officeparser mammoth @matbee/libreoffice-converter
```

## Step 3: Create sample input files

Create `create-samples.mjs`:

```javascript
import { Document, Packer, Paragraph, TextRun, Table, TableRow, TableCell, WidthType } from "docx";
import * as XLSX from "xlsx";
import { writeFileSync } from "fs";
import { PDFDocument } from "pdf-lib";

const doc = new Document({
  sections: [
    {
      children: [
        new Paragraph({ text: "Sample Document", heading: "HEADING_1" }),
        new Paragraph({ children: [new TextRun("This is a test document.")] }),
        new Table({
          rows: [new TableRow({ children: [new TableCell({ children: [new Paragraph("Name")] }), new TableCell({ children: [new Paragraph("Value")] })] }), new TableRow({ children: [new TableCell({ children: [new Paragraph("Item A")] }), new TableCell({ children: [new Paragraph("$100")] })] })],
        }),
      ],
    },
  ],
});
const docxBuffer = await Packer.toBuffer(doc);
writeFileSync("sample.docx", docxBuffer);

const wsData = [
  ["Product", "Q1", "Q2", "Total"],
  ["Widget", 15000, 20000, "=B2+C2"],
];
const ws = XLSX.utils.aoa_to_sheet(wsData);
const wb = XLSX.utils.book_new();
XLSX.utils.book_append_sheet(wb, ws, "Sheet1");
XLSX.writeFile(wb, "sample.xlsx");

const pdf = await PDFDocument.create();
const page = pdf.addPage();
page.drawText("Sample PDF Document", { x: 50, y: 700, size: 20 });
page.drawText("Created for testing document workflow", { x: 50, y: 670, size: 12 });
const pdfBytes = await pdf.save();
writeFileSync("sample.pdf", pdfBytes);

console.log("Created: sample.docx, sample.xlsx, sample.pdf");
```

Run: `node create-samples.mjs`

## Step 4: Test reading documents

Create `test-read.mjs`:

```javascript
import { OfficeParser } from "officeparser";

const docxAst = await OfficeParser.parseOffice("sample.docx");
console.log("\n=== DOCX Content ===");
console.log(docxAst.toText());

const xlsxAst = await OfficeParser.parseOffice("sample.xlsx");
console.log("\n=== XLSX Content ===");
console.log(xlsxAst.toText());

const pdfAst = await OfficeParser.parseOffice("sample.pdf");
console.log("\n=== PDF Content ===");
console.log(pdfAst.toText());
```

Run: `node test-read.mjs`

## Step 5: Test creating documents

Create `test-create.mjs`:

```javascript
import { Document, Packer, Paragraph, TextRun, HeadingLevel, Table, TableRow, TableCell, WidthType } from "docx";
import * as XLSX from "xlsx";
import { writeFileSync } from "fs";

const doc = new Document({
  sections: [
    {
      children: [
        new Paragraph({ text: "Quarterly Report", heading: HeadingLevel.HEADING_1 }),
        new Paragraph({ children: [new TextRun({ text: "Q1 2026 Summary", bold: true, size: 28 })] }),
        new Paragraph({ text: "" }),
        new Table({
          columnWidths: [3000, 3000, 3000],
          rows: [new TableRow({ children: [new TableCell({ children: [new Paragraph({ children: [new TextRun({ text: "Region", bold: true })] })], width: { size: 3000, type: WidthType.DXA } }), new TableCell({ children: [new Paragraph({ children: [new TextRun({ text: "Revenue", bold: true })] })], width: { size: 3000, type: WidthType.DXA } }), new TableCell({ children: [new Paragraph({ children: [new TextRun({ text: "Growth", bold: true })] })], width: { size: 3000, type: WidthType.DXA } })] }), new TableRow({ children: [new TableCell({ children: [new Paragraph("North America")] }), new TableCell({ children: [new Paragraph("$4.2M")] }), new TableCell({ children: [new Paragraph("+23%")] })] }), new TableRow({ children: [new TableCell({ children: [new Paragraph("Europe")] }), new TableCell({ children: [new Paragraph("$2.8M")] }), new TableCell({ children: [new Paragraph("+18%")] })] })],
        }),
      ],
    },
  ],
});
const docxBuffer = await Packer.toBuffer(doc);
writeFileSync("output-report.docx", docxBuffer);

const wsData = [
  ["Region", "Q1", "Q2", "Total"],
  ["North America", 4200000, 4800000, "=B2+C2"],
  ["Europe", 2800000, 3100000, "=B3+C3"],
  ["Asia Pacific", 1600000, 2100000, "=B4+C4"],
  ["Total", "=SUM(B2:B4)", "=SUM(C2:C4)", "=B5+C5"],
];
const ws = XLSX.utils.aoa_to_sheet(wsData);
const wb = XLSX.utils.book_new();
XLSX.utils.book_append_sheet(wb, ws, "Revenue");
XLSX.writeFile(wb, "output-data.xlsx");

console.log("Created: output-report.docx, output-data.xlsx");
```

Run: `node test-create.mjs`

## Step 6: Test DOCX to PDF conversion

Create `test-convert.mjs`:

```javascript
import { createWorkerConverter } from "@matbee/libreoffice-converter/server";
import { readFileSync, writeFileSync } from "fs";
import { fileURLToPath } from "url";
import { dirname, join } from "path";

const __dirname = dirname(fileURLToPath(import.meta.url));
const wasmPath = join(__dirname, "node_modules", "@matbee", "libreoffice-converter", "wasm");

console.log("Initializing LibreOffice WASM converter...");
const converter = await createWorkerConverter({ wasmPath });

console.log("Converting DOCX to PDF...");
const docxBuffer = readFileSync("output-report.docx");
const result = await converter.convert(docxBuffer, { outputFormat: "pdf" });
writeFileSync("output-report.pdf", result.data);
console.log("Created: output-report.pdf");

await converter.destroy();
```

Run: `node test-convert.mjs`

## Step 7: Verify outputs

```powershell
Get-ChildItem . -Filter *.* | Where-Object { $_.Name -match '\.(docx|xlsx|pdf)$' } | Select-Object Name, @{N='Size(KB)';E={[math]::Round($_.Length/1KB,1)}} | Format-Table -AutoSize
```

## Step 8: Cleanup

```powershell
Set-Location $env:LOCALAPPDATA\Temp
Remove-Item "docs-temp" -Recurse -Force -ErrorAction SilentlyContinue
Write-Host "Cleanup complete."
```

## Available Packages Summary

| Package                         | Use                                     |
| ------------------------------- | --------------------------------------- |
| `officeparser`                  | Read any format (PDF, DOCX, XLSX, PPTX) |
| `docx`                          | Create DOCX files                       |
| `xlsx`                          | Read/Create Excel files                 |
| `pdf-lib`                       | Create/fill PDF files                   |
| `mammoth`                       | DOCX to HTML/text                       |
| `@matbee/libreoffice-converter` | DOCX to PDF conversion (WASM)           |

## Quick Reference

```bash
# Read any document
npx officeparser file.docx --to=text

# Create DOCX
import { Document, Packer } from 'docx'

# Create XLSX
import * as XLSX from 'xlsx'

# Convert DOCX to PDF
import { createWorkerConverter } from '@matbee/libreoffice-converter/server'
const converter = await createWorkerConverter({ wasmPath })
const result = await converter.convert(buffer, { outputFormat: 'pdf' })
await converter.destroy()
```

$ARGUMENTS
