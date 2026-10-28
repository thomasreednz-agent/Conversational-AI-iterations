# Sleek Theme Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace monday blue palette + Poppins/Figtree fonts with a near-black cool-gray theme using Geist font, via CSS token overrides only.

**Architecture:** Install `@fontsource/geist` for self-hosted font loading, then edit `src/styles/globals.css` in two places: replace the Google Fonts import with Geist imports, and add a `:root` block that overrides Vibe's semantic tokens. No component files are touched.

**Tech Stack:** Vibe (`@vibe/core`) CSS custom properties, `@fontsource/geist`, Vite (bundler handles `@import` from node_modules), Tailwind CSS 4, yarn.

---

## File Map

| File | Change |
|---|---|
| `package.json` | Add `@fontsource/geist` dependency |
| `yarn.lock` | Updated by yarn |
| `src/styles/globals.css` | Replace font import, add `:root` token overrides |

---

## Task 1: Install Geist font package

**Files:**
- Modify: `package.json`
- Modify: `yarn.lock`

- [ ] **Step 1: Install the package**

```bash
yarn add @fontsource/geist
```

Expected: `@fontsource/geist` appears in `package.json` dependencies, `yarn.lock` updated. No errors.

- [ ] **Step 2: Verify the font files exist**

```bash
ls node_modules/@fontsource/geist/400.css node_modules/@fontsource/geist/500.css node_modules/@fontsource/geist/600.css node_modules/@fontsource/geist/700.css
```

Expected: all 4 files listed with no errors.

- [ ] **Step 3: Commit**

```bash
git add package.json yarn.lock
git commit -m "feat: add @fontsource/geist dependency"
```

---

## Task 2: Update globals.css — font import + token overrides

**Files:**
- Modify: `src/styles/globals.css`

This task has two sub-parts: replace the font import, then add the `:root` token block.

### Part A — Replace font import

The current file has this on line 3:

```css
@import url("https://fonts.googleapis.com/css2?family=Figtree:ital,wght@0,300..900;1,300..900&family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");
```

- [ ] **Step 1: Replace the Google Fonts import with Geist imports**

Open `src/styles/globals.css`. Replace the entire `@import url("https://fonts.googleapis.com/...")` line with:

```css
@import "@fontsource/geist/400.css";
@import "@fontsource/geist/500.css";
@import "@fontsource/geist/600.css";
@import "@fontsource/geist/700.css";
```

The file should now start with:
```css
/* Vibe Design System Globals */
@import "@vibe/core/tokens";
@import "@fontsource/geist/400.css";
@import "@fontsource/geist/500.css";
@import "@fontsource/geist/600.css";
@import "@fontsource/geist/700.css";
```

- [ ] **Step 2: Start dev server and verify Geist loads**

```bash
yarn dev
```

Open the app in the browser. Open DevTools → Network tab, filter by "geist". You should see font files being served from `localhost` (not from fonts.googleapis.com). The UI text should visibly change — Geist is more geometric and tighter than Figtree/Poppins.

### Part B — Add `:root` token overrides

- [ ] **Step 3: Add the `:root` block to globals.css**

After the four `@import "@fontsource/geist/..."` lines (and before the `body {}` rule), add:

```css
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

  /* Borders — layout dividers lighter, UI element borders darker */
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

- [ ] **Step 4: Visual check in dev server**

With the dev server still running (or restart with `yarn dev`), check the following in the browser:

| Element | Expected appearance |
|---|---|
| App background/wrapper | Cool gray `#f2f3f5` — no blue tint |
| Buttons (primary) | Near-black `#1d1d1f`, not monday blue |
| Left pane active item | Near-black highlight, not blue |
| Board group color accent | Near-black for the "This Month" group (was blue) |
| Checkboxes (checked) | Near-black fill, not blue |
| Table row borders | Lighter `#e5e5e5` |
| Font throughout | Geist — geometric, tight, clean |
| Status badges (Done/Stuck/Working on it) | Colors unchanged — green/red/orange |

- [ ] **Step 5: Run validate**

```bash
yarn validate
```

Expected: typecheck passes, no lint errors, no format errors. If format errors appear on `globals.css`, run `yarn format` and re-check.

- [ ] **Step 6: Commit**

```bash
git add src/styles/globals.css
git commit -m "feat: apply sleek theme — near-black tokens + Geist font"
```

---

## Done

The sleek theme is live. Two commits total:
1. `feat: add @fontsource/geist dependency`
2. `feat: apply sleek theme — near-black tokens + Geist font`

The change is fully reversible by reverting those two commits. Future iterations (dark mode, component-level CSS patches) can be layered on top.
