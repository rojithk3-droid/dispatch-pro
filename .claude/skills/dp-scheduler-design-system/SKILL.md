---
name: dp-scheduler-design-system
description: The Dispatch Pro scheduler design system established in dp-scheduler.html — 1:1 px (no zoom), Inter 12px UI, the fblue/navy token set, status palette, driver lane cards, bars, hover cards, modals/windows, the pay-profile-style dropdown, buttons, tabs and toasts. Use whenever building, restyling or extending anything in dp-scheduler.html (or a sibling planner/board page), whenever a Figma link is posted for it, and whenever a colour, radius, size or spacing has to be chosen for a scheduler screen. Read it before inventing any value.
---

# Dispatch Pro scheduler design system

Source of truth: `dp-scheduler.html` (single file, vanilla JS, no build). Everything below is already in that file — reuse the class, do not re-derive.

Unlike `newdpdesignsystem` (shadcn at 80% on a 1.25px grid) the scheduler paints in **real px at zoom 1**. Fonts are Inter 11–13px; the base `body` is Arial 13px, and the modern surfaces (lane cards, modals, windows, dropdowns) override it with `Inter,"Segoe UI",Arial,sans-serif`.

## Tokens (`:root`)

| Role | Token / value |
|---|---|
| Brand navy / button blue | `--navy #1b4f7e`, `--btn #1b5aa5` |
| Interactive blue (modern surfaces) | `--fblue #0d6efd`; hover/tint `#eef4fb`, selected tint `#eaf2ff`, focus ring `0 0 0 3px rgba(13,110,253,.12)` |
| Ink | `#14161f` primary, `#6b6f80` muted, `#8a8f9c` faint, `#4b4f66` secondary |
| Lines | `#e3e7ee` card/modal borders, `#d7dbdf` inputs, `#eceff3` row dividers |
| Page / surfaces | `--page #eef2f5`, cards `#fff`, header strips `#f6f8fb` |
| Status | green `#19c936`, orange `#ff6d10`, red `#e0272c`, blue `#1f7fd6`, grey `#8a9099`, amber `#c98a00`, each with `-bg` / `-bd` tints |
| ELD duty chips | Driving `#19c936`, On duty `#ff6d10`, Off `#f00`, Sleeper `#000` |
| Money / good | `#178a3c` (CPM, positive) |

Radii: inputs 7–8px, cards 10–12px, modals 12–14px, chips 4–6px, pills 10–13px, avatars 50%.

## Components

- **Lane card (compact):** `.lane > .who` — 40px face (`.av`, photo or initials), name 13px/600, duty chip (`.duty d-XX`, 9px/700), home line 10px muted, kebab (`.kebab`, 20px) centred on the right.
- **Lane card (expanded, `.xcard`):** 309×149, radius 19, 115px face (`.xph`), absolutely positioned name / city / duty / truck / trailer / Reserved tag; kebab `.xkeb` at `top:62px`.
- **Board bars (`.blk`):** 24px tall, `top:19px`, status colours via `k-*` classes; stacked clusters (`.cl.multi`) step down 3px per bar; opened stacks 40px per bar. Stop line (`.stops`, expanded only) sits under the deepest bar (66px single stack; `44px + i*40px` when open).
- **Fuel pin (`.fuel`):** 14px box, `bottom:15px`, sits between bars and the HOS strip (`.hos`, bottom 6px, 8px high). Never reuse the class name `.hos` for text — it is position:absolute.
- **Face hover card (`#dcard`):** appears only when hovering a face circle (`.av` / `.xph`), left-aligned under it; 28px round icon buttons (call / text / ProChat / email / history), 14px glyphs, padding `6px 8px`. No text line in it. Clicking the face opens driver-view. Hidden in Truck mode.
- **Black tooltip:** `[data-tip]::after` — `#000` bg, white 10.5px text, radius 5, below the element.
- **Driver | Truck toggle (`.laneseg`):** 2-button segmented control in the board header; `LANEBY` global; Truck mode swaps lane identity to the truck number with the driver as subtitle.
- **Dropdowns (every `<select>`):** auto-enhanced by the script at the end of the file into `.sdd` wrapper + `.sdd-t` trigger + one fixed `.sdd-p` panel. Trigger 12px, border `#d7dbdf`, radius 7, chevron at 50% opacity that flips open; open state `fblue` border + 3px ring. Items `.sdd-i`: padding `6px 28px 6px 8px`, radius 5, hover `#eef4fb` / `--fblue`, chosen row weight 500 with a check at the right. The native select stays (hidden) so `.value` and `change` keep working; `select.value = x` re-syncs through a patched setter. The board's own `.dd` menus (`.ddmenu/.ddopt`) are restyled to match. Toolbar selects inside `.etool` need explicit wrapper widths.
- **Modals / windows:** `.fp` panels (title `head()`, `.fb` body, `.ff` footer with right-aligned buttons), floating `openW` windows (e.g. email, 720px), `#addm` Add-to-schedule (520px, tab strip `.atabs`, footer `.af2`). Primary button `.fbtn` fblue; ghost `.fbtn.ghost`; destructive red tints only for errors (`.ferr`, `.bad`).
- **Fields:** `.fi/.fsel/.fta` 32px, radius 7, `#d7dbdf`; labels `.fl` 10px/700 uppercase `.09em`, `#6b6f80`; invalid = `.bad` (red border) + `.ferr` line.
- **Search-and-select (broker):** `.bsel` wrapper, `.bsl` list under the input, `.bsi` rows (name 12px/600, sub 10.5px muted); must pick from the list, picking auto-fills dependent fields (read-only MC#).
- **Tables (`.ftbl`):** 11.5px, th 9.5px/700 uppercase `.08em`, sticky head; inside `#ffBody` (Find/Match Freight) cells wrap and the table never scrolls sideways; last column stacks its buttons.
- **Cards in lists (`.aload`):** radio, name 12.5px/700, route muted, right column rate (bold), `miles · hours`, and CPM in green (`$x.xx/mi`).
- **Trip card (`.ftrip`):** 262px side card, header `.th`, stop timeline `.fstop`, KPI strip `.fstat`, rate block `.frate`, progress `.fprog`.
- **Toasts:** `toastMsg(html)` / `toast()`.

## Rules learned the hard way

1. Check for global class collisions before naming: `.hos`, `.more`, `.sw`, `.sel`, `.av` are all used by the board. Scope new rules (`.ftrip .th .hos`, `#addm …`).
2. `.etool button` styles every button in the email toolbar — new controls there must override it explicitly.
3. A fixed panel inside a transformed parent (`.fp` uses translate(-50%,-50%)) is positioned against that parent — append floating panels to `document.body`.
4. MutationObservers that rewrite their own text loop forever — only write when the value changed.
5. Absolute-positioned stacks: any new line under a bar must clear `19px + (n-1)*3px + 24px`.
6. Dropdown height/width are copied from the native select's computed style at enhance time; selects inside hidden tabs fall back to 32px / 100%.

## Workflow

1. Find the nearest existing component above and copy its classes.
2. Add CSS next to the related block in the single `<style>`; add JS to the matching section (each flow has a `/* ---------- name ---------- */` header).
3. Verify in the browser at 1400px+ wide; check expanded rows, stacked bars and both lane modes (Driver / Truck).
4. Commit `dp-scheduler.html` only; it is published at https://rojithk3-droid.github.io/dispatch-pro/dp-scheduler.html.
