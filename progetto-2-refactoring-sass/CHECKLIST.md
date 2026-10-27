# Checklist di migrazione da Sass a CSS standard

*[Read this in English](CHECKLIST-en.md)*

La checklist del Capitolo 12, pensata per essere copiata nel tuo progetto
e spuntata un passo alla volta. **Ogni passo produce un progetto funzionante,
compilabile e deployabile**: la build si spegne per ultima, quando non sta
più compilando niente.

## 0. Prima di cominciare

- [ ] **Fotografa l'output**: `sass scss/main.scss | prettier --parser=css > before.css`
- [ ] Crea una **pagina di collaudo** con ogni componente, in ogni variante e stato
- [ ] Verifica che i browser minimi supportino nesting e `@layer` (requisito, senza fallback)

## 1. Censimento

Conta, prima di toccare:

- [ ] Variabili: quante compaiono in **media query**? (non convertibili, vedi passo 4)
- [ ] Funzioni colore: `darken`, `lighten`, `mix`, `rgba`, `adjust-*`
- [ ] Mixin, divisi per sorte: **statici** / **parametrici** / **comportamentali**
- [ ] `@extend` e placeholder `%`
- [ ] Loop `@each`/`@for`: **quante delle classi generate sono davvero usate?**
      `grep -o 'class="[^"]*"' templates/ -r | tr ' ' '\n' | sort | uniq -c | sort -rn`
- [ ] Livelli di nesting BEM e occorrenze di `&__` / `&--`

## 2. Migrazione, nell'ordine

### Passo 1 — Variabili → custom properties
- [ ] `$var` → `var(--var)`, con `:root { --var: … }`
- [ ] ⚠️ Lascia in Sass le variabili usate nelle media query (passo 4)
- [ ] ⚠️ Lascia quelle dentro funzioni colore (passo 2)
- [ ] **Verifica: diff meccanico** — il diff deve essere leggibile e intenzionale

### Passo 2 — Funzioni colore → color-mix() e RCS
- [ ] `darken($c, N%)` → `color-mix(in oklch, var(--c), black N%)`
- [ ] `lighten` → verso `white` · `rgba($c, α)` → verso `transparent`
- [ ] `adjust-hue`, `scale-color` → `oklch(from var(--c) …)`
- [ ] **Verifica: occhio umano** — il diff NON sarà equivalente (OKLCH scurisce meglio)

### Passo 3 — Mixin ed @extend
- [ ] Mixin **statico** → classe di utilità (se è un ruolo del markup)
- [ ] Mixin **comportamentale** → regola condivisa nel layer base (se è un comportamento)
- [ ] Mixin **parametrico** → API di custom properties
- [ ] `@extend %placeholder` → composizione nel markup (`class="button icon-button"`)
- [ ] **Verifica: diff meccanico** — l'output dovrebbe DIMAGRIRE

### Passo 4 — Breakpoint
- [ ] Censisci: quante soglie sono decisioni di **pagina**, quante di **componente**?
- [ ] Pagina → `@media (width >= …)` con sintassi a intervalli
- [ ] Componente → `@container`, con soglia **misurata sul componente**
- [ ] Dichiara i contenitori nel layout: `> * { container-type: inline-size; }`
- [ ] Elimina la mappa `$breakpoints`

### Passo 5 — Nesting BEM → classi componibili (⚠️ tocca l'HTML)
- [ ] Un componente alla volta, **in ordine di frequenza di modifica**
- [ ] `&__elemento` → elemento o classe semplice, scopato dal nesting
- [ ] `&--variante` → `&.variante` + classe nel markup
- [ ] Ponte temporaneo: `&:is(.nuovo, .vecchio-bem)` finché i template migrano
- [ ] Rimuovi il ponte quando `grep "componente--" templates/` torna vuoto
- [ ] **Verifica: collaudo visivo**
- [ ] I componenti congelati possono restare in BEM: **non migrare per principio**

### Passo 6 — Moduli, layer e spegnimento della build
- [ ] Rinomina i file: via il prefisso `_` (non c'è più un compilatore da avvisare)
- [ ] `@use` → `@import url("…") layer(…)`
- [ ] Dichiara l'ordine dei layer in testa a `main.css`
- [ ] Assegna il CSS di terze parti a un layer `vendor` e decidi se vince o perde
- [ ] Aggiorna il percorso del CSS nell'HTML
- [ ] Rimuovi `sass` dal `package.json`, lo script di build, `dist/`, il watcher
- [ ] **Prova davvero che il progetto viva senza build**

## 3. Trappole note

- ⚠️ **Stili fuori dai layer vincono su tutto.** Se "i layer non funzionano",
  quasi sempre è rimasto uno stile non-layered in un file dimenticato.
- ⚠️ **`&--suffisso` non esiste** nel nesting nativo: non è traducibile, va ripensato.
- ⚠️ **Le custom properties non funzionano nelle media query**: ripensa la regola
  come container query, o lascia il valore letterale.
- ⚠️ **La specificità del nesting segue `:is()`**: il genitore pesa quanto il suo
  selettore più specifico. Non mescolare id e classi nelle liste.
