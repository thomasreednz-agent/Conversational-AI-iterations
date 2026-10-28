# Editorial Themes Design Spec

**Date:** 2026-03-24
**Status:** Approved

## Goal

Add three warm "editorial" CSS themes to demonstrate diversity alongside the existing `:root.slick` theme. All changes are CSS-only — no component changes, no new files beyond this spec.

## Approach

Three sibling class selectors on `:root`, mirroring `:root.slick`:

```
:root.editorial-amber
:root.editorial-terracotta
:root.editorial-espresso
```

To activate a theme, add the class to `<html>` (`:root`) the same way `slick` would be applied.

## Typography

Shared across all three editorial themes.

- **Titles** (`--title-font-family`) → `"Lora", serif` — a classical editorial serif
- **Body/UI** (`--font-family`) → `"Inter", sans-serif` — clean, neutral sans

**Font loading:** Vite does not resolve remote `@import url(...)` in CSS files alongside bare-specifier imports. Load Google Fonts via a `<link>` tag in `index.html`:

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Lora:ital,wght@0,400;0,600;1,400&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
```

Inter includes weights 400–700 to cover bold UI labels used by Vibe components.

**Font token declaration:** Declared once in a shared selector group at the top of the editorial theme section in `globals.css`:

```css
:root.editorial-amber,
:root.editorial-terracotta,
:root.editorial-espresso {
  --font-family: "Inter", sans-serif;
  --title-font-family: "Lora", serif;
}
```

Each individual theme block below then only overrides color and border-radius tokens.

## Color Tokens

### :root.editorial-amber

Earthy, confident. Amber/gold accent, cream surfaces.

| Token | Value |
|---|---|
| `--primary-color` | `#b45309` |
| `--primary-hover-color` | `#92400e` |
| `--primary-selected-color` | `#fef3c7` |
| `--primary-selected-hover-color` | `#fde68a` |
| `--primary-highlighted-color` | `#fffbeb` |
| `--primary-surface-color` | `#fdf8f0` |
| `--allgrey-background-color` | `#fdf8f0` |
| `--grey-background-color` | `#fdf8f0` |
| `--ui-background-color` | `#f5ead8` |
| `--ui-background-hover-color` | `#edd9b8` |
| `--layout-border-color` | `#e8e0d5` |
| `--ui-border-color` | `#d5c9b8` |
| `--primary-text-color` | `#1c1309` |
| `--secondary-text-color` | `#78716c` |
| `--icon-color` | `#78716c` |
| `--placeholder-color` | `#78716c` |
| `--link-color` | `#b45309` |
| `--border-radius-small` | `4px` |
| `--border-radius-medium` | `8px` |
| `--border-radius-big` | `16px` |

### :root.editorial-terracotta

Bold, editorial-magazine. Rust/terracotta accent, warm off-white surfaces. Sharpest radius.

| Token | Value |
|---|---|
| `--primary-color` | `#c2410c` |
| `--primary-hover-color` | `#9a3412` |
| `--primary-selected-color` | `#ffedd5` |
| `--primary-selected-hover-color` | `#fed7aa` |
| `--primary-highlighted-color` | `#fff7ed` |
| `--primary-surface-color` | `#fdf4ef` |
| `--allgrey-background-color` | `#fdf4ef` |
| `--grey-background-color` | `#fdf4ef` |
| `--ui-background-color` | `#f5e4d8` |
| `--ui-background-hover-color` | `#edcfb8` |
| `--layout-border-color` | `#e8ddd5` |
| `--ui-border-color` | `#d5c4b8` |
| `--primary-text-color` | `#1c0e08` |
| `--secondary-text-color` | `#78716c` |
| `--icon-color` | `#78716c` |
| `--placeholder-color` | `#78716c` |
| `--link-color` | `#c2410c` |
| `--border-radius-small` | `2px` |
| `--border-radius-medium` | `4px` |
| `--border-radius-big` | `8px` |

### :root.editorial-espresso

Sophisticated, neutral-warm. Deep brown accent, parchment surfaces. Softest radius.

| Token | Value |
|---|---|
| `--primary-color` | `#3d2a1e` |
| `--primary-hover-color` | `#2a1c12` |
| `--primary-selected-color` | `#f0e8e0` |
| `--primary-selected-hover-color` | `#e2d4c8` |
| `--primary-highlighted-color` | `#faf7f4` |
| `--primary-surface-color` | `#f7f3ef` |
| `--allgrey-background-color` | `#f7f3ef` |
| `--grey-background-color` | `#f7f3ef` |
| `--ui-background-color` | `#ede4da` |
| `--ui-background-hover-color` | `#e0d0c0` |
| `--layout-border-color` | `#e2d8cf` |
| `--ui-border-color` | `#cfc4b8` |
| `--primary-text-color` | `#1c1309` |
| `--secondary-text-color` | `#7c6f65` |
| `--icon-color` | `#7c6f65` |
| `--placeholder-color` | `#7c6f65` |
| `--link-color` | `#3d2a1e` |
| `--border-radius-small` | `8px` |
| `--border-radius-medium` | `14px` |
| `--border-radius-big` | `28px` |

## Border Radius Rationale

Each theme uses radius as part of its personality:

| Theme | Small | Medium | Big | Feel |
|---|---|---|---|---|
| Amber | `4px` | `8px` | `16px` | Structured, editorial |
| Terracotta | `2px` | `4px` | `8px` | Sharp, bold |
| Espresso | `8px` | `14px` | `28px` | Soft, sophisticated |

## Note on `--title-font-family`

This token is set by the `:root.slick` theme (line 10 of `globals.css`) and is not a standard Vibe token. Whether it actually renders Lora on headings depends on whether any component or CSS rule reads this token. If nothing consumes it, Lora will load but never appear. This is a known gap — verifying or wiring this token to heading elements is out of scope for this spec but should be a follow-up if Lora does not appear on titles at runtime.

## What Does NOT Change

- Status badge colors (`--color-done-green`, `--color-stuck-red`, etc.) — semantic, unchanged
- Product theme classes (`.dev`, `.service`, `.crm`) — not edited
- `productConfig.ts` — no changes needed
- Any component files — CSS-only change

## Files Changed

- `index.html` — three `<link>` tags added for Google Fonts (preconnect + stylesheet)
- `src/styles/globals.css` — one shared font selector group + three `:root.editorial-*` color/radius blocks appended
