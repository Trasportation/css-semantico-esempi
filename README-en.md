# CSS Semantico — example source code

Source code for the three guided projects in the book
**[CSS Semantico — Scrivere CSS che comunica, scala e dura nel tempo](https://www.csssemantico.it)**
by Roberto Beccari.

> **Note:** the book is currently available in Italian only. Code comments and
> folder names are in Italian, but the CSS itself is universal — and this README
> should be enough to find your way around. An English edition is planned.

Every line of CSS in this repository is **standard**: no build step, no dependencies,
no preprocessor. Open the `index.html` files directly in your browser — that's the point.

---

## The projects

### [Project 1 — Minimal design system](progetto-1-design-system/) · Chapter 11

A product's visual vocabulary in six files and five cascade layers: tokens,
light/dark theming, and five components built with composable classes.

Open `index.html`: it's the **test page**, the design system's laboratory.
From there you can run the chapter's four tests:

| Test | How | What should happen |
|---|---|---|
| **Brand** | the four colour buttons at the top | everything realigns: buttons, badges, focus rings, warm greys |
| **Theme** | the "Cambia tema" (change theme) button | no flash, no forgotten component |
| **Space** | compare the card in the grid vs in the sidebar | it adapts on its own, with no extra classes |
| **Motion** | turn on "reduce motion" in your OS | spinners and transitions settle everywhere |

Folder glossary: `stili`/`styles`, `componenti`/`components`, `base` = tokens and
element defaults, `layout` = containers and grids, `utilities` = single-purpose classes.

### [Project 2 — Refactoring a Sass project](progetto-2-refactoring-sass/) · Chapter 12

The same component, before and after:

- **`prima/`** ("before") — realistic SCSS: variables, mixins (in all three flavours),
  `@extend`, a `respond-to()` breakpoint mixin, BEM nesting, and the loop that
  generates 32 utility classes.
- **`dopo/`** ("after") — standard CSS: custom properties, token APIs, container
  queries, composable classes, cascade layers.
- **[`CHECKLIST-en.md`](progetto-2-refactoring-sass/CHECKLIST-en.md)** — the migration
  checklist, ready to copy into your own project and tick off one step at a time.

Compare the matching files side by side: `prima/scss/components/_product-card.scss`
against `dopo/styles/components/product-card.css` is the refactoring walked through
in section 2.8 of the book.

To compile the "before" version and see the output your migration must preserve:

```bash
npx sass progetto-2-refactoring-sass/prima/scss/main.scss before.css
```

### [Project 3 — Landing page with no media queries](progetto-3-landing/) · Chapter 13

A complete page — header, hero, features, pricing, footer — responsive from phone
to ultrawide with **zero dimensional media queries**. Check for yourself:

```bash
grep -r "@media" progetto-3-landing/styles/
```

The only `@media` rules in the whole project are **environment** queries
(`prefers-reduced-motion`), which live in Project 1's reset: they ask about the
*user*, not the screen.

Things to try on the live page:

- **Split screen**: put it next to another window at widths no breakpoint ever
  anticipated — 900px, 640px, 517px.
- **200% zoom**: a WCAG requirement, not an optional test. The page reflows.
- **Content**: lengthen a pricing tagline, add a seventh feature card.
- **Transplant**: move the pricing section inside a column with a sidebar.

---

## Browser support

The code uses features that were **Baseline** at the time of writing: native nesting,
custom properties, container queries, `:has()`, `@layer`, `oklch()`, `color-mix()`,
relative colour syntax, `light-dark()`, and subgrid.

Recent versions of the major browsers are required. Nesting and `@layer` are
**hard minimum requirements with no fallback**: browsers that don't support them
ignore the affected rules wholesale. The scroll-driven animations in Project 3 are
the only *enhancement*, wrapped in `@supports` with a visible base state.

Always check the current status on [caniuse.com](https://caniuse.com) and
[web.dev/baseline](https://web.dev/baseline) — support only ever improves.

## Structure

```
progetto-1-design-system/       ← design system
├── index.html                  ← test page
└── styles/
    ├── main.css                ← layer order + imports: the project index
    ├── reset.css  base.css  layout.css  utilities.css
    └── components/             ← button, field, card, badge, alert

progetto-2-refactoring-sass/    ← Sass migration
├── CHECKLIST.md  CHECKLIST-en.md
├── prima/scss/                 ← "before": realistic SCSS
└── dopo/styles/                ← "after": standard CSS

progetto-3-landing/             ← landing page
├── index.html
└── styles/                     ← built ON TOP of Project 1's design system
```

Project 3 is not self-contained: its `main.css` imports `reset.css`, `base.css`,
`layout.css` and `components/button.css` from Project 1 through `../../` paths.
That is deliberate — it shows the design system being reused — but it means the
`progetto-3-landing/` folder on its own will not work: you need the whole
repository.

## Licence

Code released under the MIT licence: use it in your own projects, commercial ones
included. The text of the book itself remains under copyright.

## Errors and contributions

Found a mistake, or have a suggestion? Open an issue.
Corrections will feed into the next edition of the book.

The book: **[csssemantico.it](https://www.csssemantico.it)**
