# CSS Semantico — codice degli esempi

*[Read this in English](README-en.md)*

Codice sorgente dei tre progetti guidati del libro
**[CSS Semantico — Scrivere CSS che comunica, scala e dura nel tempo](https://www.csssemantico.it)**
di Roberto Beccari.

Tutto il CSS di questo repository è **standard**: nessuna build, nessuna dipendenza,
nessun preprocessore. Apri gli `index.html` direttamente nel browser — è il punto.

---

## I progetti

### [Progetto 1 — Design system minimale](progetto-1-design-system/) · Capitolo 11

Il vocabolario visivo di un prodotto in sei file e cinque layer: token, tema
chiaro/scuro e cinque componenti costruiti con classi componibili.

Apri `index.html`: è la **pagina di collaudo**, il laboratorio del design system.
Da lì puoi eseguire i quattro test del capitolo:

| Test | Come | Cosa deve succedere |
|---|---|---|
| **Brand** | i quattro bottoni colorati in cima | tutto si riallinea: bottoni, badge, focus ring, grigi caldi |
| **Tema** | il bottone "Cambia tema" | nessun flash, nessun componente dimenticato |
| **Spazio** | confronta la card in griglia e in sidebar | si adatta da sola, senza classi aggiuntive |
| **Movimento** | attiva "riduci movimento" nel sistema operativo | spinner e transizioni si placano ovunque |

### [Progetto 2 — Refactoring di un progetto Sass](progetto-2-refactoring-sass/) · Capitolo 12

Lo stesso componente, prima e dopo:

- **`prima/`** — SCSS realistico: variabili, mixin (nelle tre varianti), `@extend`,
  `respond-to()`, nesting BEM, il loop che genera 32 utility.
- **`dopo/`** — CSS standard: custom properties, API di token, container queries,
  classi componibili, layer.
- **[`CHECKLIST.md`](progetto-2-refactoring-sass/CHECKLIST.md)** — la checklist di
  migrazione da copiare nel tuo progetto e spuntare un passo alla volta
  ([English version](progetto-2-refactoring-sass/CHECKLIST-en.md)).

Confronta i file omologhi affiancati: `prima/scss/components/_product-card.scss`
contro `dopo/styles/components/product-card.css` è il refactoring del paragrafo 2.8.

Per compilare la versione `prima` e vedere l'output che la migrazione deve preservare:

```bash
npx sass progetto-2-refactoring-sass/prima/scss/main.scss before.css
```

### [Progetto 3 — Landing senza media query](progetto-3-landing/) · Capitolo 13

Una pagina completa — header, hero, feature, pricing, footer — responsive dal
telefono all'ultrawide con **zero media query dimensionali**. Verificalo:

```bash
grep -r "@media" progetto-3-landing/styles/
```

Le uniche `@media` dell'intero progetto sono quelle **d'ambiente**
(`prefers-reduced-motion`), che vivono nel reset del Progetto 1: interrogano
l'utente, non lo schermo.

Prove da fare sulla pagina aperta:

- **Split-screen**: affiancala a un'altra finestra a larghezze che nessun
  breakpoint ha mai previsto — 900px, 640px, 517px.
- **Zoom al 200%**: requisito WCAG, non un test facoltativo. La pagina rifluisce.
- **Contenuto**: allunga una tagline nel pricing, aggiungi una settima feature card.
- **Trapianto**: sposta la sezione pricing dentro una colonna con sidebar.

---

## Supporto browser

Il codice usa funzionalità **Baseline** al momento della scrittura: nesting nativo,
custom properties, container queries, `:has()`, `@layer`, `oklch()`, `color-mix()`,
relative color syntax, `light-dark()`, subgrid.

Servono versioni recenti dei browser principali. Nesting e `@layer` sono **requisiti
minimi senza fallback**: sui browser che non li supportano le regole vengono ignorate
in blocco. Le scroll-driven animations del Progetto 3 sono l'unico *enhancement*,
racchiuso in `@supports` con base visibile.

Verifica sempre lo stato attuale su [caniuse.com](https://caniuse.com) e
[web.dev/baseline](https://web.dev/baseline): il supporto migliora solo.

## Struttura

```
progetto-1-design-system/
├── index.html              ← pagina di collaudo
└── styles/
    ├── main.css            ← ordine dei layer + import: l'indice del progetto
    ├── reset.css  base.css  layout.css  utilities.css
    └── components/         ← button, field, card, badge, alert

progetto-2-refactoring-sass/
├── CHECKLIST.md
├── prima/scss/             ← il "paziente": SCSS realistico
└── dopo/styles/            ← CSS standard

progetto-3-landing/
├── index.html
└── styles/                 ← costruita SOPRA il design system del Progetto 1
```

Il Progetto 3 non è autonomo: il suo `main.css` importa `reset.css`, `base.css`,
`layout.css` e `components/button.css` dal Progetto 1 con percorsi `../../`. È
voluto — è la dimostrazione che il design system si riusa — ma significa che la
cartella `progetto-3-landing/` da sola non funziona: serve l'intero repository.

## Licenza

Codice rilasciato con licenza MIT: usalo nei tuoi progetti, anche commerciali.
Il testo del libro è invece protetto da copyright.

## Errori e contributi

Hai trovato un errore o hai un suggerimento? Apri una issue.
Le correzioni confluiranno nella prossima edizione del libro.

Il libro: **[csssemantico.it](https://www.csssemantico.it)**
