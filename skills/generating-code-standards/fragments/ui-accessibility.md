<!-- tier: project -->
<!-- requires: none -->

## Accessibility Standards

<!-- WHY: Accessibility isn't a feature — it's a quality bar. Inaccessible UI excludes real users and creates legal liability (ADA, EAA). Catching issues during development costs a fraction of retrofitting later. -->

### Minimum Standard

Target **WCAG 2.1 AA** compliance. This covers the vast majority of accessibility requirements.

### Non-Negotiable Rules

1. **Semantic HTML first.** Use `<button>`, `<nav>`, `<main>`, `<form>`, `<label>` — not `<div onClick>`. If a native element exists for the purpose, use it.

2. **Every interactive element is keyboard-accessible.** Tab order must be logical. Focus must be visible. No keyboard traps.

3. **Every image has alt text.** Decorative images get `alt=""`. Informative images get descriptive alt text. No `alt="image"` or `alt="photo"`.

4. **Form inputs have labels.** Every `<input>` has an associated `<label>` (via `htmlFor`/`for` or wrapping). Placeholder text is not a label.

5. **Color is not the only indicator.** Don't convey information with color alone — add icons, text, or patterns. Minimum contrast ratio: 4.5:1 for text, 3:1 for large text and UI components.

6. **Dynamic content is announced.** Use ARIA live regions (`aria-live="polite"`) for content that updates without a page load (toasts, status messages, loading states).

### Testing Requirements

- Run **axe-core** checks on every page/component in CI (see `testing-frontend.md`)
- Manual keyboard navigation test before major releases
- Screen reader testing for critical user flows (VoiceOver on Mac, NVDA on Windows)

### Common Mistakes

| Mistake | Fix |
|---------|-----|
| `<div onClick>` instead of `<button>` | Use `<button>`. Divs have no keyboard handling or role. |
| Missing focus management after route change | Move focus to main content or heading on navigation |
| Modal doesn't trap focus | Focus must cycle within the modal while open |
| Icon-only buttons without labels | Add `aria-label` or visually hidden text |
| Auto-playing media | Never auto-play. If unavoidable, provide pause control and no audio by default |
