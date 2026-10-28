# Radius & Platform Tokens Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add `border-radius-large` (12px), `border-radius-xlarge` (16px), `border-radius-full` (999px), alias `border-radius-big` → `xlarge`, and add `platform-border-color` tokens to the Vibe `@vibe/style` package — all additive, zero breaking changes.

**Architecture:** Two files change: `packages/style/src/border-radius.scss` for radius and each theme SCSS file for platform tokens. Old names stay alive as CSS `var()` aliases. Ship as a single PR.

**Tech Stack:** SCSS, CSS custom properties, Vibe monorepo (`mondaycom/vibe`), Storybook

---

## File Map

| File | Change |
|---|---|
| `packages/style/src/border-radius.scss` | Add `large`, `xlarge`, `full`; rewrite `big` as alias |
| `packages/style/src/themes/light-theme.scss` | Add `platform-border-color`, `platform-border-color-subtle`, `platform-surface-color` |
| `packages/style/src/themes/dark-theme.scss` | Same platform tokens, dark values |
| `packages/style/src/themes/black-theme.scss` | Same platform tokens, black values |
| `packages/style/src/themes/hacker-theme.scss` | Same platform tokens, hacker values |

---

## Task 1: Radius tokens

**Files:**
- Modify: `packages/style/src/border-radius.scss`

- [ ] **Step 1: Read the current file**

```bash
cat packages/style/src/border-radius.scss
```

Expected: contains `--border-radius-small: 4px`, `--border-radius-medium: 8px`, `--border-radius-big: 16px`

- [ ] **Step 2: Replace the file contents**

```scss
:root {
  --border-radius-small: 4px;
  --border-radius-medium: 8px;
  --border-radius-large: 12px;
  --border-radius-xlarge: 16px;
  --border-radius-full: 999px;

  /* deprecated — use --border-radius-xlarge */
  --border-radius-big: var(--border-radius-xlarge);
}
```

- [ ] **Step 3: Build the style package and verify output**

```bash
cd packages/style && yarn build
grep "border-radius" dist/index.css
```

Expected output contains all five tokens plus the alias:
```
--border-radius-small:4px
--border-radius-medium:8px
--border-radius-large:12px
--border-radius-xlarge:16px
--border-radius-full:999px
--border-radius-big:var(--border-radius-xlarge)
```

- [ ] **Step 4: Verify the alias resolves to the correct value**

```bash
node -e "
const css = require('fs').readFileSync('packages/style/dist/index.css', 'utf8');
const hasXlarge = css.includes('--border-radius-xlarge:16px') || css.includes('--border-radius-xlarge: 16px');
const hasBigAlias = css.includes('--border-radius-big:var(--border-radius-xlarge)') || css.includes('--border-radius-big: var(--border-radius-xlarge)');
console.log('xlarge defined:', hasXlarge);
console.log('big aliased:', hasBigAlias);
"
```

Expected:
```
xlarge defined: true
big aliased: true
```

- [ ] **Step 5: Commit**

```bash
git add packages/style/src/border-radius.scss
git commit -m "feat(style): add border-radius-large, xlarge, full; alias big to xlarge"
```

---

## Task 2: Platform shell tokens — light theme

**Files:**
- Modify: `packages/style/src/themes/light-theme.scss`

- [ ] **Step 1: Add platform tokens at the end of the light theme token block**

Add these lines inside the `:root` block (or theme class block — match the existing pattern in the file):

```scss
/* Platform shell tokens — for layout framework team's chrome only.
   Product / in-app UI: use --layout-border-color instead. */
--platform-border-color: #e6e9ef;
--platform-border-color-subtle: #eef0f6;
--platform-surface-color: #f6f7fb;
```

`#f6f7fb` matches `allgrey-background-color` in light — the same recessed canvas. Once the surface token plan ships, `platform-surface-color` becomes `var(--surface-sunken)`.

- [ ] **Step 2: Verify the token appears in the built output**

```bash
cd packages/style && yarn build
grep "platform-border-color" dist/index.css
```

Expected:
```
--platform-border-color:#e6e9ef
--platform-border-color-subtle:#eef0f6
--platform-surface-color:#f6f7fb
```

- [ ] **Step 3: Commit**

```bash
git add packages/style/src/themes/light-theme.scss
git commit -m "feat(style): add platform-border-color tokens for layout framework shell (light)"
```

---

## Task 3: Platform shell tokens — dark, black, hacker themes

**Files:**
- Modify: `packages/style/src/themes/dark-theme.scss`
- Modify: `packages/style/src/themes/black-theme.scss`
- Modify: `packages/style/src/themes/hacker-theme.scss`

- [ ] **Step 1: Add platform tokens to dark theme**

```scss
/* Platform shell tokens */
--platform-border-color: #3a3d56;
--platform-border-color-subtle: #434660;
--platform-surface-color: #30324e;
```

`#30324e` matches `allgrey-background-color` in dark (the recessed surface level in dark mode).

- [ ] **Step 2: Add platform tokens to black theme**

```scss
/* Platform shell tokens */
--platform-border-color: #333333;
--platform-border-color-subtle: #3d3d3d;
--platform-surface-color: #2c2c2c;
```

`#2c2c2c` matches `allgrey-background-color` in black.

- [ ] **Step 3: Add platform tokens to hacker theme**

```scss
/* Platform shell tokens */
--platform-border-color: #3b3d52;
--platform-border-color-subtle: #44475a;
--platform-surface-color: #282a36;
```

`#282a36` matches `allgrey-background-color` in hacker (same as page background in hacker — shell blends in, which is intentional for this theme's aesthetic).

- [ ] **Step 4: Build and verify all themes have the tokens**

```bash
cd packages/style && yarn build
grep -c "platform-border-color" dist/index.css
```

Expected: `8` (2 tokens × 4 themes, minimum — may be higher if themes are in one file).

- [ ] **Step 5: Commit**

```bash
git add packages/style/src/themes/dark-theme.scss \
        packages/style/src/themes/black-theme.scss \
        packages/style/src/themes/hacker-theme.scss
git commit -m "feat(style): add platform-border-color tokens to dark, black, hacker themes"
```

---

## Task 4: Storybook verification and PR

- [ ] **Step 1: Start Storybook with theme switching**

```bash
yarn storybook
```

Open the Storybook UI in a browser. Switch between light/dark/black/hacker themes using the theme switcher addon.

- [ ] **Step 2: Visually verify radius tokens appear in component stories**

Check any component that uses `border-radius` (e.g. Button, Dialog, Avatar). Confirm:
- Buttons still use `border-radius-small` (4px) — no visual change
- Dialog/Modal still uses `border-radius-big` which now resolves via alias to `xlarge` (16px) — no visual change
- No component looks broken

- [ ] **Step 3: Check that the new tokens are browseable in the Tokens story (if one exists)**

Search Storybook for a "Design Tokens" or "Tokens" story. Confirm `border-radius-large`, `border-radius-xlarge`, `border-radius-full` appear.

- [ ] **Step 4: Open PR**

```bash
gh pr create \
  --title "feat(style): add radius and platform shell tokens" \
  --body "## Changes
- Add \`border-radius-large\` (12px), \`border-radius-xlarge\` (16px), \`border-radius-full\` (999px)
- Alias \`border-radius-big\` → \`border-radius-xlarge\` (backward compat, no breaking change)
- Add \`platform-border-color\`, \`platform-border-color-subtle\`, \`platform-surface-color\` across all 4 themes

## Migration note
\`border-radius-big\` continues to resolve to 16px via CSS alias. No consumer changes required.

## Governance
\`platform-border-color\` is for layout framework shell chrome only. Product / in-app layout: keep using \`layout-border-color\`."
```
