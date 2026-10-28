# Surface Elevation Tokens & Alias Bridge Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add the `surface-*` semantic elevation token scale to `@vibe/style`, wire all old surface/background tokens as CSS aliases pointing to the new canonical tokens, and fix the `box-shadow-mediun` typo bug — all with zero breaking changes.

**Architecture:** New `surface-*` tokens are defined first in each theme file with concrete hex values. Old tokens are then rewritten as `var(--surface-*)` aliases in the same file. Consumers using old names continue to resolve the same visual values; fixing a `surface-*` value propagates to all aliases automatically. Semantic tints (`surface-brand/positive/negative/warning/info`) are additive — no old token maps to them.

**Tech Stack:** SCSS, CSS custom properties, Vibe monorepo (`mondaycom/vibe`), Storybook

---

## Reference: Token Values by Theme

Use this table when writing theme file tasks:

| Token | Light | Dark | Black | Hacker |
|---|---|---|---|---|
| `--surface-sunken` | `#f6f7fb` | `#30324e` | `#2c2c2c` | `#282a36` |
| `--surface-base` | `#ffffff` | `#181b34` | `#111111` | `#282a36` |
| `--surface-base-hover` | `rgba(103,104,121,0.1)` | `rgba(103,104,121,0.3)` | `rgba(103,104,121,0.3)` | `#4b4e69` |
| `--surface-base-disabled` | `#ecedf5` | `#3c3f59` | `#3a3a3a` | `#3a3a3a` |
| `--surface-raised` | `#e7e9ef` | `#434660` | `#4d4d4d` | `#4b4e69` |
| `--surface-raised-hover` | `#d8d9e0` | `#35384d` | `#3b3b3b` | `#3b3b3b` |
| `--surface-overlay` | `#ffffff` | `#30324e` | `#2c2c2c` | `#30324e` |
| `--surface-inverted` | `#323338` | `#ffffff` | `#eeeeee` | `#ffffff` |
| `--backdrop-default` | `rgba(41,47,76,0.7)` | `rgba(41,47,76,0.7)` | `rgba(33,33,33,0.7)` | `rgba(41,47,76,0.7)` |

Semantic surface tints (background-only, no interaction states in Phase 1):

| Token | Light | Dark | Black | Hacker |
|---|---|---|---|---|
| `--surface-brand` | `#e6e9ff` | `#2b3060` | `#1e2040` | `#2d2f55` |
| `--surface-positive` | `#e6f9f1` | `#1a3d2b` | `#132b1e` | `#1e3528` |
| `--surface-negative` | `#ffeeed` | `#3d1a1a` | `#2b1212` | `#3a1e1e` |
| `--surface-warning` | `#fff4e0` | `#3d2e0d` | `#2b2008` | `#35280e` |
| `--surface-info` | `#e0f0ff` | `#1a2e3d` | `#0e2028` | `#1e2d3a` |

> **Note:** Semantic tint values are starting points — validate with the design team before shipping. They follow the pattern of the existing `--label-background-color: #cce5ff` (light) / `#404b69` (dark) as a reference.

---

## File Map

| File | Change |
|---|---|
| `packages/style/src/themes/light-theme.scss` | Add `surface-*` tokens, add alias block |
| `packages/style/src/themes/dark-theme.scss` | Add `surface-*` tokens, add alias block |
| `packages/style/src/themes/black-theme.scss` | Add `surface-*` tokens, add alias block |
| `packages/style/src/themes/hacker-theme.scss` | Add `surface-*` tokens, add alias block |
| `packages/style/src/themes/dark-theme.scss` | Fix `box-shadow-mediun` alias |
| `packages/style/src/themes/black-theme.scss` | Fix `box-shadow-mediun` alias |
| `packages/style/src/themes/hacker-theme.scss` | Fix `box-shadow-mediun` alias |

---

## Task 1: Surface elevation tokens — light theme

**Files:**
- Modify: `packages/style/src/themes/light-theme.scss`

- [ ] **Step 1: Add new surface tokens to the light theme**

Inside the `:root` (or `.light-theme`) block, add a clearly-labelled section:

```scss
/* ─── Surface tokens ─────────────────────────────────────────── */

/* Elevation scale */
--surface-sunken: #f6f7fb;
--surface-base: #ffffff;
--surface-base-hover: rgba(103, 104, 121, 0.1);
--surface-base-disabled: #ecedf5;
--surface-raised: #e7e9ef;
--surface-raised-hover: #d8d9e0;
--surface-overlay: #ffffff;
--surface-inverted: #323338;

/* Backdrop */
--backdrop-default: rgba(41, 47, 76, 0.7);

/* Semantic tints (background-only — validate hex with design team) */
--surface-brand: #e6e9ff;
--surface-positive: #e6f9f1;
--surface-negative: #ffeeed;
--surface-warning: #fff4e0;
--surface-info: #e0f0ff;

/* ─── Backward-compat aliases (deprecated — use surface-* instead) */
--primary-background-color: var(--surface-base);
--primary-background-hover-color: var(--surface-base-hover);
--secondary-background-color: var(--surface-base);
--allgrey-background-color: var(--surface-sunken);
--grey-background-color: var(--surface-sunken);
--dark-background-color: var(--surface-sunken);
--ui-background-color: var(--surface-raised);
--ui-background-hover-color: var(--surface-raised-hover);
--modal-background-color: var(--surface-overlay);
--dialog-background-color: var(--surface-overlay);
--inverted-color-background: var(--surface-inverted);
--backdrop-color: var(--backdrop-default);
--disabled-background-color: var(--surface-base-disabled);
```

> `secondary-background-color` maps to `surface-base` here. In light theme both are `#ffffff` so this is value-identical. Dark/hacker themes are handled separately in Task 2.

> Do **not** alias `--primary-surface-color`, `--color-surface`, or `--label-background-color` yet — these need a usage audit before safe aliasing (see Open Questions in the design spec).

- [ ] **Step 2: Build and verify light theme output**

```bash
cd packages/style && yarn build
grep "surface-base" dist/index.css | head -5
```

Expected:
```
--surface-base:#ffffff
--primary-background-color:var(--surface-base)
```

- [ ] **Step 3: Verify modal-background-color resolves correctly via alias**

```bash
node -e "
const css = require('fs').readFileSync('packages/style/dist/index.css', 'utf8');
console.log('modal alias:', css.includes('--modal-background-color:var(--surface-overlay)'));
"
```

Expected: `modal alias: true`

- [ ] **Step 4: Commit**

```bash
git add packages/style/src/themes/light-theme.scss
git commit -m "feat(style): add surface elevation tokens and alias bridge (light theme)"
```

---

## Task 2: Surface elevation tokens — dark theme

**Files:**
- Modify: `packages/style/src/themes/dark-theme.scss`

- [ ] **Step 1: Add surface tokens to dark theme**

```scss
/* ─── Surface tokens ─────────────────────────────────────────── */

/* Elevation scale */
--surface-sunken: #30324e;
--surface-base: #181b34;
--surface-base-hover: rgba(103, 104, 121, 0.3);
--surface-base-disabled: #3c3f59;
--surface-raised: #434660;
--surface-raised-hover: #35384d;
--surface-overlay: #30324e;
--surface-inverted: #ffffff;

/* Backdrop */
--backdrop-default: rgba(41, 47, 76, 0.7);

/* Semantic tints */
--surface-brand: #2b3060;
--surface-positive: #1a3d2b;
--surface-negative: #3d1a1a;
--surface-warning: #3d2e0d;
--surface-info: #1a2e3d;

/* ─── Backward-compat aliases */
--primary-background-color: var(--surface-base);
--primary-background-hover-color: var(--surface-base-hover);
--secondary-background-color: var(--surface-raised);
--allgrey-background-color: var(--surface-sunken);
--grey-background-color: var(--surface-base);
--dark-background-color: var(--surface-sunken);
--ui-background-color: var(--surface-raised);
--ui-background-hover-color: var(--surface-raised-hover);
--modal-background-color: var(--surface-overlay);
--dialog-background-color: var(--surface-overlay);
--inverted-color-background: var(--surface-inverted);
--backdrop-color: var(--backdrop-default);
--disabled-background-color: var(--surface-base-disabled);
```

> **Key difference from light:** `secondary-background-color` maps to `surface-raised` in dark (its value `#30324e` = `surface-overlay` = `surface-sunken` in dark, which is the visually-raised tier). `grey-background-color` maps to `surface-base` because in dark, `grey-bg` was `#181b34` — the page background, not recessed. This split was identified in the usage audit note in the design spec.

- [ ] **Step 2: Fix the `box-shadow-mediun` typo bug**

Find the `box-shadow-mediun` line in `dark-theme.scss`. Its current value is `0px 6px 20px rgba(0,0,0,0.2)` — which is the **wrong** dark-theme shadow (it uses the light-theme alpha). Replace it with an alias:

```scss
/* deprecated typo — use --box-shadow-medium */
--box-shadow-mediun: var(--box-shadow-medium);
```

- [ ] **Step 3: Build and spot-check**

```bash
cd packages/style && yarn build
node -e "
const css = require('fs').readFileSync('packages/style/dist/index.css', 'utf8');
console.log('modal alias dark:', css.includes('--modal-background-color:var(--surface-overlay)'));
console.log('mediun fixed:', css.includes('--box-shadow-mediun:var(--box-shadow-medium)'));
"
```

Expected: both `true`

- [ ] **Step 4: Commit**

```bash
git add packages/style/src/themes/dark-theme.scss
git commit -m "feat(style): add surface tokens and alias bridge (dark theme); fix box-shadow-mediun"
```

---

## Task 3: Surface elevation tokens — black and hacker themes

**Files:**
- Modify: `packages/style/src/themes/black-theme.scss`
- Modify: `packages/style/src/themes/hacker-theme.scss`

- [ ] **Step 1: Add surface tokens to black theme**

```scss
/* ─── Surface tokens ─────────────────────────────────────────── */

/* Elevation scale */
--surface-sunken: #2c2c2c;
--surface-base: #111111;
--surface-base-hover: rgba(103, 104, 121, 0.3);
--surface-base-disabled: #3a3a3a;
--surface-raised: #4d4d4d;
--surface-raised-hover: #3b3b3b;
--surface-overlay: #2c2c2c;
--surface-inverted: #eeeeee;

/* Backdrop */
--backdrop-default: rgba(33, 33, 33, 0.7);

/* Semantic tints */
--surface-brand: #1e2040;
--surface-positive: #132b1e;
--surface-negative: #2b1212;
--surface-warning: #2b2008;
--surface-info: #0e2028;

/* ─── Backward-compat aliases */
--primary-background-color: var(--surface-base);
--primary-background-hover-color: var(--surface-base-hover);
--secondary-background-color: var(--surface-raised);
--allgrey-background-color: var(--surface-sunken);
--grey-background-color: var(--surface-base);
--dark-background-color: var(--surface-sunken);
--ui-background-color: var(--surface-raised);
--ui-background-hover-color: var(--surface-raised-hover);
--modal-background-color: var(--surface-overlay);
--dialog-background-color: var(--surface-overlay);
--inverted-color-background: var(--surface-inverted);
--backdrop-color: var(--backdrop-default);
--disabled-background-color: var(--surface-base-disabled);

/* deprecated typo — use --box-shadow-medium */
--box-shadow-mediun: var(--box-shadow-medium);
```

> **Bug fix:** Previously `modal-background-color` in black was `#181b34` (the dark-theme page color, not the black-theme one). It now correctly resolves to `surface-overlay: #2c2c2c` which is the actual black-theme panel color.

- [ ] **Step 2: Add surface tokens to hacker theme**

```scss
/* ─── Surface tokens ─────────────────────────────────────────── */

/* Elevation scale */
--surface-sunken: #282a36;
--surface-base: #282a36;
--surface-base-hover: #4b4e69;
--surface-base-disabled: #3a3a3a;
--surface-raised: #4b4e69;
--surface-raised-hover: #3b3b3b;
--surface-overlay: #30324e;
--surface-inverted: #ffffff;

/* Backdrop */
--backdrop-default: rgba(41, 47, 76, 0.7);

/* Semantic tints */
--surface-brand: #2d2f55;
--surface-positive: #1e3528;
--surface-negative: #3a1e1e;
--surface-warning: #35280e;
--surface-info: #1e2d3a;

/* ─── Backward-compat aliases */
--primary-background-color: var(--surface-base);
--primary-background-hover-color: var(--surface-base-hover);
--secondary-background-color: var(--surface-raised);
--allgrey-background-color: var(--surface-sunken);
--grey-background-color: var(--surface-base);
--dark-background-color: var(--surface-sunken);
--ui-background-color: var(--surface-raised);
--ui-background-hover-color: var(--surface-raised-hover);
--modal-background-color: var(--surface-overlay);
--dialog-background-color: var(--surface-overlay);
--inverted-color-background: var(--surface-inverted);
--backdrop-color: var(--backdrop-default);
--disabled-background-color: var(--surface-base-disabled);

/* deprecated typo — use --box-shadow-medium */
--box-shadow-mediun: var(--box-shadow-medium);
```

> **Note:** `surface-sunken` and `surface-base` have the same value in hacker (`#282a36`) — this is intentional. The hacker theme has minimal depth contrast by aesthetic design.

- [ ] **Step 3: Build and verify all four themes**

```bash
cd packages/style && yarn build
node -e "
const css = require('fs').readFileSync('packages/style/dist/index.css', 'utf8');
const occurrences = (css.match(/--surface-base:/g) || []).length;
console.log('surface-base defined in themes:', occurrences, '(expected 4)');
"
```

Expected: `surface-base defined in themes: 4`

- [ ] **Step 4: Commit**

```bash
git add packages/style/src/themes/black-theme.scss \
        packages/style/src/themes/hacker-theme.scss
git commit -m "feat(style): add surface tokens and alias bridge (black, hacker themes)"
```

---

## Task 4: Storybook visual verification

- [ ] **Step 1: Start Storybook**

```bash
yarn storybook
```

- [ ] **Step 2: Switch through all 4 themes and check for regressions**

Check these components in each theme:
- **Modal / Dialog** — background should match the page (light: white, dark: `#30324e`, black: `#2c2c2c`, hacker: `#30324e`). Previously black theme showed `#181b34` which was wrong.
- **Board** — background behind columns (`surface-sunken`) should look the same as before.
- **Button** — no change expected (buttons don't use surface tokens).
- **Dropdown/Popover** — should use `surface-overlay` via `dialog-background-color` alias.

- [ ] **Step 3: Verify no console errors about undefined CSS variables**

Open browser DevTools console while Storybook is running. Search for any `undefined` or `initial` values on `--surface-*` tokens:

```javascript
// Run in DevTools console
const style = getComputedStyle(document.documentElement);
['surface-base','surface-raised','surface-overlay','surface-sunken','surface-inverted']
  .forEach(t => console.log(`--${t}:`, style.getPropertyValue(`--${t}`).trim()));
```

Expected: all five return non-empty hex or rgba values.

- [ ] **Step 4: Commit any Storybook-prompted fixes**

If a story broke, fix the underlying component and commit:

```bash
git add <changed files>
git commit -m "fix(style): correct surface token value in <theme>"
```

---

## Task 5: Open PR

- [ ] **Step 1: Create the pull request**

```bash
gh pr create \
  --title "feat(style): add surface elevation tokens and alias bridge (all themes)" \
  --body "## Summary
- Adds \`--surface-sunken/base/raised/overlay/inverted\` semantic tokens across all 4 themes
- Adds \`--surface-brand/positive/negative/warning/info\` semantic tint tokens
- Adds \`--backdrop-default\` (theme-aware modal scrim)
- Wires all old background tokens as CSS aliases — zero breaking changes
- Fixes \`--modal-background-color\` cross-theme bug in black theme (was \`#181b34\`, now correctly \`#2c2c2c\` via \`surface-overlay\`)
- Fixes \`--box-shadow-mediun\` typo (aliases to \`--box-shadow-medium\` with correct dark-theme value)

## Migration
No consumer changes required. All old \`--*-background-color\` tokens continue to resolve to the same values via CSS aliases. To adopt the new names, replace usages of old tokens with their \`surface-*\` equivalents at your own pace.

## Tokens NOT aliased yet (require audit first)
- \`--primary-surface-color\` — inconsistent cross-theme meaning
- \`--color-surface\` — missing from hacker theme
- \`--label-background-color\` — maps to \`surface-info\` but needs audit
- \`--secondary-background-color\` — mapped to \`surface-base\` in light, \`surface-raised\` in dark/black/hacker (see alias comments)"
```

---

## Spec Coverage Check

| Spec section | Covered by task |
|---|---|
| Surface elevation tokens (5 levels) | Tasks 1–3 |
| Interaction states (hover, disabled) | Tasks 1–3 (hover + disabled; pressed/selected = Phase 2) |
| Semantic tints (brand/positive/negative/warning/info) | Tasks 1–3 |
| `backdrop-default` | Tasks 1–3 |
| CSS alias bridge for all old tokens | Tasks 1–3 |
| `box-shadow-mediun` typo fix | Tasks 2–3 |
| Cross-theme modal bug fix | Tasks 2–3 |
| Visual regression check | Task 4 |
| PR | Task 5 |

**Not in scope for this plan (separate follow-up):**
- `secondary-background-color` full intent audit
- `primary-surface-color`, `color-surface`, `label-background-color` aliasing
- Shadow → Figma variables migration
- `pressed` and `selected` surface interaction states
