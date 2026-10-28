# Per-Theme Dark/Black Mode Overrides

**Date:** 2026-03-26
**Branch:** monday-new-theme

## Problem

Custom themes (`.slick`, `.editorial-amber`, `.editorial-terracotta`, `.editorial-espresso`) define light-mode color tokens. When a dark or black mode class is also applied to `:root`, the existing bare `dark-app-theme` / `black-app-theme` blocks override the surface/background tokens — but they use neutral blue-tinted darks that don't match each theme's character.

Result: all themes look the same in dark/black mode.

## Goal

Each custom theme gets its own dark and black overrides, expressed as compound CSS selectors (e.g. `:root.slick.dark-app-theme`). Colors are derived from each theme's palette character. The existing bare `dark-app-theme` / `black-app-theme` blocks remain unchanged for the Default theme.

## Approach

Compound CSS selectors — one block per theme × mode combination — appended to `globals.css`. No new abstractions, no JS changes.

```css
:root.slick.dark-app-theme { ... }
:root.slick.black-app-theme { ... }
:root.editorial-amber.dark-app-theme { ... }
/* etc. */
```

## Token Coverage

Each block sets the same 15 tokens as the existing bare dark/black blocks:

- `--primary-background-color`
- `--primary-surface-color`
- `--secondary-background-color`
- `--primary-text-color`
- `--secondary-text-color`
- `--icon-color`
- `--placeholder-color`
- `--link-color`
- `--ui-background-color`
- `--ui-background-hover-color`
- `--ui-border-color`
- `--layout-border-color`
- `--primary-selected-color`
- `--primary-selected-hover-color`
- `--primary-highlighted-color`

## Color Values (approved)

### Slick — cool neutral gray, no blue tint

| Token | Dark | Black |
|---|---|---|
| `--primary-background-color` | `#252528` | `#000000` |
| `--primary-surface-color` | `#303036` | `#111113` |
| `--secondary-background-color` | `#303036` | `#111113` |
| `--ui-background-color` | `#3e3e46` | `#1c1c20` |
| `--ui-background-hover-color` | `#4c4c56` | `#28282e` |
| `--ui-border-color` | `#4c4c56` | `#28282e` |
| `--layout-border-color` | `#303036` | `#111113` |
| `--primary-text-color` | `#d5d5d8` | `#e0e0e3` |
| `--secondary-text-color` | `#9090a0` | `#737380` |
| `--icon-color` | `#9090a0` | `#737380` |
| `--placeholder-color` | `#5a5a68` | `#4a4a56` |
| `--link-color` | `#9a9aa2` | `#909098` |
| `--primary-selected-color` | `#32323c` | `#1e1e26` |
| `--primary-selected-hover-color` | `#3a3a46` | `#242430` |
| `--primary-highlighted-color` | `#303036` | `#111113` |

### Amber — warm brown, red-brown hue

| Token | Dark | Black |
|---|---|---|
| `--primary-background-color` | `#231f18` | `#110e09` |
| `--primary-surface-color` | `#2e2820` | `#181410` |
| `--secondary-background-color` | `#2e2820` | `#181410` |
| `--ui-background-color` | `#3c3228` | `#221c16` |
| `--ui-background-hover-color` | `#4a3e30` | `#2e2620` |
| `--ui-border-color` | `#4a3e30` | `#2e2620` |
| `--layout-border-color` | `#2e2820` | `#181410` |
| `--primary-text-color` | `#ead6b8` | `#f0e4d0` |
| `--secondary-text-color` | `#a08868` | `#847060` |
| `--icon-color` | `#a08868` | `#847060` |
| `--placeholder-color` | `#6a5840` | `#584838` |
| `--link-color` | `#c97c30` | `#d4924a` |
| `--primary-selected-color` | `#362c20` | `#201810` |
| `--primary-selected-hover-color` | `#40342a` | `#281e16` |
| `--primary-highlighted-color` | `#2e2820` | `#181410` |

### Terracotta — warm rust/orange-brown

| Token | Dark | Black |
|---|---|---|
| `--primary-background-color` | `#211810` | `#100c07` |
| `--primary-surface-color` | `#2a2018` | `#151009` |
| `--secondary-background-color` | `#2a2018` | `#151009` |
| `--ui-background-color` | `#382818` | `#1c1510` |
| `--ui-background-hover-color` | `#463220` | `#271c14` |
| `--ui-border-color` | `#463220` | `#271c14` |
| `--layout-border-color` | `#2a2018` | `#151009` |
| `--primary-text-color` | `#ead4c4` | `#f0d8c8` |
| `--secondary-text-color` | `#9c8070` | `#8a6e60` |
| `--icon-color` | `#9c8070` | `#8a6e60` |
| `--placeholder-color` | `#685040` | `#564030` |
| `--link-color` | `#ea580c` | `#f97316` |
| `--primary-selected-color` | `#342418` | `#1c1210` |
| `--primary-selected-hover-color` | `#3e2e20` | `#241816` |
| `--primary-highlighted-color` | `#2a2018` | `#151009` |

### Espresso — rich coffee brown

| Token | Dark | Black |
|---|---|---|
| `--primary-background-color` | `#1c1610` | `#0e0b08` |
| `--primary-surface-color` | `#241e18` | `#141009` |
| `--secondary-background-color` | `#241e18` | `#141009` |
| `--ui-background-color` | `#302618` | `#1c1610` |
| `--ui-background-hover-color` | `#3c3020` | `#261e18` |
| `--ui-border-color` | `#3c3020` | `#261e18` |
| `--layout-border-color` | `#241e18` | `#141009` |
| `--primary-text-color` | `#e2d5c8` | `#ede0d2` |
| `--secondary-text-color` | `#9a8878` | `#887868` |
| `--icon-color` | `#9a8878` | `#887868` |
| `--placeholder-color` | `#645448` | `#504038` |
| `--link-color` | `#c4a882` | `#d4b896` |
| `--primary-selected-color` | `#2c2218` | `#181210` |
| `--primary-selected-hover-color` | `#382c20` | `#201816` |
| `--primary-highlighted-color` | `#241e18` | `#141009` |

## Files Changed

- `src/styles/globals.css` — append 8 new compound selector blocks (4 themes × 2 modes)

No changes to TypeScript, ThemeSwitcher, or App.tsx — the class application logic already works correctly.
