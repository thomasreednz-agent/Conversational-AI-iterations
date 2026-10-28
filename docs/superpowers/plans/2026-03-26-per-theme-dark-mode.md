# Per-Theme Dark/Black Mode Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add compound CSS selectors so each custom theme (slick, editorial-amber, editorial-terracotta, editorial-espresso) has its own dark and black mode color token overrides derived from its palette.

**Architecture:** Append 8 new compound selector blocks to `src/styles/globals.css` — one per theme × mode combination (e.g. `:root.slick.dark-app-theme`). No JS changes needed; App.tsx already applies both classes simultaneously. The existing bare `dark-app-theme` / `black-app-theme` blocks remain for the Default theme.

**Tech Stack:** CSS custom properties, Tailwind CSS 4, Vibe token system

---

### Task 1: Add Slick dark/black overrides

**Files:**
- Modify: `src/styles/globals.css` (append after existing `black-app-theme` block)

- [ ] **Step 1: Open `src/styles/globals.css` and append the Slick blocks after the existing `black-app-theme` block (line 267)**

```css
/* Slick — dark mode */
:root.slick.dark-app-theme {
  --primary-background-color: #252528;
  --primary-surface-color: #303036;
  --secondary-background-color: #303036;
  --primary-text-color: #d5d5d8;
  --secondary-text-color: #9090a0;
  --icon-color: #9090a0;
  --placeholder-color: #5a5a68;
  --link-color: #9a9aa2;
  --ui-background-color: #3e3e46;
  --ui-background-hover-color: #4c4c56;
  --ui-border-color: #4c4c56;
  --layout-border-color: #303036;
  --primary-selected-color: #32323c;
  --primary-selected-hover-color: #3a3a46;
  --primary-highlighted-color: #303036;
}

/* Slick — black mode */
:root.slick.black-app-theme {
  --primary-background-color: #000000;
  --primary-surface-color: #111113;
  --secondary-background-color: #111113;
  --primary-text-color: #e0e0e3;
  --secondary-text-color: #737380;
  --icon-color: #737380;
  --placeholder-color: #4a4a56;
  --link-color: #909098;
  --ui-background-color: #1c1c20;
  --ui-background-hover-color: #28282e;
  --ui-border-color: #28282e;
  --layout-border-color: #111113;
  --primary-selected-color: #1e1e26;
  --primary-selected-hover-color: #242430;
  --primary-highlighted-color: #111113;
}
```

- [ ] **Step 2: Verify in browser**

Run `yarn dev`, switch to Slick theme + Dark mode, then Slick + Black mode. Confirm backgrounds are cool neutral grays with no blue tint, and that surface/ui-bg layers are visibly distinct.

- [ ] **Step 3: Commit**

```bash
git add src/styles/globals.css
git commit -m "feat: add slick dark/black theme overrides"
```

---

### Task 2: Add Amber dark/black overrides

**Files:**
- Modify: `src/styles/globals.css` (append after Task 1 additions)

- [ ] **Step 1: Append the Amber blocks**

```css
/* Amber — dark mode */
:root.editorial-amber.dark-app-theme {
  --primary-background-color: #231f18;
  --primary-surface-color: #2e2820;
  --secondary-background-color: #2e2820;
  --primary-text-color: #ead6b8;
  --secondary-text-color: #a08868;
  --icon-color: #a08868;
  --placeholder-color: #6a5840;
  --link-color: #c97c30;
  --ui-background-color: #3c3228;
  --ui-background-hover-color: #4a3e30;
  --ui-border-color: #4a3e30;
  --layout-border-color: #2e2820;
  --primary-selected-color: #362c20;
  --primary-selected-hover-color: #40342a;
  --primary-highlighted-color: #2e2820;
}

/* Amber — black mode */
:root.editorial-amber.black-app-theme {
  --primary-background-color: #110e09;
  --primary-surface-color: #181410;
  --secondary-background-color: #181410;
  --primary-text-color: #f0e4d0;
  --secondary-text-color: #847060;
  --icon-color: #847060;
  --placeholder-color: #584838;
  --link-color: #d4924a;
  --ui-background-color: #221c16;
  --ui-background-hover-color: #2e2620;
  --ui-border-color: #2e2620;
  --layout-border-color: #181410;
  --primary-selected-color: #201810;
  --primary-selected-hover-color: #281e16;
  --primary-highlighted-color: #181410;
}
```

- [ ] **Step 2: Verify in browser**

Switch to Amber + Dark, then Amber + Black. Confirm backgrounds are warm red-brown (not golden/yellow), text is warm off-white, and the accent link color reads as amber-brown.

- [ ] **Step 3: Commit**

```bash
git add src/styles/globals.css
git commit -m "feat: add editorial-amber dark/black theme overrides"
```

---

### Task 3: Add Terracotta dark/black overrides

**Files:**
- Modify: `src/styles/globals.css` (append after Task 2 additions)

- [ ] **Step 1: Append the Terracotta blocks**

```css
/* Terracotta — dark mode */
:root.editorial-terracotta.dark-app-theme {
  --primary-background-color: #211810;
  --primary-surface-color: #2a2018;
  --secondary-background-color: #2a2018;
  --primary-text-color: #ead4c4;
  --secondary-text-color: #9c8070;
  --icon-color: #9c8070;
  --placeholder-color: #685040;
  --link-color: #ea580c;
  --ui-background-color: #382818;
  --ui-background-hover-color: #463220;
  --ui-border-color: #463220;
  --layout-border-color: #2a2018;
  --primary-selected-color: #342418;
  --primary-selected-hover-color: #3e2e20;
  --primary-highlighted-color: #2a2018;
}

/* Terracotta — black mode */
:root.editorial-terracotta.black-app-theme {
  --primary-background-color: #100c07;
  --primary-surface-color: #151009;
  --secondary-background-color: #151009;
  --primary-text-color: #f0d8c8;
  --secondary-text-color: #8a6e60;
  --icon-color: #8a6e60;
  --placeholder-color: #564030;
  --link-color: #f97316;
  --ui-background-color: #1c1510;
  --ui-background-hover-color: #271c14;
  --ui-border-color: #271c14;
  --layout-border-color: #151009;
  --primary-selected-color: #1c1210;
  --primary-selected-hover-color: #241816;
  --primary-highlighted-color: #151009;
}
```

- [ ] **Step 2: Verify in browser**

Switch to Terracotta + Dark, then Terracotta + Black. Confirm backgrounds are warm rust-orange-brown, the accent link color is terracotta orange, and contrast between surface layers is visible.

- [ ] **Step 3: Commit**

```bash
git add src/styles/globals.css
git commit -m "feat: add editorial-terracotta dark/black theme overrides"
```

---

### Task 4: Add Espresso dark/black overrides

**Files:**
- Modify: `src/styles/globals.css` (append after Task 3 additions)

- [ ] **Step 1: Append the Espresso blocks**

```css
/* Espresso — dark mode */
:root.editorial-espresso.dark-app-theme {
  --primary-background-color: #1c1610;
  --primary-surface-color: #241e18;
  --secondary-background-color: #241e18;
  --primary-text-color: #e2d5c8;
  --secondary-text-color: #9a8878;
  --icon-color: #9a8878;
  --placeholder-color: #645448;
  --link-color: #c4a882;
  --ui-background-color: #302618;
  --ui-background-hover-color: #3c3020;
  --ui-border-color: #3c3020;
  --layout-border-color: #241e18;
  --primary-selected-color: #2c2218;
  --primary-selected-hover-color: #382c20;
  --primary-highlighted-color: #241e18;
}

/* Espresso — black mode */
:root.editorial-espresso.black-app-theme {
  --primary-background-color: #0e0b08;
  --primary-surface-color: #141009;
  --secondary-background-color: #141009;
  --primary-text-color: #ede0d2;
  --secondary-text-color: #887868;
  --icon-color: #887868;
  --placeholder-color: #504038;
  --link-color: #d4b896;
  --ui-background-color: #1c1610;
  --ui-background-hover-color: #261e18;
  --ui-border-color: #261e18;
  --layout-border-color: #141009;
  --primary-selected-color: #181210;
  --primary-selected-hover-color: #201816;
  --primary-highlighted-color: #141009;
}
```

- [ ] **Step 2: Verify in browser**

Switch to Espresso + Dark, then Espresso + Black. Confirm backgrounds are deep coffee-brown, the accent link color reads as warm tan/cream, and text is warm white.

- [ ] **Step 3: Run validate**

```bash
yarn validate
```

Expected: no TypeScript errors, no lint errors, no format issues. CSS-only change so no type errors expected.

- [ ] **Step 4: Commit**

```bash
git add src/styles/globals.css
git commit -m "feat: add editorial-espresso dark/black theme overrides"
```

---

### Task 5: Cross-theme smoke check

No code changes — verification only.

- [ ] **Step 1: Test all 12 combinations**

With `yarn dev` running, cycle through every theme × mode combination using the ThemeSwitcher (bottom-right corner). Check:

| Theme | Light | Dark | Black |
|---|---|---|---|
| Default | ✓ unchanged | ✓ unchanged | ✓ unchanged |
| Slick | ✓ existing | cool gray bg | near-black gray |
| Amber | ✓ existing | warm red-brown | very dark brown |
| Terracotta | ✓ existing | rust-orange-brown | near-black orange-brown |
| Espresso | ✓ existing | deep coffee | near-black coffee |

- [ ] **Step 2: Check no light-mode tokens bleed through in dark/black**

For each custom theme in dark mode, confirm:
- Background is visibly dark (not the light cream/gray surface color)
- Text is light-on-dark (not dark text on dark bg)
- The left pane gradient (`globals.css` `.leftpane`) uses `--primary-background-color` and `--primary-surface-color` — verify it still looks correct in dark mode

- [ ] **Step 3: Final commit if any minor tweaks made**

```bash
git add src/styles/globals.css
git commit -m "fix: adjust theme dark/black token values after smoke check"
```
