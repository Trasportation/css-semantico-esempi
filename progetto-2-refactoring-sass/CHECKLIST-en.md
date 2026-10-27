# Sass to standard CSS migration checklist

The checklist from Chapter 12, designed to be copied into your own project and
ticked off one step at a time. **Every step leaves you with a working, compilable,
deployable project**: the build is switched off last, when it has nothing left to compile.

## 0. Before you start

- [ ] **Snapshot the output**: `sass scss/main.scss | prettier --parser=css > before.css`
- [ ] Build a **test page** showing every component, in every variant and state
- [ ] Confirm your minimum browsers support nesting and `@layer` (hard requirements, no fallback)

## 1. Take inventory

Count before you touch anything:

- [ ] Variables: how many appear inside **media queries**? (not convertible — see step 4)
- [ ] Colour functions: `darken`, `lighten`, `mix`, `rgba`, `adjust-*`
- [ ] Mixins, sorted by fate: **static** / **parametric** / **behavioural**
- [ ] `@extend` and `%placeholder` selectors
- [ ] `@each`/`@for` loops: **how many of the generated classes are actually used?**
      `grep -o 'class="[^"]*"' templates/ -r | tr ' ' '\n' | sort | uniq -c | sort -rn`
- [ ] Depth of BEM nesting, and occurrences of `&__` / `&--`

## 2. Migrate, in this order

### Step 1 — Variables → custom properties
- [ ] `$var` → `var(--var)`, declared in `:root { --var: … }`
- [ ] ⚠️ Leave variables used in media queries in Sass for now (step 4)
- [ ] ⚠️ Leave variables inside colour functions for now (step 2)
- [ ] **Verify: mechanical diff** — the diff should be readable and intentional

### Step 2 — Colour functions → color-mix() and relative colour syntax
- [ ] `darken($c, N%)` → `color-mix(in oklch, var(--c), black N%)`
- [ ] `lighten` → toward `white` · `rgba($c, α)` → toward `transparent`
- [ ] `adjust-hue`, `scale-color` → `oklch(from var(--c) …)`
- [ ] **Verify: human eyes** — the diff will NOT be equivalent (OKLCH darkens better)

### Step 3 — Mixins and @extend
- [ ] **Static** mixin → utility class (when it's a role the markup declares)
- [ ] **Behavioural** mixin → shared rule in the base layer (when it's a behaviour)
- [ ] **Parametric** mixin → custom property API
- [ ] `@extend %placeholder` → composition in the markup (`class="button icon-button"`)
- [ ] **Verify: mechanical diff** — the output should get *smaller*

### Step 4 — Breakpoints
- [ ] Take inventory: how many thresholds are **page** decisions, how many are **component** ones?
- [ ] Page → `@media (width >= …)` using range syntax
- [ ] Component → `@container`, with a threshold **measured on the component itself**
- [ ] Declare containers in the layout: `> * { container-type: inline-size; }`
- [ ] Delete the `$breakpoints` map

### Step 5 — BEM nesting → composable classes (⚠️ this touches the HTML)
- [ ] One component at a time, **in order of how often you edit it**
- [ ] `&__element` → plain element or simple class, scoped by nesting
- [ ] `&--variant` → `&.variant` plus a class in the markup
- [ ] Temporary bridge: `&:is(.new, .old-bem)` while templates migrate
- [ ] Remove the bridge once `grep "component--" templates/` comes back empty
- [ ] **Verify: visual test page**
- [ ] Frozen components may stay on BEM: **don't migrate on principle**

### Step 6 — Modules, layers, and switching off the build
- [ ] Rename the files: drop the `_` prefix (there's no compiler left to tell)
- [ ] `@use` → `@import url("…") layer(…)`
- [ ] Declare the layer order at the top of `main.css`
- [ ] Assign third-party CSS to a `vendor` layer and decide whether it wins or loses
- [ ] Update the stylesheet path in your HTML
- [ ] Remove `sass` from `package.json`, the build script, `dist/`, the watcher
- [ ] **Actually prove the project runs with no build**

## 3. Known traps

- ⚠️ **Styles outside any layer beat everything.** If "layers aren't working",
  it's almost always a stray non-layered style in a forgotten file.
- ⚠️ **`&--suffix` doesn't exist** in native nesting: it can't be translated, only rethought.
- ⚠️ **Custom properties don't work in media query conditions**: rethink the rule as a
  container query, or keep the literal value.
- ⚠️ **Nesting specificity follows `:is()`**: the parent weighs as much as its most
  specific selector. Don't mix ids and classes in the same selector list.
