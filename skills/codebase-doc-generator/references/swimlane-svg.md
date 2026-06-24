# Vertical Swimlane — SVG Conventions for Raster/DOCX Output

Aligned with the `flowchart-maker` skill, BUT with one important difference: this output is
**rasterized to PNG and embedded into the .docx**, not displayed inline in chat.

⛔ DO NOT use CSS variables (`var(--color-...)`) — those only work in the chat widget with dark mode.
✅ Use **solid hex colors** and a **white background** so the raster looks consistent in Word.

> Swimlane lane labels and node text use the user's chosen OUTPUT language.

---

## Type: always vertical swimlane

Lane = vertical column. Flow goes top to bottom. Lane labels in the header row. Good for long, readable
processes. (Per requirement: every flow is drawn as a vertical swimlane.)

---

## Structure

```
Header row: y=0..40, fixed height 40px. Each lane label centered, bold.
Each column (lane): width = TOTAL_WIDTH / lane_count. Up to 4 lanes is comfortable.
Content area: y=40 downward.
Vertical divider between lanes: thin grey line.
viewBox: 0 0 <W> <H>, W = lane_count × column_width (e.g. 4×180=720), H = content + 40 buffer.
```

Comfortable column width: 170–190px. A node must not exceed its column (unless it deliberately crosses
lanes via an arrow).

---

## Shape per element

| Element | Shape |
|---------|-------|
| Start / End | `<ellipse>` (rx≈55, ry≈22) |
| Step/process | `<rect rx="8">`, height 44px (1 line) / 56px (title+subtitle) |
| Decision | `<polygon>` diamond (top, right, bottom, left), span ±36px |
| Sub-process | `<rect rx="8">` + a second rect 3px inside (double border) |

---

## SOLID color palette (hex, not CSS vars)

| Element | Fill | Stroke | Text |
|---------|------|--------|------|
| Start / End | `#CCFBF1` | `#0D9488` | `#134E4A` |
| User/customer step | `#EDE9FE` | `#7C3AED` | `#4C1D95` |
| System/backend step | `#DBEAFE` | `#2563EB` | `#1E3A8A` |
| Payment/transaction step | `#CCFBF1` | `#0D9488` | `#134E4A` |
| Operational/warehouse step | `#DCFCE7` | `#16A34A` | `#14532D` |
| Decision diamond | `#FEF3C7` | `#D97706` | `#78350F` |
| Error / fail / reject | `#FEE2E2` | `#DC2626` | `#7F1D1D` |
| Neutral / generic | `#F3F4F6` | `#6B7280` | `#374151` |
| Canvas background | `#FFFFFF` | — | — |
| Lane divider | — | `#E5E7EB` | — |
| Connector/arrow | — | `#6B7280` | — |

The element → color mapping is identical to `flowchart-maker` (Start/End teal, user purple, system blue,
decision amber, error coral, neutral grey).

---

## Text

- Node title: `font-size:13` `font-weight:600`.
- Subtitle/caption: `font-size:11` `fill:#6B7280`.
- Lane label (header): `font-size:13` `font-weight:700`, centered.
- Font family: `font-family="Arial, Helvetica, sans-serif"` (safe when rasterized).

---

## Arrows & connectors

- An arrow marker in `<defs>` is REQUIRED at the start of the SVG (color `#6B7280`).
- Within a lane: straight `<line>` point-to-point.
- Cross-lane: `<path fill="none">` with an L-bend (don't cut through another node's interior).
- All connectors `fill="none"`, `stroke="#6B7280"`, `marker-end="url(#arrow)"`.
- Branch labels (Yes/No): `<text font-size="11" fill="#6B7280">` near the arrow, not on the line center.
- Feedback loop / return notification: `stroke-dasharray="4 3"`.

---

## Checklist before rasterizing

- [ ] viewBox height = lowest element + 40px.
- [ ] No node escapes the viewBox / its column.
- [ ] Arrows don't pierce other nodes (use L-bends when needed).
- [ ] `<defs>` arrow marker present.
- [ ] All colors are solid hex, background `#FFFFFF`. NO `var(--...)`.
- [ ] Lane labels not clipped.
- [ ] No `onclick`/`sendPrompt` (those are chat-widget only, useless in a PNG).

---

## Skeleton example

```svg
<svg viewBox="0 0 720 520" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <marker id="arrow" markerWidth="8" markerHeight="8" refX="7" refY="4" orient="auto">
      <path d="M0,0 L8,4 L0,8 z" fill="#6B7280"/>
    </marker>
  </defs>
  <rect x="0" y="0" width="720" height="520" fill="#FFFFFF"/>
  <!-- lane headers -->
  <text x="90"  y="26" text-anchor="middle" font-family="Arial" font-size="13" font-weight="700">Customer</text>
  <text x="270" y="26" text-anchor="middle" font-family="Arial" font-size="13" font-weight="700">System</text>
  <text x="450" y="26" text-anchor="middle" font-family="Arial" font-size="13" font-weight="700">Admin</text>
  <line x1="180" y1="0" x2="180" y2="520" stroke="#E5E7EB"/>
  <line x1="360" y1="0" x2="360" y2="520" stroke="#E5E7EB"/>
  <!-- example start node -->
  <ellipse cx="90" cy="80" rx="55" ry="22" fill="#CCFBF1" stroke="#0D9488"/>
  <text x="90" y="84" text-anchor="middle" font-family="Arial" font-size="13" font-weight="600" fill="#134E4A">Start</text>
  <!-- ... -->
</svg>
```
