---
name: html-prototype-studio
description: >-
  Wrap a single-page HTML product prototype in a three-mode shell (Preview /
  Edit / Annotate): place-then-write pins, editable copy, export for QA and
  eng. Use when a prototype is structurally clear and the user wants preview /
  edit / annotate delivery, pin annotations, or mode-based HTML export.
---

# HTML Prototype Studio (three modes)

Add a self-contained delivery shell to a **clear** single-page HTML product prototype: dark top toolbar + right annotation panel + local edit / export. Product can tweak copy; QA and eng can review against requirements.

## When to use

- A single-page HTML prototype needs clear interaction / edge-case notes for QA and eng
- Product wants to edit copy in the browser, place pins, write notes, mark resolved
- You need mode-based export: clean preview / annotated / edited+annotated

## Deliverables

- One self-contained `.html` (double-click to open)
- **Dark top toolbar** (visually distinct from page body), with:
  - Modes: `Preview` / `Edit` / `Annotate`
  - Version chip: Original / Edited · Export · Clear cache
  - Edit tools: `+ Add annotation`, Reset copy & notes
- Toolbar sits **full-width under the banner**, not floating over the annotation list or body
- Right annotation panel: numbers match orange pins; click a row to focus + flash
- Annotation copy states behavior / boundaries; unwired areas say “demo-only”
- Optional statuses: `open` (orange) / `resolved` (grey, reopen) / delete (hard delete)

## Three modes

| Mode | Behavior |
|------|----------|
| Preview | Full-width page view; no pins, no right panel |
| Edit | Edit page copy; place pins + notes; Place / Resolved / Delete; auto-saves to localStorage |
| Annotate | Read-only pins + panel for review |

### Add annotation flow (place, then write)

1. Edit → click top-bar **+ Add annotation**
2. Placement mode (crosshair + **grey** ghost pin)
3. Click the target on the page → store durable selector + offsets `ox` / `oy`
4. Dialog for title / description / group → submit turns the pin **orange**
5. After add / delete, renumber `1..n` by `PROTO_ANNOTATION_GROUPS` region order

Each note can **Place** again; **Position details** (anchor / ox / oy) stay **collapsed by default**.

### Export (follows current mode)

- Preview → `*.preview.html` (clean)
- Annotate → `*.annotated.html` (panel + orange pins)
- Edit → `*.edited-annotated.html` (current copy + annotations, read-only shell)

## Implementation steps

```
Task Progress:
- [ ] 1. Read the product HTML; define region groups (header / hero / …)
- [ ] 2. Use `examples/landing-modes.html` as the gold sample; extract chrome (banner + toolbar + panel + markers + three-mode JS)
- [ ] 3. Put product body inside `#protoMainScroll`; keep stable anchor ids / `data-anno`
- [ ] 4. Fill `PROTO_ANNOTATION_GROUPS` + `PROTO_ANNOTATIONS` (group / anchor / title / desc; optional pos / ox / oy / status)
- [ ] 5. Walkthrough: toolbar does not cover panel, mode switch, grey→orange pin, region renumber, resolved / delete, export, clear cache
- [ ] 6. Hand the finished path to the user; explain Edit to place pins, then Annotate for eng review
```

### Gold sample

Copy and trim: `examples/landing-modes.html`. **Do not** rewrite the chrome from scratch; port the verified CSS / JS and swap body + annotation data.

### Data fields

**Group** `{ key, label }`.

**Item**:

| Field | Required | Notes |
|-------|----------|-------|
| id | ✓ | Number; renumbered by region order after add / delete |
| group | ✓ | Group key |
| anchor | ✓ | CSS selector; prefer `#id` / `[data-anno]` |
| title / desc | ✓ | Short title + 1–2 sentence note |
| ox, oy | | Pixel offset from anchor top-left (written on place) |
| pos | | When no offset: `left` or default top-right |
| status | | `open` (default) \| `resolved` |

### Copy guidelines

- Spell out trigger, expected result, empty / permission states; unwired → “demo-only”
- Avoid implementation jargon; do not promise capabilities the page does not show

### Walkthrough checklist

- [ ] Dark toolbar, distinct from body; does not cover the annotation list
- [ ] Preview: no pins / panel; Edit: editable + placeable; Annotate: read-only
- [ ] Grey placement preview → orange on submit; Esc cancels
- [ ] Numbers follow region order; resolved turns grey and can reopen
- [ ] Position details collapsed by default; actions on their own row inside the card
- [ ] All three export files work; Clear cache / Reset restore originals
