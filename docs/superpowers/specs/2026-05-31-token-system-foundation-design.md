# Token System Foundation — Design Spec

**Date:** 2026-05-31  
**Status:** Approved  
**Scope:** Surface tokens, shadow pairing, radius scale, platform shell tokens, deprecation roadmap

---

## Context

Vibe's surface, radius, and border tokens were grown organically with no unified semantic model. Names describe colors instead of roles, hidden duplicates exist, cross-theme bugs affect production, and ~9.3M Figma usages across 78 teams are built on the current token set.

**Core constraint:** Zero breaking changes. All old token names must continue to work. No forced migration. Alias-bridge strategy throughout — old names become CSS aliases pointing to new canonical tokens.

---

## 1. Surface Tokens

One unified `surface-*` scale covering both elevation and semantic use cases. No separate `fill-*` scale.

### Elevation (structural containers)

| Token | Role |
|---|---|
| `--surface-sunken` | Wells, board backgrounds behind cards |
| `--surface-base` | Default page/panel background |
| `--surface-raised` | Cards, panels lifted off the base |
| `--surface-overlay` | Modals, popovers, dropdowns, sheets |
| `--surface-inverted` | High-contrast dark/inverted regions |

Each elevation token ships interaction states: `-hover`, `-pressed`, `-selected`, `-disabled`.

### Semantic (light tint backgrounds for status/brand)

| Token | Role |
|---|---|
| `--surface-brand` | Brand-tinted highlight backgrounds |
| `--surface-positive` | Success/green tint (banners, toasts) |
| `--surface-negative` | Error/red tint (alerts, destructive feedback) |
| `--surface-warning` | Warning/yellow tint |
| `--surface-info` | Informational/blue tint |

Background tints only — not button fills, not full saturated colors.

### Special

| Token | Notes |
|---|---|
| `--backdrop-default` | Default modal scrim, theme-aware value per theme |

### Old tokens → CSS aliases (zero breaking change)

Old token names are kept as CSS aliases pointing to the new canonical tokens. Fixing a value on the new token automatically fixes it for all consumers of the old name.

| Old token | Alias of | Notes |
|---|---|---|
| `--primary-background-color` | `--surface-base` | #3 most-used token in Vibe (5.75M usages) |
| `--allgrey-background-color` | `--surface-sunken` | The "board background" pattern |
| `--grey-background-color` | `--surface-sunken` | Duplicate of allgrey |
| `--dark-background-color` | `--surface-sunken` | Duplicate of allgrey |
| `--ui-background-color` | `--surface-raised` | "Elevated grey" = raised |
| `--ui-background-hover-color` | `--surface-raised-hover` | |
| `--modal-background-color` | `--surface-overlay` | Fixes black-theme cross-theme bug |
| `--dialog-background-color` | `--surface-overlay` | Collapses with modal |
| `--inverted-color-background` | `--surface-inverted` | |
| `--backdrop-color` | `--backdrop-default` | |
| `--disabled-background-color` | `--surface-base-disabled` | |
| `--box-shadow-mediun` (typo) | `--box-shadow-medium` | Bug today: wrong dark-theme value; alias fixes it |

### Tokens requiring usage audit before aliasing

These cannot be safely aliased without first determining intent per usage.

| Token | Problem | Required action |
|---|---|---|
| `--secondary-background-color` (1.36M) | Same value as primary in light; distinct in dark/hacker. Intent varies per usage. | Audit 50–100 files from Boards, CRM, Automations to determine `surface-base` vs `surface-raised` split |
| `--primary-surface-color` (357K) | Different semantic meaning in every theme — cannot map to one target | Mark deprecated in docs; case-by-case migration per usage |
| `--color-surface` | Missing from hacker theme entirely | Audit code usage; likely maps to `--surface-inverted` |
| `--label-background-color` | Info-tinted control background | Maps to `--surface-info` after audit |

---

## 2. Shadow Tokens

**No name changes.** `--box-shadow-xs/small/medium/large` keep their current names.

The new model adds **pairing rules** — each elevated surface has a required shadow partner:

| Shadow token | Paired surface | Notes |
|---|---|---|
| `--box-shadow-small` | `--surface-raised` | Use together |
| `--box-shadow-medium` | `--surface-overlay` | Use together |
| `--box-shadow-xs` | Inset/subtle only | Not part of the elevation hierarchy |
| `--box-shadow-large` | Special use only | Use sparingly; not in standard elevation model |

In dark/black themes, shadows can be softened or no-op'd — tonal difference between `surface-raised` and `surface-base` carries the elevation instead.

---

## 3. Radius Tokens

### Scale

| Token | Value | Status |
|---|---|---|
| `--border-radius-small` | 4px | keep (unchanged) |
| `--border-radius-medium` | 8px | keep (unchanged) |
| `--border-radius-large` | 12px | **new** |
| `--border-radius-xlarge` | 16px | **new** — canonical replacement for `big` |
| `--border-radius-full` | 999px | **new** |
| `--border-radius-big` | alias → `--border-radius-xlarge` | deprecated, alias for backward compat |

### Usage guidelines

| Element type | Token |
|---|---|
| Buttons, inputs, tags, compact UI | `border-radius-small` (4px) |
| Cards, columns, dropdowns, in-page containers | `border-radius-medium` (8px) |
| Modals, large dialogs, prominent surfaces | `border-radius-large` (12px) |
| Large panels, chat shells | `border-radius-xlarge` (16px) |
| Avatars, pills, toggles, chips | `border-radius-full` (999px) |

---

## 4. Platform Shell Tokens

Additive only. No changes to `--layout-border-color` (product/in-app layout) or any existing token.

| New token | Suggested value | Use for |
|---|---|---|
| `--platform-border-color` | `#e6e9ef` (validate with layout team) | Top bar, main nav, shell dividers |
| `--platform-border-color-subtle` | `#eef0f6` (optional) | Low-emphasis shell separators |
| `--platform-surface-color` | alias of `--surface-sunken` or new | Platform canvas behind app |

**Governance rule:** `--layout-border-color` is for product/in-app layout only. `--platform-border-color` is for persistent platform chrome only.

---

## 5. Deprecation Roadmap

### Phase 1 — Additive (Month 1–2)
- Ship all new `surface-*`, `backdrop-default`, `border-radius-large/xlarge/full`, `platform-*` tokens
- Establish CSS aliases for all Tier 1 old tokens (the clean direct-rename cases)
- Fix cross-theme bugs in new token values; aliases inherit the fix automatically
- Zero breaking changes

### Phase 2 — Adoption & Audit (Month 2–4)
- Mark old tokens as deprecated in Figma variables and code docs
- Add lint warnings for old token usage in product repos
- Run usage audit on `secondary-background-color`, `primary-surface-color`, `color-surface`
- Migrate Vibe's own components to new tokens first (validates model before product teams)
- Prioritize Boards, monday service, CRM (highest usage — ~2.3M usages combined)

### Phase 3 — Deprecation Campaign (Quarter 2–3)
- Per-team migration support: Figma bulk swap tooling + CSS codemod for product repos
- Track adoption via Figma Analytics variable usage data
- Target: >80% of usages on new tokens before scheduling removal

### Phase 4 — Removal (Next major version, 12–18 months out)
- Remove deprecated aliases
- Breaking change, announced well in advance with migration guide
- `--border-radius-big` removal happens here

---

## Open Questions

1. **`secondary-background-color` audit** — what % of 1.36M usages are `surface-raised` vs `surface-base`? Determines if we can alias or need per-file migration.
2. **Interaction states scope** — ship `-hover/-pressed/-selected/-disabled` on all elevation tokens in Phase 1, or add incrementally?
3. **Semantic surface interaction states** — do `surface-positive/-negative` etc. need hover/pressed states, or are they background-only?
4. **`platform-border-color` value** — needs validation from layout framework team on real shell mocks.
5. **Shadow → variable migration** — shadows are Figma *effect styles* today, not variables. Converting to variables would enable better pairing enforcement. Separate workstream.

---

## Verification

1. CSS aliases resolve correctly: each old token name → same hex value per theme as before
2. Cross-theme bugs fixed: `--modal-background-color` in black theme renders correct overlay color
3. `--border-radius-big` alias resolves to same 16px value as before
4. Figma variables publish correctly for all new tokens
5. Spot-check top consumer (Boards) renders correctly with no code changes required
