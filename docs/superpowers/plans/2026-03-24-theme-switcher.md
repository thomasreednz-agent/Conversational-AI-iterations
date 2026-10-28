# Theme Switcher Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a floating bottom-right theme switcher that opens a Vibe Dialog popover with 5 self-theming swatch cards, switching the app theme by toggling a class on `document.documentElement`.

**Architecture:** Three changes: (1) fix 4 CSS selector bugs in `globals.css` so theme classes work when applied to `<html>`; (2) add `activeTheme` state + effect to `App.tsx`; (3) create `ThemeSwitcher.tsx` — a fixed-position Vibe `Dialog` wrapping an `IconButton`, with swatch cards that preview each theme's colors via hardcoded hex values in the JS constants (CSS vars can't be scoped to a child element, only to `:root`).

**Tech Stack:** React 18, TypeScript, Vibe (`@vibe/core` — `Dialog`, `IconButton`, `DialogContentContainer`), `@mondaydotcomorg/icons` (`ColorPicker`), Tailwind CSS 4

---

## File Map

| File | Change |
|---|---|
| `src/styles/globals.css` | Fix 4 selectors: `:root .X` → `:root.X` |
| `src/App.tsx` | Add `activeTheme` state + effect + Fragment wrapper + `<ThemeSwitcher>` |
| `src/components/ThemeSwitcher.tsx` | New file — complete theme switcher component |

---

### Task 1: Fix CSS selector bugs in `globals.css`

**Files:**
- Modify: `src/styles/globals.css` (lines 7, 106, 139, 172)

The existing selectors use `:root .X` (space = descendant combinator), which targets a child of `:root`. The switcher adds classes to `document.documentElement` (which IS `:root`), so selectors must be `:root.X` (no space = class on root itself).

- [ ] **Step 1: Fix the slick selector (line 7)**

Change:
```css
:root .slick {
```
To:
```css
:root.slick {
```

- [ ] **Step 2: Fix the editorial-amber selector (line 106)**

Change:
```css
:root .editorial-amber {
```
To:
```css
:root.editorial-amber {
```

- [ ] **Step 3: Fix the editorial-terracotta selector (line 139)**

Change:
```css
:root .editorial-terracotta {
```
To:
```css
:root.editorial-terracotta {
```

- [ ] **Step 4: Fix the editorial-espresso selector (line 172)**

Change:
```css
:root .editorial-espresso {
```
To:
```css
:root.editorial-espresso {
```

- [ ] **Step 5: Verify the fix manually**

In a browser (with `yarn dev` running), open DevTools console and run:

```js
document.documentElement.classList.add('slick')
```

The entire app should shift to near-black primary color and Geist font. Then:

```js
document.documentElement.classList.remove('slick')
document.documentElement.classList.add('editorial-amber')
```

App should shift to amber/cream palette. Remove before continuing:

```js
document.documentElement.classList.remove('editorial-amber')
```

- [ ] **Step 6: Run validate**

```bash
yarn validate
```

Expected: typecheck + lint + format pass. No errors.

- [ ] **Step 7: Commit**

```bash
git add src/styles/globals.css
git commit -m "fix: correct CSS selector specificity for theme classes (:root.X not :root .X)"
```

---

### Task 2: Create `ThemeSwitcher.tsx` and wire into `App.tsx`

**Files:**
- Create: `src/components/ThemeSwitcher.tsx`
- Modify: `src/App.tsx`

Tasks 2 and 3 are combined into one commit to avoid a TypeScript error where `setActiveTheme` would be declared but unused between separate commits.

**Important CSS scoping note:** Each swatch card previews its theme's colors using hardcoded hex values in the JS `THEMES` constant. We cannot use CSS vars like `var(--primary-color)` on a child `<button>` element to preview per-theme colors, because theme CSS vars are scoped to `:root.X` — they only activate when the class is on `<html>`, not on a descendant element.

- [ ] **Step 1: Create `src/components/ThemeSwitcher.tsx`**

```tsx
import React from "react";
import { Dialog, DialogContentContainer, IconButton } from "@vibe/core";
import { ColorPicker } from "@mondaydotcomorg/icons";

interface ThemeSwitcherProps {
  activeTheme: string;
  onThemeChange: (theme: string) => void;
}

interface ThemeDefinition {
  value: string;
  label: string;
  colors: {
    primary: string;
    surface: string;
    ui: string;
  };
}

const THEMES: ThemeDefinition[] = [
  {
    value: "",
    label: "Default",
    colors: { primary: "#0073ea", surface: "#f0f2f8", ui: "#e7e9ef" },
  },
  {
    value: "slick",
    label: "Slick",
    colors: { primary: "#1d1d1f", surface: "#f2f3f5", ui: "#e8e8ed" },
  },
  {
    value: "editorial-amber",
    label: "Amber",
    colors: { primary: "#b45309", surface: "#fdf8f0", ui: "#f5ead8" },
  },
  {
    value: "editorial-terracotta",
    label: "Terracotta",
    colors: { primary: "#c2410c", surface: "#fdf4ef", ui: "#f5e4d8" },
  },
  {
    value: "editorial-espresso",
    label: "Espresso",
    colors: { primary: "#3d2a1e", surface: "#f7f3ef", ui: "#ede4da" },
  },
];

function SwatchCard({
  theme,
  isActive,
  onClick,
}: {
  theme: ThemeDefinition;
  isActive: boolean;
  onClick: () => void;
}) {
  return (
    <button
      className={`flex w-full items-center gap-3 rounded-lg px-3 py-2 text-left transition-colors hover:bg-[var(--primary-highlighted-color)] ${
        isActive
          ? "bg-[var(--primary-selected-color)]"
          : "bg-[var(--primary-background-color)]"
      }`}
      onClick={onClick}
      aria-pressed={isActive}
      type="button"
    >
      {/* Hardcoded color previews — CSS vars can't be scoped to a child element */}
      <div className="flex shrink-0 gap-1">
        <div
          className="h-5 w-5 rounded-sm"
          style={{ background: theme.colors.primary }}
        />
        <div
          className="h-5 w-5 rounded-sm"
          style={{ background: theme.colors.surface }}
        />
        <div
          className="h-5 w-5 rounded-sm border border-[var(--ui-border-color)]"
          style={{ background: theme.colors.ui }}
        />
      </div>

      {/* Label */}
      <span
        className="flex-1 text-sm font-medium"
        style={{ color: "var(--primary-text-color)" }}
      >
        {theme.label}
      </span>

      {/* Active indicator */}
      {isActive && (
        <span
          className="text-sm font-semibold"
          style={{ color: "var(--primary-color)" }}
          aria-hidden
        >
          ✓
        </span>
      )}
    </button>
  );
}

function SwatchList({
  activeTheme,
  onThemeChange,
}: {
  activeTheme: string;
  onThemeChange: (theme: string) => void;
}) {
  return (
    <DialogContentContainer>
      <div className="flex flex-col gap-1 p-2" style={{ minWidth: "220px" }}>
        <p
          className="px-3 pb-1 pt-1 text-xs font-semibold uppercase tracking-wider"
          style={{ color: "var(--secondary-text-color)" }}
        >
          Theme
        </p>
        {THEMES.map((theme) => (
          <SwatchCard
            key={theme.value}
            theme={theme}
            isActive={activeTheme === theme.value}
            onClick={() => onThemeChange(theme.value)}
          />
        ))}
      </div>
    </DialogContentContainer>
  );
}

export function ThemeSwitcher({
  activeTheme,
  onThemeChange,
}: ThemeSwitcherProps) {
  return (
    <div className="fixed bottom-6 right-6 z-50">
      <Dialog
        content={
          <SwatchList activeTheme={activeTheme} onThemeChange={onThemeChange} />
        }
        showTrigger={["click"]}
        hideTrigger={["clickoutside", "esckey", "onContentClick"]}
        position="top"
      >
        <IconButton
          icon={ColorPicker}
          aria-label="Switch theme"
          kind="secondary"
          size="medium"
        />
      </Dialog>
    </div>
  );
}
```

Key decisions:
- `hideTrigger` includes `"onContentClick"` so clicking a swatch closes the popover
- No `aria-label` on `Dialog` (not in its TypeScript props) — `IconButton` already has `aria-label="Switch theme"`
- Swatch colors are hardcoded hex values matching `globals.css` token values

- [ ] **Step 2: Update `src/App.tsx`**

Add the import at the top of the file (with other component imports):

```ts
import { ThemeSwitcher } from "./components/ThemeSwitcher";
```

After the existing imports block, add the `THEME_CLASSES` constant (outside the component):

```ts
const THEME_CLASSES = [
  "slick",
  "editorial-amber",
  "editorial-terracotta",
  "editorial-espresso",
] as const;
```

Inside the `App` component, after the existing `useState` calls, add:

```ts
const [activeTheme, setActiveTheme] = useState("");
```

After the existing `useEffect(() => { initProduct(PRODUCT); }, [])`, add:

```ts
useEffect(() => {
  THEME_CLASSES.forEach((cls) =>
    document.documentElement.classList.remove(cls),
  );
  if (activeTheme) document.documentElement.classList.add(activeTheme);
}, [activeTheme]);
```

Change the return statement to wrap in a Fragment and add `ThemeSwitcher`:

```tsx
return (
  <>
    <MainLayout product={product}>
      {/* keep existing children unchanged */}
    </MainLayout>
    <ThemeSwitcher activeTheme={activeTheme} onThemeChange={setActiveTheme} />
  </>
);
```

- [ ] **Step 3: Run validate**

```bash
yarn validate
```

Expected: typecheck + lint + format pass. No errors.

- [ ] **Step 4: Test in browser**

Run `yarn dev`. Verify:
- A button is visible in the bottom-right corner
- Clicking it opens a popover above with 5 theme rows
- Each row shows 3 color swatches with the theme's actual colors (hardcoded hex previews)
- Clicking a theme: closes the popover AND changes the app's colors/fonts instantly
- The selected theme shows `✓`
- Clicking outside or pressing Escape closes the popover without changing theme
- Switching back to Default restores the original monday blue theme

- [ ] **Step 5: Commit**

```bash
git add src/components/ThemeSwitcher.tsx src/App.tsx
git commit -m "feat: add ThemeSwitcher — floating popover with theme preview swatch cards"
```

---

### Task 4: Final validation

**Files:**
- Read: all changed files (final check)

- [ ] **Step 1: Run validate**

```bash
yarn validate
```

Expected: typecheck + lint + format all pass. 5 pre-existing warnings OK, 0 errors.

- [ ] **Step 2: Full browser smoke test**

Run `yarn dev`. Cycle through all themes and verify:

| Theme | Primary color | Surface color | Font |
|---|---|---|---|
| Default | Monday blue `#0073ea` | Light blue-gray | Figtree/Poppins |
| Slick | Near-black `#1d1d1f` | Cool gray `#f2f3f5` | Geist |
| Amber | Amber `#b45309` | Cream `#fdf8f0` | Inter/Lora |
| Terracotta | Rust `#c2410c` | Warm white `#fdf4ef` | Inter/Lora |
| Espresso | Deep brown `#3d2a1e` | Parchment `#f7f3ef` | Inter/Lora |

- [ ] **Step 3: Commit if needed**

If validate produced any auto-fixable formatting issues (`yarn format` was needed), commit those:

```bash
git add -u
git commit -m "chore: format after theme switcher implementation"
```
