# GitHub Copilot — Code Review Instructions

# Focus: HTML & CSS Best Practices

---

## 🔍 Scope

Review all `.html`, `.css`, `.scss`, `.sass`, and template files (`.njk`, `.liquid`, `.hbs`, `.blade.php`, `.jsx`, `.tsx`, `.vue`).

---

## ✅ HTML — What to check

### Semantics

- Verify that structural elements use semantic tags: `<header>`, `<main>`, `<footer>`, `<section>`, `<article>`, `<aside>`, `<nav>`
- Flag any `<div>` or `<span>` used where a semantic element would be more appropriate
- Headings must follow a logical hierarchy: only one `<h1>` per page, no skipping levels (e.g. `<h1>` → `<h3>` without `<h2>`)
- Lists must use `<ul>`, `<ol>`, or `<dl>` — never simulate lists with `<br>` or plain text dashes
- Tables must only be used for tabular data, never for layout
- Buttons must use `<button>`, not `<div>`, `<span>`, or `<a>` without `href`
- Links (`<a>`) must have a meaningful `href`; avoid `href="#"` or `href="javascript:void(0)"`

### Forms

- Every `<input>`, `<select>`, and `<textarea>` must have an associated `<label>` (via `for`/`id` or wrapping)
- `<input>` elements must have an explicit and correct `type` attribute (`email`, `tel`, `number`, `password`, etc.)
- Forms must have a `<button type="submit">` or `<input type="submit">`
- Required fields must have the `required` attribute
- `autocomplete` should be set on relevant fields (`name`, `email`, `tel`, etc.)

### Accessibility (a11y)

- Images must have descriptive `alt` text; decorative images must have `alt=""`
- Interactive elements (buttons, links, inputs) must be keyboard-accessible and focusable
- `aria-label` or `aria-labelledby` must be present when the visual context alone is not enough
- `role` attributes must only be used when native semantics are insufficient
- Color must never be the sole means of conveying information
- Focus states must not be suppressed with `outline: none` unless a custom focus style replaces it

### Performance & Loading

- Images should have `width` and `height` attributes to prevent layout shift (CLS)
- Images below the fold should use `loading="lazy"`
- Critical images (LCP candidates) must NOT use `loading="lazy"`
- `<link rel="preload">` should be used for critical fonts and assets
- Inline `<style>` blocks inside `<body>` should be flagged and moved to external stylesheets
- Inline `onclick`, `onfocus`, and similar event handlers in HTML should be flagged and moved to JS files

### Document Structure

- Every HTML file must have `<!DOCTYPE html>` on the first line
- The `<html>` tag must have a `lang` attribute (e.g. `lang="pt-BR"`)
- `<meta charset="UTF-8">` must be the first tag inside `<head>`
- `<meta name="viewport" content="width=device-width, initial-scale=1">` must be present
- `<title>` must be present and descriptive (not generic like "Page" or "Untitled")
- CSS files must be loaded in `<head>`; JS files should be loaded before `</body>` or use `defer`/`async`

---

## ✅ CSS — What to check

### Selectors & Specificity

- Flag overly specific selectors (e.g. `div#main > ul.list > li.item > a.link`)
- Flag use of `!important` unless inside a utility class or reset; always request a comment explaining why
- Flag ID selectors (`#element`) used for styling — IDs should be reserved for JS hooks and anchor links
- Flag deeply nested selectors (more than 3 levels) in SCSS/SASS
- Flag use of element selectors (`div`, `p`, `span`) combined with classes unnecessarily

### Naming & Organization

- Class names must be descriptive and reflect purpose, not appearance (`.card-title` ✅, `.red-bold-text` ❌)
- If BEM is used in the project, verify the pattern is followed consistently: `block__element--modifier`
- CSS custom properties (variables) must be used for repeated values: colors, spacing, font sizes, breakpoints
- Flag hardcoded color hex/rgb values that are not assigned to a variable
- Flag magic numbers (e.g. `margin-top: 37px`) without a comment explaining the value
- Group related properties in a consistent order: positioning → box model → typography → visual → animation

### Layout

- Flag use of `float` for layout (acceptable only for text wrapping around images)
- Prefer `flexbox` or `grid` for layout; flag legacy hacks like `display: table` used for alignment
- Flag fixed pixel widths on containers that should be fluid/responsive
- Verify that `grid` and `flex` properties are used correctly and not redundantly

### Responsive Design

- All layouts must be mobile-first: base styles for small screens, enhancements via `min-width` media queries
- Flag any media queries using `max-width` in a codebase that follows mobile-first convention
- Flag hardcoded pixel values for font sizes — prefer `rem` or `clamp()`
- Flag hardcoded pixel values for spacing when a spacing scale/variable exists
- Verify touch targets are at least 44×44px for interactive elements on mobile

### Typography

- `font-size` on the `<body>` or `:root` should not be set in `px` — use `%` or `rem` base
- Line height (`line-height`) should be unitless (e.g. `1.5`, not `1.5rem`)
- `font-family` must always end with a generic fallback (`serif`, `sans-serif`, `monospace`)
- Avoid `text-transform: uppercase` on long text blocks for readability reasons

### Animation & Transitions

- Animations must respect `prefers-reduced-motion`:
  ```css
  @media (prefers-reduced-motion: reduce) {
    * {
      animation: none;
      transition: none;
    }
  }
  ```
- Flag animations that animate `width`, `height`, `top`, `left` — prefer `transform` and `opacity` for performance
- Transitions should have a duration between 150ms–400ms; flag anything above 600ms unless intentional

### Resets & Global Styles

- Verify `box-sizing: border-box` is applied globally
- Verify a CSS reset or normalize is in place
- Flag removal of `:focus-visible` styles without a custom replacement

### Browser Compatibility & Vendor Prefixes

- Flag CSS features without fallbacks for relevant browser targets
- Flag manual vendor prefixes (`-webkit-`, `-moz-`) that are now unnecessary for modern browser targets
- Flag use of deprecated properties (e.g. `zoom`, `-ms-filter`)

---

## 🚫 Always flag (critical issues)

- Empty CSS rules or selectors with no declarations
- Duplicate CSS rules for the same property on the same selector
- `display: none` applied to elements that should be hidden accessibly — use `visibility: hidden` or `sr-only` where appropriate
- CSS files larger than 50KB that have no evidence of being split or lazy-loaded
- Any hardcoded credential, API key, or token inside HTML comments or `data-*` attributes
- `<script>` tags loading external resources without `integrity` (SRI) where applicable

---

## 💡 Suggestions (non-blocking, but worth noting)

- Opportunities to replace repeated patterns with a reusable class or component
- CSS that could be simplified using modern features (`gap` instead of margins, `aspect-ratio`, `clamp()`, `container queries`)
- Images that could benefit from using `<picture>` with multiple sources for format/resolution switching
- Places where a CSS custom property would reduce duplication

---

## 🔕 Ignore

- Vendor/third-party CSS files (e.g. `node_modules/`, `vendor/`, `dist/`)
- Auto-generated files (e.g. compiled SCSS output if source `.scss` files are also present)
- Files explicitly marked with `/* copilot-ignore */` at the top
