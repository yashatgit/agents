---
name: design-picker
description: >
  Generate interactive HTML mockups exploring 3-4 distinct design languages for any app.
  Helps teams visually compare themes side-by-side before committing to a direction.
  Outputs a single self-contained HTML file with live-switchable variations.
triggers:
  - User wants to explore or pick a design language/theme for an app
  - User says "design picker", "explore designs", "compare themes"
  - User wants to iterate on visual direction before building
inputs:
  - name: app_name
    description: Name of the app being designed
    required: true
  - name: app_description
    description: One-line description of what the app does and who it's for
    required: true
  - name: key_screens
    description: List of 2-4 key screens to mock up (e.g. "Dashboard, Detail view, Form")
    required: true
  - name: mood_keywords
    description: Vibe/mood keywords to guide theme generation (e.g. "minimal, warm, editorial")
    required: false
output: Single self-contained HTML file with all themes rendered and switchable
---

# Design Picker Skill

## Purpose

Before writing a single line of production CSS, explore the design space. This skill generates a self-contained HTML file with 3-4 distinct design language variations for your app's key screens. Each variation is fully styled and live-switchable, letting you compare directions side-by-side and iterate with confidence.

This skill is complementary to the **design-tokens** skill — use design-picker first to choose your direction, then use design-tokens to formalize the chosen theme into a production token system.

## Workflow

### Step 1: Gather Context

Collect from the user (or infer from project files):

| Input              | Example                                         |
| ------------------ | ----------------------------------------------- |
| **App name**       | Brew Better                                     |
| **Description**    | Pour-over coffee journal for home baristas       |
| **Key screens**    | Dashboard, Brew detail, New brew form            |
| **Mood keywords**  | warm, editorial, craft, minimal                  |

If mood keywords are not provided, generate 3-4 contrasting directions automatically based on the app's domain and audience.

### Step 2: Generate Theme Variations

Create **3-4 distinct design languages**. Each theme must differ meaningfully across these dimensions:

| Dimension          | What varies                                      |
| ------------------ | ------------------------------------------------ |
| **Color palette**  | Background, text, primary accent, surface colors |
| **Typography**     | Font pairing (display + body), scale, weight     |
| **Border style**   | Rounded vs sharp, border width, shadow vs flat   |
| **Density**        | Spacing scale, padding, content density          |
| **Personality**    | Formal vs playful, bold vs quiet, warm vs cool   |

Each theme gets:
- A short **name** (2-3 words, e.g. "Bold Editorial", "Soft Minimal")
- A one-line **description** of its personality
- A complete **CSS variable set** (backgrounds, text, accents, borders, radii, fonts)

#### Theme Generation Guidelines

- At least one theme should feel safe/conventional for the domain
- At least one theme should be unexpectedly bold or distinctive
- Themes should be genuinely different — not just color swaps of the same layout
- Every theme must be usable (sufficient contrast, readable text, clear hierarchy)
- Use Google Fonts (loaded via CDN) for typographic variety
- Each theme should tell a different story about the brand

### Step 3: Build Key Screens

For each key screen the user specified, create a representative mockup using:
- Realistic placeholder content relevant to the app domain
- Common UI patterns (cards, lists, forms, stats, navigation)
- Enough fidelity to judge the theme — not pixel-perfect, but emotionally accurate

Each screen should demonstrate:
- **Navigation** — how nav elements feel in this theme
- **Cards/surfaces** — how content containers look
- **Typography hierarchy** — headings, body, labels, captions
- **Interactive elements** — buttons, inputs, chips, tags
- **Data display** — numbers, ratings, status indicators
- **Empty/loading states** — if relevant to the screen

### Step 4: Assemble the HTML File

Output a **single self-contained HTML file** with:

```
┌─────────────────────────────────────────────┐
│  Theme Switcher Bar (top, always visible)    │
│  [Theme A] [Theme B] [Theme C] [Theme D]    │
├─────────────────────────────────────────────┤
│                                             │
│  Screen 1: Dashboard                        │
│  ┌───────────────────────────────────────┐  │
│  │  (full mockup in selected theme)      │  │
│  └───────────────────────────────────────┘  │
│                                             │
│  Screen 2: Detail View                      │
│  ┌───────────────────────────────────────┐  │
│  │  (full mockup in selected theme)      │  │
│  └───────────────────────────────────────┘  │
│                                             │
│  Screen 3: Form                             │
│  ┌───────────────────────────────────────┐  │
│  │  (full mockup in selected theme)      │  │
│  └───────────────────────────────────────┘  │
│                                             │
├─────────────────────────────────────────────┤
│  Theme Details Panel                        │
│  - Color swatches with hex values           │
│  - Font names and sizes                     │
│  - Border radius and spacing values         │
│  - CSS variable export (copy-ready)         │
└─────────────────────────────────────────────┘
```

#### HTML File Requirements

- **Self-contained**: No external dependencies except Google Fonts CDN
- **Instant switching**: Clicking a theme tab re-themes all screens immediately via CSS variable swap
- **Responsive**: Mockups should look reasonable on both desktop and mobile viewports
- **Theme details panel**: Bottom section shows the active theme's design tokens as copyable CSS variables
- **Keyboard navigation**: Arrow keys or number keys (1-4) to switch themes quickly
- **Side-by-side mode** (optional): A toggle to show 2 themes next to each other for direct comparison
- **Print-friendly**: Each theme should be printable for offline review

### Step 5: Iterate

After the user reviews the HTML file:

1. **If they pick a theme** — Extract its tokens and hand off to the design-tokens skill
2. **If they want refinements** — Generate a new HTML file mixing elements from multiple themes (e.g. "Theme A's colors with Theme C's typography")
3. **If none fit** — Ask for adjusted mood keywords and generate a fresh set
4. **If they like parts of multiple themes** — Create a hybrid theme combining the best elements

## Theme Naming Convention

Use evocative two-word names that communicate the theme's personality:

| Style              | Example names                                    |
| ------------------ | ------------------------------------------------ |
| Bold/strong        | Bold Editorial, Heavy Ink, Stark Contrast        |
| Soft/gentle        | Soft Linen, Quiet Paper, Gentle Warmth           |
| Modern/tech        | Neo Minimal, Glass Interface, Sharp System       |
| Warm/organic       | Craft Workshop, Warm Earth, Artisan Touch        |
| Dark/moody         | Dark Roast, Night Mode, Deep Carbon              |
| Playful/bright     | Bright Pop, Color Burst, Candy UI                |

## Anti-Patterns

- **Color-swap syndrome**: Themes that only differ in hue but share identical layout, spacing, and typography. Each theme must feel structurally different.
- **Unusable extremes**: Themes that look interesting but fail basic usability (low contrast, tiny text, confusing hierarchy). Every theme must be production-viable.
- **Kitchen sink screens**: Cramming every possible UI element into mockups. Show only what's needed to judge the theme's character.
- **Generic placeholder content**: Using "Lorem ipsum" or "User Name". Use domain-specific realistic content (e.g. "Ethiopia Yirgacheffe" for a coffee app, not "Product Name").
- **Font overload**: Using more than 2 fonts per theme. One display + one body font is the sweet spot.

## Self-Check

Before delivering the HTML file, verify:

- [ ] All 3-4 themes are genuinely distinct (not just recolored)
- [ ] Every theme passes WCAG AA contrast for body text
- [ ] Theme switching is instant (no page reload)
- [ ] All key screens are represented for every theme
- [ ] Realistic domain-specific content is used throughout
- [ ] CSS variables are clearly named and extractable
- [ ] The file opens correctly with no external dependencies (except Google Fonts)
- [ ] Keyboard shortcuts work for theme switching
- [ ] Mobile viewport looks reasonable

## Example Output Structure

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[App Name] — Design Exploration</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <!-- Google Fonts for all themes -->
  <style>
    /* Theme A: Bold Editorial */
    [data-theme="bold-editorial"] {
      --bg: #FAF7F2;
      --text: #1A1A1A;
      --primary: #C44B2B;
      --surface: #FFFFFF;
      --border: #1A1A1A;
      --radius: 0px;
      --font-display: 'DM Serif Display', serif;
      --font-body: 'Inter', sans-serif;
      /* ... */
    }

    /* Theme B: Soft Minimal */
    [data-theme="soft-minimal"] { /* ... */ }

    /* Theme C: ... */
    /* Theme D: ... */

    /* Shared layout styles using variables */
    /* Theme switcher UI */
    /* Screen mockup containers */
    /* Token display panel */
  </style>
</head>
<body data-theme="bold-editorial">
  <!-- Theme switcher bar -->
  <!-- Screen mockups -->
  <!-- Token details panel -->
  <script>
    // Theme switching logic
    // Keyboard shortcuts
    // Token copy-to-clipboard
  </script>
</body>
</html>
```

## Handoff to design-tokens

When the user selects a theme, extract its variables into the format expected by the design-tokens skill:

```
Primitives (raw values)
  → Semantic tokens (purpose-mapped)
    → Component styles (applied)
```

This bridges exploration (design-picker) to implementation (design-tokens).
