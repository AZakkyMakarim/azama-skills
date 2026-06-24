# DOCX Build Pipeline

Final output = **a single `.docx`** with swimlanes (PNG) embedded. Runs in Claude Code.

Pipeline: render swimlane SVG → convert to PNG → build docx (Node `docx`) → post-process for Word compat.

> Document text is written in the user's chosen OUTPUT language.

---

## 1. Setup

```bash
npm init -y
npm install docx @resvg/resvg-js
# fallback for SVG→PNG if resvg has issues:
# pip install cairosvg   (then: cairosvg in.svg -o out.png -s 2)
```

`docx` = builds the document. `@resvg/resvg-js` = rasterizes SVG→PNG purely in Node (no headless browser).

---

## 2. SVG → PNG

Each flow is stored as an SVG string (follow `references/swimlane-svg.md`), then:

```js
const { Resvg } = require('@resvg/resvg-js');
const fs = require('fs');

function svgToPng(svgString, outPath, scale = 2) {
  const resvg = new Resvg(svgString, {
    fitTo: { mode: 'zoom', value: scale }, // 2x for sharpness in Word
    background: '#FFFFFF',
    font: { defaultFontFamily: 'Arial' },
  });
  const png = resvg.render().asPng();
  fs.writeFileSync(outPath, png);
  // also keep the original viewBox dimensions for docx sizing
}
```

Record the SVG viewBox width/height → used for `ImageRun` so the ratio stays correct.
Fallback if resvg fails: `cairosvg in.svg -o out.png -s 2` via `child_process`.

---

## 3. Build DOCX (Node `docx`)

Minimal pattern to embed a swimlane image:

```js
const { Document, Packer, Paragraph, HeadingLevel, TextRun, ImageRun,
        Table, TableRow, TableCell, WidthType, AlignmentType } = require('docx');
const fs = require('fs');

function swimlaneImage(pngPath, vbW, vbH) {
  const maxW = 600;                       // px to fit the A4 content area
  const w = Math.min(maxW, vbW);
  const h = Math.round(w * (vbH / vbW));  // preserve ratio
  return new Paragraph({
    alignment: AlignmentType.CENTER,
    children: [ new ImageRun({
      data: fs.readFileSync(pngPath),
      transformation: { width: w, height: h },
    }) ],
  });
}
```

The document structure follows `references/doc-template.md`:
Part I (system) → Part II (per page). Use `HeadingLevel.HEADING_1..3` for headings.
Tables (input, page map, story mapping) use `Table`/`TableRow`/`TableCell`.

Mark `⚠️ NEEDS CONFIRMATION` with a highlighted `TextRun` (e.g. `highlight: 'yellow'`) so the user can
find them easily during review.

```js
const doc = new Document({
  styles: { default: { document: { run: { font: 'Calibri', size: 22 } } } },
  sections: [{ children: [ /* paragraphs & tables & swimlaneImage(...) */ ] }],
});
Packer.toBuffer(doc).then(b => fs.writeFileSync('system-documentation.docx', b));
```

---

## 4. Known Fixes — MS Word Compatibility

Issues already encountered + how to handle them:

1. **Duplicate heading style IDs** — don't redefine heading styles with the same ID; use the built-in
   `HeadingLevel` or ensure `styleId` is unique.
2. **Missing "Normal" style** — some generated files make Word complain because the `Normal` style is
   absent. Ensure a default document style is defined (see the `styles.default` block above).
3. **Post-process via Python (optional)** — if Word still refuses to open, unzip the `.docx`, fix
   `word/styles.xml` (add `Normal`, dedupe styleId), re-zip. Simple post-process scaffold:

```python
import zipfile, shutil, re, os
src, dst = 'system-documentation.docx', 'system-documentation-fixed.docx'
shutil.copy(src, dst)
# (open styles.xml, patch Normal/dedupe styleId as needed, then write back)
```

---

## 5. Output

- Default name: `system-documentation.docx` (or match the project name).
- Place it where the user wants. In Claude Code, tell the user the path.
- Include a summary: number of documented pages, number of stories, number of open gaps.
