---
name: wcag-audit
description: "Audit a repository for WCAG conformance at a chosen level (A, AA, or AAA), report the status per success criterion, then interactively implement the fixes the user selects. Handles alt text, form labels, color contrast, keyboard access, focus, landmarks, ARIA, and more."
argument-hint: "[path-to-project]"
---

# WCAG Conformance Audit & Fix

Audit a repository's front-end against the **Web Content Accessibility Guidelines (WCAG 2.2)**, report where it stands, and interactively implement the fixes the user chooses.

The target project directory is: `$ARGUMENTS` (default: current working directory if not specified).

Follow the steps **in order**. Do not skip Step 0 — the target conformance level determines which success criteria are in scope.

---

## Step 0: Ask which conformance level to target

**This is the first thing you do.** Before scanning anything, ask the user which WCAG conformance level they want to audit and implement against, using the `AskUserQuestion` tool.

- **A** — Minimum. Essential baseline; blocks nobody outright.
- **AA** — Standard. The level most laws and policies require (EN 301 549, ADA, Section 508, European Accessibility Act). **Recommend this as the default.**
- **AAA** — Enhanced. Aspirational; not required across an entire site and often impractical for all content.

Conformance is cumulative: **AA includes all of A**, and **AAA includes all of A and AA**. Audit every criterion at or below the chosen level.

Record the chosen level — every later step is scoped to it.

---

## Step 1: Discover the front-end surface

Identify what there is to audit before auditing it.

1. Read `package.json` (if present) to detect the framework: React/Next, Vue/Nuxt, Svelte, Angular, Astro, plain HTML, a templating engine, etc.
2. Locate the files that render UI:
   - `.html`, `.htm`
   - `.jsx`, `.tsx`, `.vue`, `.svelte`, `.astro`
   - Server templates: `.ejs`, `.hbs`, `.pug`, `.erb`, `.twig`, `.blade.php`
   - Global styles: `.css`, `.scss`, and design tokens / theme files
3. Note any existing accessibility tooling already in the repo (e.g. `eslint-plugin-jsx-a11y`, `axe-core`, `pa11y`, `@axe-core/playwright`, Storybook a11y addon). Existing config tells you what's already enforced.

If the repo has **no** front-end surface (pure backend/library), tell the user WCAG does not apply and stop.

---

## Step 2: Audit against the in-scope success criteria

Statically review the UI files against the criteria below, up to and including the chosen level. WCAG is primarily about rendered output, so a static scan cannot prove conformance for everything — mark anything you cannot verify from source as **Needs review** rather than guessing.

### Level A

| # | Criterion | What to check in source |
|---|---|---|
| 1.1.1 | Non-text Content | Every `<img>` has meaningful `alt`; decorative images use `alt=""`; `<svg>`/icon buttons have accessible names; `<input type="image">` has `alt`. |
| 1.3.1 | Info & Relationships | Semantic HTML over `<div>` soup; real headings (`<h1>`–`<h6>`) in order; lists use `<ul>/<ol>`; tables use `<th>`/`scope`; form fields tied to `<label>`. |
| 1.3.2 | Meaningful Sequence | DOM order matches reading order; no layout that depends on CSS to make sense. |
| 2.1.1 | Keyboard | All interactive elements are natively focusable or have `tabindex`; no click-only handlers on `<div>`/`<span>` without keyboard equivalents. |
| 2.1.2 | No Keyboard Trap | Modals/widgets can be exited with the keyboard. |
| 2.4.1 | Bypass Blocks | A "skip to content" link or landmark structure exists. |
| 2.4.2 | Page Titled | Each page/route sets a descriptive `<title>`. |
| 2.4.3 | Focus Order | Tab order follows a logical, meaningful sequence. |
| 2.4.4 | Link Purpose (In Context) | Link text describes its destination; no bare "click here"/"read more". |
| 3.1.1 | Language of Page | `<html lang="…">` is set. |
| 3.3.1 | Error Identification | Form errors are identified in text, not by color alone. |
| 3.3.2 | Labels or Instructions | Inputs have visible labels/instructions. |
| 4.1.2 | Name, Role, Value | Custom widgets expose correct roles/states via ARIA; icon-only controls have `aria-label`. |

### Level AA (adds to A)

| # | Criterion | What to check in source |
|---|---|---|
| 1.4.3 | Contrast (Minimum) | Text vs background ≥ 4.5:1 (≥ 3:1 for large text). Compute from CSS colors/tokens. |
| 1.4.4 | Resize Text | No fixed `px` that breaks at 200% zoom; use relative units where practical. |
| 1.4.5 | Images of Text | Real text instead of text baked into images. |
| 1.4.10 | Reflow | Content reflows at 320 CSS px with no 2-D scrolling; no fixed-width layouts. |
| 1.4.11 | Non-text Contrast | UI components/graphics vs adjacent colors ≥ 3:1 (borders, icons, focus rings). |
| 1.4.12 | Text Spacing | Layout survives increased line/letter/word spacing; no clipped text. |
| 2.4.5 | Multiple Ways | More than one way to reach a page (nav + search/sitemap). |
| 2.4.6 | Headings & Labels | Headings and labels are descriptive. |
| 2.4.7 | Focus Visible | A visible focus indicator exists; no blanket `outline: none` without a replacement. |
| 2.4.11 | Focus Not Obscured (Min) | Focused element isn't hidden behind sticky headers/footers. |
| 2.5.8 | Target Size (Min) | Interactive targets ≥ 24×24 CSS px (or adequate spacing). |
| 3.2.3 | Consistent Navigation | Navigation is consistent across pages. |
| 3.2.4 | Consistent Identification | Same-function components are identified consistently. |
| 3.3.3 | Error Suggestion | Error messages suggest how to fix the input. |
| 3.3.4 | Error Prevention | Legal/financial/data submissions are reversible/checked/confirmed. |
| 4.1.3 | Status Messages | Status updates use `role="status"`/`aria-live` so they're announced without focus change. |

### Level AAA (adds to A + AA)

Audit these only if the user chose AAA. Highlights: **1.4.6 Contrast (Enhanced)** (≥ 7:1 text, ≥ 4.5:1 large), **2.1.3 Keyboard (No Exception)**, **2.2.3 No Timing**, **2.3.2 Three Flashes**, **2.4.8 Location**, **2.4.9 Link Purpose (Link Only)**, **2.4.12 Focus Not Obscured (Enhanced)**, **2.5.5 Target Size (Enhanced)** (≥ 44×44 px), **3.1.3–3.1.5 Reading Level & unusual words**, **3.3.7 Redundant Entry**, **3.3.9 Accessible Authentication (Enhanced)**. Treat most of these as **Needs review** unless the source clearly shows a pass or fail.

> If the repo already has an a11y linter/test, run it and fold its results into the audit rather than re-deriving everything by hand.

---

## Step 3: Report the status

Present the results as a clear per-criterion table, grouped by level, covering **only** the in-scope criteria. Use these statuses:

- **Pass** — verifiably met in source.
- **Fail** — a concrete violation found (cite `file:line`).
- **Needs review** — cannot be confirmed statically (e.g. contrast on dynamic content, runtime focus behavior).
- **N/A** — the criterion doesn't apply (e.g. no forms, no tables).

```
## WCAG 2.2 Audit — Target Level: AA

### Level A
| # | Criterion | Status | Findings |
|---|-----------|--------|----------|
| 1.1.1 | Non-text Content | Fail | 6 <img> without alt — src/Hero.tsx:12, src/Card.tsx:20, … |
| 1.3.1 | Info & Relationships | Pass | Semantic headings and labelled inputs throughout |
| 3.1.1 | Language of Page | Fail | <html> missing lang attribute — index.html:2 |

### Level AA
| # | Criterion | Status | Findings |
|---|-----------|--------|----------|
| 1.4.3 | Contrast (Minimum) | Fail | .btn-secondary #9aa on #fff = 2.1:1 — styles/buttons.css:44 |
| 2.4.7 | Focus Visible | Fail | Global `outline: none` with no replacement — styles/base.css:8 |

### Summary
- Pass: 14 · Fail: 5 · Needs review: 3 · N/A: 2
```

Then group the failing (and, if the user wants, "Needs review") criteria into a short list of **implementation topics** — one topic per fixable theme, so the user chooses themes, not raw criterion numbers. Example topics:

- **Alt text** — add alternative text to images/icons (1.1.1)
- **Page language** — set `<html lang>` (3.1.1)
- **Form labels & errors** — associate labels, describe errors (1.3.1, 3.3.1–3.3.3)
- **Color contrast** — fix low-contrast text/UI colors (1.4.3, 1.4.11)
- **Focus visibility** — restore visible focus indicators (2.4.7)
- **Keyboard access** — make custom controls operable by keyboard (2.1.1, 2.1.2)
- **Landmarks & skip link** — add landmarks and a skip-to-content link (1.3.1, 2.4.1)
- **Accessible names & roles** — ARIA on icon-only/custom widgets (4.1.2)

If **no** failures were found, congratulate the user, note any **Needs review** items that still warrant manual/assistive-tech testing, and stop.

---

## Step 4: Ask which topics to implement

For the topics identified in Step 3, ask the user which to implement using the `AskUserQuestion` tool with **`multiSelect: true`**. Offer:

- **Implement all** — apply fixes for every topic.
- One option **per topic** — so the user can pick one or several.

Respect the selection exactly. If the user picks "Implement all", queue every topic; otherwise queue only the chosen topics.

---

## Step 5: Implement the selected topics

Fix the queued topics **one topic at a time**, smallest-risk first. After each topic, report the files changed.

Guidance per common topic:

- **Alt text** — add descriptive `alt` for informative images; `alt=""` for purely decorative ones; `aria-label` for icon-only buttons/links. Don't invent alt text you can't justify from context — flag those for the user.
- **Page language** — set `<html lang="…">` (and framework equivalents, e.g. Next `<Html lang>`, Nuxt `htmlAttrs`).
- **Form labels & errors** — connect every control to a `<label for>`/`id` (or wrap it); give errors text + `aria-describedby`; don't signal errors by color alone.
- **Color contrast** — adjust the offending color token/value to meet the threshold for the chosen level (4.5:1 / 3:1 at AA, 7:1 / 4.5:1 at AAA). Prefer editing shared tokens over per-component overrides. Show the before/after ratio.
- **Focus visibility** — remove blanket `outline: none`, or pair it with a visible `:focus-visible` style meeting 1.4.11.
- **Keyboard access** — convert click-only `<div>`/`<span>` handlers to real `<button>`/`<a>`, or add `role`, `tabindex="0"`, and key handlers; ensure modals trap and release focus correctly.
- **Landmarks & skip link** — use `<header>`, `<nav>`, `<main>`, `<footer>`; add a skip-to-content link targeting `<main>`.
- **Accessible names & roles** — give custom widgets correct `role` plus state attributes (`aria-expanded`, `aria-checked`, …) and accessible names.

**Do not** change a component's public API (prop/attribute names) or restructure markup in ways that alter behavior without flagging it to the user first.

---

## Step 6: Re-verify and summarize

After applying fixes:

1. Re-check each implemented topic against its criteria; confirm the violations are resolved.
2. If the repo has an a11y linter/test, run it again to confirm the count dropped.
3. Present a final summary:

```
## WCAG Fixes Applied — Target Level: AA

| Topic | Criteria | Files Changed | Status |
|-------|----------|---------------|--------|
| Alt text | 1.1.1 | src/Hero.tsx, src/Card.tsx | ✓ |
| Page language | 3.1.1 | index.html | ✓ |
| Color contrast | 1.4.3, 1.4.11 | styles/tokens.css | ✓ |

### Still needs manual / assistive-tech testing
- 1.4.10 Reflow — verify at 320px in a real browser
- 4.1.3 Status Messages — verify announcements with a screen reader

### Skipped (not selected)
- Focus visibility (2.4.7)
```

Remind the user that a static audit cannot fully guarantee conformance — real verification needs keyboard-only navigation, a screen reader, and browser zoom/reflow testing.
