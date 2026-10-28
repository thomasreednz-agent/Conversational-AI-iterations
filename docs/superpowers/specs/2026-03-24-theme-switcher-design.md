# Theme Switcher Design Spec

**Date:** 2026-03-24
**Status:** Approved

## Goal

Add a floating theme switcher button (bottom-right corner) that opens a popover listing all 5 themes. Each option renders itself in its own theme so users can preview before selecting. Designed as a prototype/demo tool, not part of the real product UI.

## Themes

| Value | Label | CSS class on `:root` |
|---|---|---|
| `""` | Default | *(no class added)* |
| `"slick"` | Slick | `slick` |
| `"editorial-amber"` | Amber | `editorial-amber` |
| `"editorial-terracotta"` | Terracotta | `editorial-terracotta` |
| `"editorial-espresso"` | Espresso | `editorial-espresso` |

## Architecture

### State

`activeTheme: string` lives in `App.tsx`. When it changes, a `useEffect` clears all known theme classes from `document.documentElement` and adds the new one (empty string = no class = default theme).

Known theme classes to clear: `["slick", "editorial-amber", "editorial-terracotta", "editorial-espresso"]`

`activeTheme` and `onThemeChange` are passed as props to `ThemeSwitcher`.

### Component tree

```
App.tsx
├── MainLayout (existing)
└── ThemeSwitcher (new, fixed position, outside layout flow)
    └── Dialog (Vibe) — wraps the trigger; content= swatch list
        └── IconButton (trigger, rendered as Dialog's children = reference element)
```

**Dialog API note:** Vibe's `Dialog` uses a wrapper pattern — the trigger is its `children`, and the floating panel is the `content` prop. Props for toggle behavior: `showTrigger={["click"]}` and `hideTrigger={["clickoutside", "esckey"]}`. There is no separate open/close callback — only `onDialogDidShow`/`onDialogDidHide`.

```tsx
<Dialog
  content={<SwatchList ... />}
  showTrigger={["click"]}
  hideTrigger={["clickoutside", "esckey"]}
  position="top"
>
  <IconButton icon={ColorPicker} aria-label="Switch theme" />
</Dialog>
```

### Files

- **Modify:** `src/App.tsx` — add `activeTheme` state + effect + render `<ThemeSwitcher>`
- **Create:** `src/components/ThemeSwitcher.tsx` — trigger + dialog + swatch cards

## ThemeSwitcher Component

### Props

```ts
interface ThemeSwitcherProps {
  activeTheme: string;
  onThemeChange: (theme: string) => void;
}
```

### Trigger

A Vibe `IconButton` using the `ColorPicker` (or similar palette) icon from `@mondaydotcomorg/icons`. Positioned `fixed bottom-6 right-6 z-50`. When clicked, toggles the dialog open/closed.

### Dialog / Popover

Use Vibe's `Dialog` component anchored to the trigger button. Opens upward (position `top`). Contains a vertical list of `SwatchCard` components with a label at the top: "Theme".

### SwatchCard

Each card is a `<button>` element styled with its own theme class applied directly — so the card renders in its own colors. Layout:

```
[ color swatches row ]  Theme name    [ ✓ if active ]
```

**Color swatches row:** Three small inline color blocks showing:
1. `var(--primary-color)` — accent
2. `var(--primary-surface-color)` — surface
3. `var(--ui-background-color)` — UI fill

These are inline `<div>` elements with `background: var(--token-name)` — they automatically pick up the card's theme class.

**Active state:** Unicode `✓` shown when this theme is `activeTheme`. (`Check` icon does not exist in `@mondaydotcomorg/icons`.)

**On click:** Calls `onThemeChange(themeValue)` and closes the dialog.

## App.tsx Changes

```tsx
const THEME_CLASSES = ["slick", "editorial-amber", "editorial-terracotta", "editorial-espresso"];

const [activeTheme, setActiveTheme] = useState("");

useEffect(() => {
  THEME_CLASSES.forEach(cls => document.documentElement.classList.remove(cls));
  if (activeTheme) document.documentElement.classList.add(activeTheme);
}, [activeTheme]);
```

`ThemeSwitcher` rendered as a sibling of `MainLayout` inside a React Fragment (required since `App()` currently returns a single root element):

```tsx
return (
  <>
    <MainLayout product={product}>...</MainLayout>
    <ThemeSwitcher activeTheme={activeTheme} onThemeChange={setActiveTheme} />
  </>
);
```

## Accessibility

- Trigger button has `aria-label="Switch theme"`
- Dialog has `aria-label="Theme switcher"`
- Each swatch card is a `<button>` with `aria-pressed` reflecting active state
- Escape closes the dialog (Vibe `Dialog` handles this)

## CSS Selector Fix (globals.css)

The existing theme selectors in `globals.css` have a bug: they use `:root .slick` and `:root .editorial-*` (with a space = descendant combinator), which targets a child element. The switcher adds classes to `document.documentElement` (which IS `:root`), so selectors must use `:root.slick` (no space = class on root).

The shared editorial font group at line 98 is already correct (`:root.editorial-amber, ...`). The individual color blocks and the slick block need fixing.

**Fix:** Change these selectors in `globals.css`:

- `:root .slick {` → `:root.slick {`
- `:root .editorial-amber {` → `:root.editorial-amber {`
- `:root .editorial-terracotta {` → `:root.editorial-terracotta {`
- `:root .editorial-espresso {` → `:root.editorial-espresso {`

## What Does NOT Change

- `productConfig.ts` — no changes (product theming is independent of visual theme)
- Any other component files

## Files Changed

- `src/styles/globals.css` — fix 4 selector bugs (`:root .X` → `:root.X`)
- `src/App.tsx` — `activeTheme` state, theme-switching effect, Fragment wrapper, `<ThemeSwitcher>` rendered
- `src/components/ThemeSwitcher.tsx` — new file
