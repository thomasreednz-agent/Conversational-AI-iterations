# Sleek Theme Design Spec

**Date:** 2026-03-24
**Status:** Approved
**Approach:** Global CSS token overrides in `globals.css` — no component changes, no new files.

## Goal

Replace monday.com's default blue-tinted palette with a cool, crisp near-black theme inspired by Apple/Linear. All changes are via CSS custom property overrides on `:root`. The existing product theme classes (`.dev`, `.service`, `.crm`, `.work_management`) remain untouched — their CSS rules are not edited. Note: because the `:root` overrides apply globally, the visual appearance of product themes will shift (e.g. `.dev`'s surface color stays green-tinted but its buttons will become near-black). This is intentional.

## Typography

Replace the current Poppins (title) + Figtree (body) pairing with **Geist** as a single unified family for both title and body text.

**Font source:** Geist is NOT on Google Fonts. Use `@fontsource/geist` (npm package, self-hosted, no external request):

```bash
yarn add @fontsource/geist
```

Then import in `globals.css`:

```css
@import "@fontsource/geist/400.css";
@import "@fontsource/geist/500.css";
@import "@fontsource/geist/600.css";
@import "@fontsource/geist/700.css";
```

**Remove** the existing Poppins + Figtree `@import url(...)` from `globals.css`.

**Tokens to override** (both confirmed to exist in Vibe):

- `--font-family` → `"Geist", sans-serif` (body text, labels, UI)
- `--title-font-family` → `"Geist", sans-serif` (headings h1–h4, set via `--font-h1` through `--font-h4` which reference this token)

## Primary Color — monday blue → near-black

Every place Vibe uses `--primary-color` (buttons, focus rings, checkboxes, active states) switches to near-black.

| Token | Old value | New value |
|---|---|---|
| `--primary-color` | `#0073ea` | `#1d1d1f` |
| `--primary-hover-color` | `#0060b9` | `#3a3a3c` |
| `--primary-selected-color` | `#cce5ff` | `#e8e8ed` |
| `--primary-selected-hover-color` | `#aed4fc` | `#d2d2d7` |
| `--primary-highlighted-color` | `#f0f7ff` | `#f9f9f9` |

## Surface Hierarchy — strip all blue tints

The surface stack from outermost to innermost. `--primary-background-color` and `--secondary-background-color` are already `#ffffff` in Vibe and require no override.

| Token | Role | Old value | New value |
|---|---|---|---|
| `--primary-surface-color` | App wrapper shell | `#eceff8` | `#f2f3f5` |
| `--allgrey-background-color` | Mid-tier surface fill | `#f6f7fb` | `#f5f5f7` |
| `--grey-background-color` | Mid-tier surface fill (separate token, same change) | `#f6f7fb` | `#f5f5f7` |
| `--ui-background-color` | UI element fills (chips, inputs) | `#e7e9ef` | `#e8e8ed` |
| `--ui-background-hover-color` | UI element hover | `#d8d9e0` | `#d2d2d7` |

## Borders — two distinct levels

| Token | Role | Old value | New value |
|---|---|---|---|
| `--layout-border-color` | Layout dividers (table rows, pane edges) — lighter | `#d0d4e4` | `#e5e5e5` |
| `--ui-border-color` | UI element borders (inputs, chips, buttons) — darker | `#c3c6d4` | `#d2d2d7` |

## Text & Icons — remove blue tint

| Token | Old value | New value |
|---|---|---|
| `--primary-text-color` | `#323338` | `#1d1d1f` |
| `--secondary-text-color` | `#676879` | `#636366` |
| `--icon-color` | `#676879` | `#636366` |
| `--placeholder-color` | `#676879` | `#636366` |
| `--link-color` | `#1f76c2` | `#1d1d1f` |

**Note on `--link-color`:** Setting this to `#1d1d1f` (same as body text) makes links visually indistinguishable from body text unless underlined. This is intentional for the clean aesthetic — links in this board UI are primarily navigation elements that are distinguished by context and underline styling. If any standalone inline links exist without underlines, they will need a targeted patch (out of scope for approach A).

## What Does NOT Change

- `--primary-background-color` / `--secondary-background-color` — already `#ffffff`, no override needed
- `--positive-color` / `--negative-color` / `--warning-color` — semantic status colors, not brand
- `--brand-color` / `--brand-hover-color` etc. — represent monday's brand identity
- `--color-done-green`, `--color-stuck-red`, `--color-working_orange` — board status badge colors
- All product theme class CSS rules (`.dev`, `.service`, `.crm`) — not edited (see note above about visual effect)

## Implementation

**Step 1:** Install Geist font package:

```bash
yarn add @fontsource/geist
```

**Step 2:** Update `src/styles/globals.css` — replace the Poppins + Figtree `@import url(...)` line with Geist imports, add a `:root` override block after the Vibe token import:

```css
/* Replace existing Google Fonts @import url(...) with: */
@import "@fontsource/geist/400.css";
@import "@fontsource/geist/500.css";
@import "@fontsource/geist/600.css";
@import "@fontsource/geist/700.css";

:root {
  /* Typography — Geist replaces Poppins (title) + Figtree (body) */
  --font-family: "Geist", sans-serif;
  --title-font-family: "Geist", sans-serif;

  /* Primary color — near-black replaces monday blue */
  --primary-color: #1d1d1f;
  --primary-hover-color: #3a3a3c;
  --primary-selected-color: #e8e8ed;
  --primary-selected-hover-color: #d2d2d7;
  --primary-highlighted-color: #f9f9f9;

  /* Surfaces — neutral cool grays, blue tints removed */
  --primary-surface-color: #f2f3f5;
  --allgrey-background-color: #f5f5f7;
  --grey-background-color: #f5f5f7;
  --ui-background-color: #e8e8ed;
  --ui-background-hover-color: #d2d2d7;

  /* Borders — two distinct levels */
  --layout-border-color: #e5e5e5;
  --ui-border-color: #d2d2d7;

  /* Text & icons — neutral, blue tint removed */
  --primary-text-color: #1d1d1f;
  --secondary-text-color: #636366;
  --icon-color: #636366;
  --placeholder-color: #636366;
  --link-color: #1d1d1f;
}
```

## Files Changed

- `package.json` / `yarn.lock` — `@fontsource/geist` dependency added
- `src/styles/globals.css` — font import replaced, `:root` block added

## Out of Scope

- Dark mode — separate future spec
- Component-level CSS patches — can be layered on top if tokens don't reach certain elements
- `productConfig.ts` changes — not needed for approach A
- Inline link accessibility patches — tracked as a known trade-off (see link-color note)
