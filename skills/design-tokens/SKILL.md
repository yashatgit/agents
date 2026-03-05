---
name: design-tokens
description: |
  Enforces correct use of semantic design tokens and design system components in frontend code.
  Use when: (1) Writing new UI components or pages, (2) Reviewing or fixing color/styling issues,
  (3) An agent produces hardcoded color utilities (slate-*, zinc-*, gray-*), (4) Building or
  modifying ds-components, (5) Choosing between text-foreground / text-secondary-foreground /
  text-muted-foreground, (6) Any Tailwind className authoring that involves color, border, ring,
  shadow, or background.
  Do NOT use for layout-only work (flex, grid, spacing) that has no color decisions.
---

# Design Tokens & Semantic Styling

This skill teaches you to write frontend styles that are theme-aware, consistent, and maintainable. Every color decision must go through the semantic token layer — never reach for raw palette values.

---

## 1. Why Semantic Tokens Exist

A design system has three layers:

```
Primitives   →   Semantic Tokens   →   Component Styles
(zinc-500)       (muted-foreground)     (text-muted-foreground)
```

**Primitives** are raw color values (hex, oklch, rgb). They describe a color, not a purpose.
**Semantic tokens** map a purpose to a primitive. `muted-foreground` means "de-emphasized text" — it resolves to zinc-500 in light mode and zinc-400 in dark mode.
**Component styles** consume semantic tokens via utility classes.

### The Rule

> Never reference a primitive in component code. Always use the semantic token.

**Why:**
- Primitives break in dark mode (a hardcoded `zinc-200` border becomes invisible on `zinc-950` background)
- Primitives scatter color decisions across hundreds of files instead of one token definition
- Primitives make redesigns require find-and-replace across the entire codebase

---

## 2. The Token Vocabulary

Every design system defines its own tokens, but the pattern is universal. Learn the **roles**, not the names.

### Surface Tokens (backgrounds)

| Role | Token | When to Use |
|------|-------|-------------|
| Page background | `background` | Root page canvas |
| Card / panel | `card` | Elevated containers with border/shadow |
| Popover / dropdown | `popover` | Floating overlays |
| Subtle fill | `muted` | Backgrounds for tags, badges, code spans, hover states |
| Interactive fill | `accent` | Hover/active states on list items, menu items |
| Primary fill | `primary` | Primary action buttons, strong visual emphasis |
| Secondary fill | `secondary` | Secondary action buttons, softer emphasis |
| Danger fill | `destructive` | Delete buttons, error states |

**Decision tree for backgrounds:**
1. Is it floating over other content? → `popover`
2. Is it an elevated container with a border? → `card`
3. Is it a subtle tint behind inline content? → `muted`
4. Is it a hover/active state? → `accent`
5. Is it the page itself? → `background`

### Text Tokens (3-tier hierarchy)

| Tier | Token | Purpose | Examples |
|------|-------|---------|----------|
| **Primary** | `foreground` | Headings, labels, primary body text | Page titles, button labels, active nav items |
| **Secondary** | `secondary-foreground` | Standard body text, descriptions | Paragraph text, form labels, table cells |
| **Muted** | `muted-foreground` | De-emphasized, supplementary | Timestamps, placeholders, helper text, metadata |

**Decision tree for text color:**
1. Is it a heading, title, or the most important text on screen? → `foreground`
2. Is it body text the user should read? → `secondary-foreground`
3. Is it supplementary info the user can skip? → `muted-foreground`

**The default is `secondary-foreground`** for body text. Only reach for `muted-foreground` when content is genuinely secondary (timestamps, placeholders, disabled labels).

### Foreground Pairing

Every surface token has a `-foreground` counterpart for text placed on that surface:

| Surface | Text on it |
|---------|-----------|
| `bg-primary` | `text-primary-foreground` |
| `bg-secondary` | `text-secondary-foreground` |
| `bg-destructive` | `text-destructive-foreground` |
| `bg-card` | `text-card-foreground` |
| `bg-popover` | `text-popover-foreground` |
| `bg-muted` | `text-muted-foreground` |
| `bg-accent` | `text-accent-foreground` |

> When you set a background, always pair it with the matching foreground for text within that surface.

### Edge Tokens (borders, rings, dividers)

| Token | Purpose |
|-------|---------|
| `border` | Default border color for cards, inputs, separators |
| `input` | Border color specifically for form inputs |
| `ring` | Focus ring color for interactive elements |

### Contextual Token Sets

Some design systems define token sets scoped to a context (e.g., sidebar, navigation). These follow the same pattern but with a prefix:

```
sidebar, sidebar-foreground, sidebar-primary, sidebar-accent, sidebar-border
```

Use contextual tokens only inside their context. Don't use `sidebar-border` outside the sidebar.

---

## 3. Common Anti-Patterns

### Anti-Pattern 1: Hardcoded Palette Values

```tsx
// WRONG — breaks in dark mode, not theme-aware
<div className="bg-slate-100 text-slate-600 border-slate-200">

// CORRECT
<div className="bg-muted text-secondary-foreground border-border">
```

### Anti-Pattern 2: Wrong Text Tier

```tsx
// WRONG — body text should not use muted-foreground
<p className="text-muted-foreground">Account has 3 open tickets requiring attention.</p>

// CORRECT — this is content the user should read
<p className="text-secondary-foreground">Account has 3 open tickets requiring attention.</p>
```

```tsx
// WRONG — a heading using secondary-foreground
<h2 className="text-secondary-foreground font-semibold">Account Health</h2>

// CORRECT — headings use foreground
<h2 className="text-foreground font-semibold">Account Health</h2>
```

### Anti-Pattern 3: Missing Foreground Pairing

```tsx
// WRONG — text color not paired with background
<div className="bg-primary text-white">Submit</div>

// CORRECT — use the token pair
<div className="bg-primary text-primary-foreground">Submit</div>
```

### Anti-Pattern 4: Icon/Text Color Mismatch

```tsx
// WRONG — icon and label have different color tokens
<div className="flex items-center gap-2">
  <HomeIcon className="w-5 h-5 text-muted-foreground" />
  <span className="text-foreground">Home</span>
</div>

// CORRECT — set color on parent, let icon inherit
<div className="flex items-center gap-2 text-secondary-foreground">
  <HomeIcon className="w-5 h-5" />
  <span>Home</span>
</div>
```

### Anti-Pattern 5: Opacity as a Color System

```tsx
// WRONG — using opacity to fake a lighter shade
<p className="text-foreground/50">Helper text</p>

// CORRECT — use the semantic tier
<p className="text-muted-foreground">Helper text</p>
```

Opacity on tokens is acceptable for **borders and dividers** (e.g., `border-border/70`), where the intent is visual softening of a structural element. Never use opacity to create a text hierarchy — that's what the 3-tier system is for.

---

## 4. Intentional Exceptions

Some colors are intentionally outside the token system. Don't "fix" these:

- **Status colors**: `text-red-600`, `bg-emerald-50`, `text-amber-500` — these carry fixed semantic meaning (error, success, warning) that shouldn't change with theme
- **Brand / category colors**: Per-brand color assignments, chart colors — these are data-driven
- **Attribution / pill colors**: Typed pills that use fixed hue families (indigo for meetings, sky for email, purple for tickets) — the hue IS the meaning

**How to tell the difference:**
- If the color communicates **status or category** → hardcoded color is correct
- If the color communicates **UI structure** (background, border, text hierarchy) → must use a token

---

## 5. Component Token Cheatsheet

Quick reference for the most common UI patterns:

```
Card:           bg-card text-card-foreground border rounded-lg shadow-sm
Input:          border-input bg-transparent placeholder:text-muted-foreground
Button primary: bg-primary text-primary-foreground
Button ghost:   hover:bg-accent hover:text-accent-foreground
Badge:          bg-muted text-secondary-foreground (or bg-primary text-primary-foreground)
Separator:      bg-border
Focus ring:     focus-visible:ring-ring/50 focus-visible:border-ring
Error state:    border-destructive text-destructive
Disabled:       text-muted-foreground opacity-50
Code inline:    bg-muted text-secondary-foreground border-border
Hover row:      hover:bg-accent
Selected item:  bg-accent text-accent-foreground
Sidebar item:   text-sidebar-foreground hover:bg-sidebar-accent
```

---

## 6. Self-Check Before Writing Styles

Run through this checklist for every className you author:

1. **Does any class contain a raw palette name?** (`slate-*`, `zinc-*`, `gray-*`, `white`, `black`)
   - If yes: replace with semantic token. Exception: status colors (red, green, amber).

2. **Does every `bg-*` have a matching `text-*-foreground`?**
   - `bg-primary` needs `text-primary-foreground`, not `text-white`.

3. **Is the text color the right tier?**
   - Heading → `foreground`
   - Body → `secondary-foreground`
   - Helper → `muted-foreground`

4. **Do sibling icon + text share one color?**
   - Set on parent, both inherit.

5. **Am I using opacity to fake a color tier?**
   - Replace `text-foreground/60` with the correct semantic tier.

6. **Is this a known exception?** (status color, brand color, attribution pill)
   - If yes: hardcoded is correct.

---

## 7. shadcn/ui Specifics

If the project uses **shadcn/ui** as its component library, these additional conventions apply:

### Token Source

shadcn/ui components expect CSS custom properties on `:root` and `.dark`:

```css
:root {
  --background: <value>;
  --foreground: <value>;
  --primary: <value>;
  --primary-foreground: <value>;
  /* ... */
}
```

These are bridged to Tailwind via `@theme inline` (Tailwind 4) or `tailwind.config.ts` theme extension (Tailwind 3). The bridge makes `bg-primary` resolve to `var(--primary)`.

### Component Installation

shadcn components install into a dedicated directory (typically `ds-components/` or `components/ui/`). These are **your code** — you own and modify them. They are not node_modules.

### Data Attributes

shadcn components use `data-slot` and `data-variant` attributes for CSS targeting:

```tsx
// Target from a parent:
<div className="[&_[data-slot=icon-button]]:text-white">
  <IconButton>...</IconButton>
</div>
```

Never target shadcn internals by tag name or generated class. Use `data-slot` selectors.

### Variant Authoring with CVA

When adding variants to shadcn components, always use semantic tokens:

```tsx
const buttonVariants = cva("...", {
  variants: {
    variant: {
      // CORRECT — semantic tokens
      default: "bg-primary text-primary-foreground",
      outline: "border border-input bg-background hover:bg-accent",

      // WRONG — hardcoded palette
      default: "bg-zinc-900 text-white",
      outline: "border border-zinc-200 bg-white hover:bg-zinc-100",
    }
  }
});
```

### Don't Duplicate What shadcn Provides

Before adding classes to a shadcn component wrapper, check what the component already includes. Common duplicates:

- `DialogContent` already has `rounded-lg`, `border`, `shadow-lg`, `bg-background`
- `Card` already has `rounded-lg`, `border`, `shadow-sm`, `bg-card`
- `Button` already has `cursor-pointer` and focus ring styles

### New Component Checklist

Before building a new component:

1. Check if shadcn has it: [ui.shadcn.com/docs/components](https://ui.shadcn.com/docs/components)
2. Check if it's already installed in the ds-components directory
3. If neither: ask before installing or building from scratch

---

## 8. Token Pipeline Reference

For projects with a generated token system, the flow is:

```
Source of truth (tokens.js / tokens.json / Figma)
    ↓  generation script
CSS custom properties (:root / .dark)
    ↓  Tailwind theme bridge
Utility classes (bg-primary, text-muted-foreground, border-border)
    ↓  component code
Final rendered styles
```

**Never edit generated CSS files directly.** Always modify the source of truth and regenerate.
