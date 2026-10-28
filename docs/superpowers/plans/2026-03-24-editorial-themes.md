# Editorial Themes Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add three warm CSS editorial themes (amber, terracotta, espresso) as `:root.editorial-*` class selectors alongside the existing `:root.slick` theme.

**Architecture:** All changes are CSS-only — two files touched. Google Fonts (Lora + Inter) loaded via `<link>` tags in `index.html`. Token overrides appended to `src/styles/globals.css` as a shared font group plus three individual theme blocks.

**Tech Stack:** CSS custom properties, Vibe design tokens, Google Fonts, Vite

---

## File Map

| File | Change |
|---|---|
| `index.html` | Add 3 `<link>` tags: 2 preconnect + 1 Google Fonts stylesheet |
| `src/styles/globals.css` | Append 1 shared font selector group + 3 `:root.editorial-*` theme blocks |

No new files created. No TypeScript or component files touched.

---

### Task 1: Add Google Fonts `<link>` tags to `index.html`

**Files:**
- Modify: `index.html:4-7` (inside `<head>`)

- [ ] **Step 1: Add the preconnect and stylesheet links**

Open `index.html`. Inside `<head>`, after the existing `<meta>` tags and before `</head>`, add:

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Lora:ital,wght@0,400;0,600;1,400&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
```

The result should look like:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>🖤 Boards - monday.com template (experimental)</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Lora:ital,wght@0,400;0,600;1,400&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>
```

- [ ] **Step 2: Verify fonts load in the browser**

Run `yarn dev` and open the app. Open DevTools → Network → filter by "fonts.googleapis". You should see the Google Fonts request. No console errors.

You do NOT need to activate a theme yet — just confirm the fonts are fetched.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: load Lora and Inter from Google Fonts for editorial themes"
```

---

### Task 2: Add shared font token group to `globals.css`

**Files:**
- Modify: `src/styles/globals.css` (append after the `:root.slick` block, before the `body` rule)

- [ ] **Step 1: Append the shared font selector group**

Open `src/styles/globals.css`. After the closing `}` of the `:root.slick` block (currently line 50), and before the `body { ... }` block, insert:

```css
:root.editorial-amber,
:root.editorial-terracotta,
:root.editorial-espresso {
  --font-family: "Inter", sans-serif;
  --title-font-family: "Lora", serif;
}
```

- [ ] **Step 2: Verify in browser**

In `App.tsx`, temporarily add `editorial-amber` to `<html>` to test — the easiest way is to add it via DevTools console:

```js
document.documentElement.classList.add('editorial-amber')
```

If `--font-family` is wired in Vibe, body text should shift to Inter. If `--title-font-family` is consumed anywhere, titles will shift to Lora. (Note: `--title-font-family` may not render visibly until component-level hookup — this is a known gap per the spec.)

- [ ] **Step 3: Commit**

```bash
git add src/styles/globals.css
git commit -m "feat: add shared editorial font tokens (Lora titles, Inter body)"
```

---

### Task 3: Add `:root.editorial-amber` theme block

**Files:**
- Modify: `src/styles/globals.css` (append after the shared font group)

- [ ] **Step 1: Append the amber theme block**

After the shared font group, append:

```css
:root.editorial-amber {
  /* Primary — amber/gold */
  --primary-color: #b45309;
  --primary-hover-color: #92400e;
  --primary-selected-color: #fef3c7;
  --primary-selected-hover-color: #fde68a;
  --primary-highlighted-color: #fffbeb;

  /* Surfaces — cream */
  --primary-surface-color: #fdf8f0;
  --allgrey-background-color: #fdf8f0;
  --grey-background-color: #fdf8f0;
  --ui-background-color: #f5ead8;
  --ui-background-hover-color: #edd9b8;

  /* Borders */
  --layout-border-color: #e8e0d5;
  --ui-border-color: #d5c9b8;

  /* Text & icons */
  --primary-text-color: #1c1309;
  --secondary-text-color: #78716c;
  --icon-color: #78716c;
  --placeholder-color: #78716c;
  --link-color: #b45309;

  /* Border radius — structured */
  --border-radius-small: 4px;
  --border-radius-medium: 8px;
  --border-radius-big: 16px;
}
```

- [ ] **Step 2: Test amber theme in browser**

In DevTools console:

```js
document.documentElement.classList.add('editorial-amber')
```

Check:
- Buttons / primary actions are amber (`#b45309`)
- App shell / sidebar background is warm cream (`#fdf8f0`)
- Borders feel warmer/lighter than the default blue-tinted ones
- Border radius on UI elements looks structured (4/8/16px)

- [ ] **Step 3: Commit**

```bash
git add src/styles/globals.css
git commit -m "feat: add editorial-amber theme — amber accent, cream surfaces"
```

---

### Task 4: Add `:root.editorial-terracotta` theme block

**Files:**
- Modify: `src/styles/globals.css` (append after amber block)

- [ ] **Step 1: Append the terracotta theme block**

```css
:root.editorial-terracotta {
  /* Primary — rust/terracotta */
  --primary-color: #c2410c;
  --primary-hover-color: #9a3412;
  --primary-selected-color: #ffedd5;
  --primary-selected-hover-color: #fed7aa;
  --primary-highlighted-color: #fff7ed;

  /* Surfaces — warm off-white */
  --primary-surface-color: #fdf4ef;
  --allgrey-background-color: #fdf4ef;
  --grey-background-color: #fdf4ef;
  --ui-background-color: #f5e4d8;
  --ui-background-hover-color: #edcfb8;

  /* Borders */
  --layout-border-color: #e8ddd5;
  --ui-border-color: #d5c4b8;

  /* Text & icons */
  --primary-text-color: #1c0e08;
  --secondary-text-color: #78716c;
  --icon-color: #78716c;
  --placeholder-color: #78716c;
  --link-color: #c2410c;

  /* Border radius — sharpest */
  --border-radius-small: 2px;
  --border-radius-medium: 4px;
  --border-radius-big: 8px;
}
```

- [ ] **Step 2: Test terracotta theme in browser**

```js
document.documentElement.classList.remove('editorial-amber')
document.documentElement.classList.add('editorial-terracotta')
```

Check:
- Primary actions are rust/orange (`#c2410c`)
- Surfaces are slightly more orange-tinted than amber
- Border radius is noticeably sharper than amber (almost square corners)

- [ ] **Step 3: Commit**

```bash
git add src/styles/globals.css
git commit -m "feat: add editorial-terracotta theme — rust accent, sharpest radius"
```

---

### Task 5: Add `:root.editorial-espresso` theme block

**Files:**
- Modify: `src/styles/globals.css` (append after terracotta block)

- [ ] **Step 1: Append the espresso theme block**

```css
:root.editorial-espresso {
  /* Primary — deep brown */
  --primary-color: #3d2a1e;
  --primary-hover-color: #2a1c12;
  --primary-selected-color: #f0e8e0;
  --primary-selected-hover-color: #e2d4c8;
  --primary-highlighted-color: #faf7f4;

  /* Surfaces — parchment */
  --primary-surface-color: #f7f3ef;
  --allgrey-background-color: #f7f3ef;
  --grey-background-color: #f7f3ef;
  --ui-background-color: #ede4da;
  --ui-background-hover-color: #e0d0c0;

  /* Borders */
  --layout-border-color: #e2d8cf;
  --ui-border-color: #cfc4b8;

  /* Text & icons */
  --primary-text-color: #1c1309;
  --secondary-text-color: #7c6f65;
  --icon-color: #7c6f65;
  --placeholder-color: #7c6f65;
  --link-color: #3d2a1e;

  /* Border radius — softest */
  --border-radius-small: 8px;
  --border-radius-medium: 14px;
  --border-radius-big: 28px;
}
```

- [ ] **Step 2: Test espresso theme in browser**

```js
document.documentElement.classList.remove('editorial-terracotta')
document.documentElement.classList.add('editorial-espresso')
```

Check:
- Primary actions are deep near-black brown (`#3d2a1e`)
- Surfaces are parchment-warm (`#f7f3ef`)
- Border radius is noticeably rounder than amber and terracotta (8/14/28px)
- Hover darkens correctly (from `#3d2a1e` to `#2a1c12`)

- [ ] **Step 3: Commit**

```bash
git add src/styles/globals.css
git commit -m "feat: add editorial-espresso theme — deep brown accent, softest radius"
```

---

### Task 6: Validate and clean up

**Files:**
- Read: `src/styles/globals.css` (final check)
- Delete: `theme-preview.html` (scratch file from brainstorming, no longer needed)

- [ ] **Step 1: Run validate**

```bash
yarn validate
```

Expected: typecheck + lint + format all pass. No errors.

- [ ] **Step 2: Spot-check all three themes**

Run `yarn dev`. In DevTools console, cycle through all three:

```js
// amber
document.documentElement.className = 'editorial-amber'
// terracotta
document.documentElement.className = 'editorial-terracotta'
// espresso
document.documentElement.className = 'editorial-espresso'
// reset to default
document.documentElement.className = ''
```

Confirm each theme changes the primary color, surface color, and border radius visibly.

- [ ] **Step 3: Delete the scratch preview file (if it exists)**

```bash
git rm theme-preview.html 2>/dev/null || true
```

- [ ] **Step 4: Final commit**

```bash
git add -u
git commit -m "chore: remove brainstorm scratch file, validate editorial themes"
```
