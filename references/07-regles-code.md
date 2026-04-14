# Design System — 07 Règles de Code

> **Prérequis** : avoir lu [00-fondations.md](./00-fondations.md), [01-design-tokens.md](./01-design-tokens.md) et [02-layout.md](./02-layout.md).

Ce document définit les conventions de code pour HTML, CSS et JavaScript. Toute personne (humaine ou IA) qui produit du code pour une application couverte par ce design system doit suivre ces règles. L'objectif est d'éliminer l'ambiguïté stylistique et de garantir un code cohérent, maintenable et conforme au principe Vanilla Web.

---

## 1. Principes généraux

1. **Lisibilité d'abord** : le code est lu bien plus souvent qu'il n'est écrit. Privilégier la clarté à la concision.
2. **Explicite plutôt qu'implicite** : nommer les choses par leur rôle, pas par leur apparence ou leur position.
3. **Vanilla Web** : pas de framework, pré-processeur ou transpileur en production. Les fichiers livrés sont directement exécutables par le navigateur.
4. **Tokens obligatoires** : toute valeur de couleur, espacement, taille de texte, rayon, ombre, z-index ou durée d'animation doit référencer un token CSS. Aucune valeur brute (hex, px, ms) dans le CSS applicatif — sauf les exceptions listées en section 3.7.

---

## 2. HTML

### 2.1 Structure de base

Tout document HTML commence par cette structure :

```html
<!DOCTYPE html>
<html lang="fr" data-theme="light">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Nom de l'application — Page</title>

  <!-- Tokens et styles de base -->
  <link rel="stylesheet" href="css/tokens.css">
  <link rel="stylesheet" href="css/reset.css">
  <link rel="stylesheet" href="css/base.css">
  <link rel="stylesheet" href="css/layout.css">
  <link rel="stylesheet" href="css/utilities.css">

  <!-- Styles de composants -->
  <link rel="stylesheet" href="css/components/button.css">
  <link rel="stylesheet" href="css/components/input.css">
  <!-- ... -->

  <!-- Styles spécifiques à la page (si nécessaire) -->
  <link rel="stylesheet" href="css/pages/dashboard.css">
</head>
<body>
  <!-- App Shell -->
  <div class="app-shell">
    <header class="app-shell__topbar">...</header>
    <nav class="app-shell__sidenav" aria-label="Navigation principale">...</nav>
    <main class="app-shell__main">...</main>
    <footer class="app-shell__status" aria-label="Barre de statut">...</footer>
  </div>

  <!-- Scripts -->
  <script type="module" src="js/app.js"></script>
</body>
</html>
```

### 2.2 Ordre de chargement CSS

L'ordre des fichiers CSS est **strict** et ne doit pas être modifié :

1. `tokens.css` — Variables CSS (custom properties sur `:root`)
2. `reset.css` — Remise à zéro des styles navigateur
3. `base.css` — Styles élémentaires (`body`, `h1-h6`, `a`, `p`, `code`, etc.)
4. `layout.css` — App Shell, grille, conteneurs
5. `utilities.css` — Classes utilitaires (flex, gap, overflow, etc.)
6. `components/*.css` — Un fichier par composant
7. `pages/*.css` — Styles spécifiques à une page (optionnel, à éviter si possible)

### 2.3 HTML sémantique

Les éléments HTML doivent être choisis pour leur **sémantique**, pas pour leur rendu par défaut.

| Besoin                        | Élément HTML correct         | Interdit                        |
| ----------------------------- | ---------------------------- | ------------------------------- |
| Navigation principale         | `<nav aria-label="...">`     | `<div class="nav">`            |
| En-tête de page               | `<header>`                   | `<div class="header">`         |
| Contenu principal             | `<main>`                     | `<div class="main">`           |
| Section thématique            | `<section aria-labelledby>`  | `<div class="section">`        |
| Titre                         | `<h1>` à `<h6>`             | `<div class="title">`          |
| Liste d'éléments              | `<ul>` / `<ol>` + `<li>`    | `<div>` + `<div>`              |
| Tableau de données            | `<table>` + `<thead/tbody>`  | Grille de `<div>`              |
| Action qui navigue            | `<a href="...">`             | `<button onclick="navigate">` |
| Action qui agit               | `<button type="button">`     | `<a href="#">`                 |
| Champ de formulaire           | `<input>` + `<label>`        | `<div contenteditable>`        |
| Regroupement de champs        | `<fieldset>` + `<legend>`    | `<div class="group">`          |
| Texte important               | `<strong>`                   | `<span class="bold">`          |
| Citation                      | `<blockquote>` / `<q>`      | `<div class="quote">`          |
| Information complémentaire    | `<aside>`                    | `<div class="sidebar">`        |
| Contenu temporel              | `<time datetime="...">`      | Texte brut                     |
| Image informative             | `<img alt="description">`    | `<div style="background:...">`|
| Image décorative              | `<img alt="" role="none">`   | `alt` non vide                 |

### 2.4 Attributs `data-*`

Les états visuels dynamiques sont pilotés par des attributs `data-*` ou des attributs ARIA, **pas** par l'ajout/suppression de classes de style :

```html
<!-- Correct : état piloté par attribut -->
<button data-variant="primary" data-size="sm">Enregistrer</button>
<nav data-open>...</nav>
<details open>...</details>

<!-- Incorrect : état dans le nom de classe -->
<button class="btn btn-primary btn-sm is-active">Enregistrer</button>
```

**Exception** : les classes utilitaires de layout (`flex`, `gap-md`, `col-6`, etc.) restent des classes CSS car elles décrivent la mise en page, pas un état.

### 2.5 Hiérarchie des titres

Chaque page a un **et un seul** `<h1>`. Les niveaux suivants (`<h2>`, `<h3>`, etc.) ne sautent pas de niveau :

```html
<!-- Correct -->
<h1>Tableau de bord</h1>
  <h2>Activité récente</h2>
  <h2>Statistiques</h2>
    <h3>Ce mois</h3>

<!-- Incorrect : saut de h1 à h3 -->
<h1>Tableau de bord</h1>
  <h3>Activité récente</h3>
```

### 2.6 Accessibilité HTML

- Tout élément interactif (`<button>`, `<a>`, `<input>`) doit être **focusable au clavier** et avoir un **label accessible** (contenu textuel, `aria-label`, ou `aria-labelledby`).
- Les icônes sans texte adjacent doivent avoir un `aria-label` sur l'élément interactif parent.
- Les images informatives ont un `alt` descriptif. Les images décoratives ont `alt=""`.
- Les rôles ARIA ne sont utilisés que quand l'élément HTML natif ne suffit pas. Ne pas écrire `<button role="button">` (redondant).
- Les régions de page (`<nav>`, `<main>`, `<aside>`) ont un `aria-label` si elles apparaissent plusieurs fois.

---

## 3. CSS

### 3.1 Convention de nommage — BEM modifié

Le design system utilise une variante de **BEM** (Block Element Modifier) avec la syntaxe suivante :

```
.block                    → Composant racine
.block__element            → Sous-élément du composant
.block--modifier           → Variante du composant (usage limité, voir 3.2)
.block__element--modifier  → Variante d'un sous-élément (usage limité)
```

Exemples :

```css
.card { }
.card__header { }
.card__body { }
.card__footer { }
.card__title { }
```

**Règles de nommage :**

- Noms en **anglais**, en **kebab-case** (minuscules, mots séparés par des tirets).
- Maximum **2 niveaux** de profondeur BEM. Pas de `.card__header__title__icon` — aplatir en `.card__header-icon` ou créer un sous-composant.
- Le nom du bloc reflète le **rôle** du composant, pas son apparence : `.alert` et non `.red-box`, `.data-table` et non `.striped-grid`.

### 3.2 États et variantes — Attributs plutôt que classes

Les variantes de composants et les états dynamiques sont exprimés par des **sélecteurs d'attribut** plutôt que par des classes BEM modifier :

```css
/* Variantes via data-variant */
.btn { /* styles de base */ }
.btn[data-variant="primary"]   { background: var(--color-primary); }
.btn[data-variant="danger"]    { background: var(--color-danger); }
.btn[data-variant="ghost"]     { background: transparent; }

/* Tailles via data-size */
.btn[data-size="sm"]  { padding: var(--padding-xs) var(--padding-sm); }
.btn[data-size="lg"]  { padding: var(--padding-md) var(--padding-lg); }

/* États via attributs natifs ou ARIA */
.btn:hover    { }
.btn:focus-visible { }
.btn:active   { }
.btn:disabled { }
.btn[aria-pressed="true"] { }
.btn[aria-expanded="true"] { }
```

**Pourquoi les attributs plutôt que les classes modifier ?**

- Ils séparent clairement la **structure** (classe BEM) de la **configuration** (attributs data).
- Ils sont directement lisibles dans le HTML : `<button data-variant="primary" data-size="sm">` est plus explicite que `<button class="btn btn--primary btn--sm">`.
- Les attributs ARIA servent à la fois l'accessibilité et le style — pas de duplication entre `aria-expanded="true"` et `.is-expanded`.
- Pour une IA, la règle est non ambiguë : la classe indique **ce que c'est**, les attributs indiquent **comment c'est configuré**.

### 3.3 Classes utilitaires

Les classes utilitaires sont réservées au **layout** et à l'**espacement**. Elles sont définies dans `utilities.css` et documentées dans [02-layout.md](./02-layout.md). Elles ne concernent **jamais** les couleurs, la typographie ou les bordures — cela relève des tokens et des composants.

Classes utilitaires autorisées :

- Layout : `flex`, `flex-col`, `grid`, `grid-cols-*`, `col-*`
- Alignement : `items-center`, `justify-between`, etc.
- Gap : `gap-xs`, `gap-sm`, `gap-md`, `gap-lg`, `gap-xl`
- Overflow : `overflow-auto`, `overflow-hidden`, etc.
- Position : `relative`, `absolute`, `sticky`
- Taille : `w-full`, `h-full`, `min-h-0`, `min-w-0`
- Visibilité : `sr-only` (screen reader only)

Classes utilitaires **interdites** (utiliser des tokens dans le composant à la place) :

- Couleurs : pas de `.bg-primary`, `.text-red`
- Typographie : pas de `.text-sm`, `.font-bold`
- Marges/paddings spécifiques : pas de `.mt-4`, `.px-2`
- Bordures : pas de `.border`, `.rounded-md`

### 3.4 Classe `sr-only`

Pour le contenu visible uniquement par les lecteurs d'écran :

```css
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border-width: 0;
}
```

### 3.5 Style de focus

Tous les éléments interactifs doivent avoir un focus visible. Le style de focus est défini globalement :

```css
:focus-visible {
  outline: 2px solid var(--color-focus-ring);
  outline-offset: 2px;
}

/* Supprimer l'outline par défaut au clic (souris) mais garder pour le clavier */
:focus:not(:focus-visible) {
  outline: none;
}
```

**Règle** : ne **jamais** écrire `outline: none` ou `outline: 0` sans fournir un style de focus alternatif via `:focus-visible`.

### 3.6 Reset CSS

Le reset CSS est minimal et opinionné pour les besoins du design system :

```css
/* reset.css */

*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

html {
  font-size: 16px;
  -webkit-text-size-adjust: 100%;
  text-size-adjust: 100%;
}

body {
  font-family: var(--font-sans);
  font-size: var(--text-base);
  line-height: var(--leading-normal);
  color: var(--color-text-default);
  background-color: var(--color-bg-page);
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

img,
picture,
video,
canvas,
svg {
  display: block;
  max-width: 100%;
}

input,
button,
textarea,
select {
  font: inherit;
  color: inherit;
}

button {
  cursor: pointer;
  background: none;
  border: none;
}

a {
  color: inherit;
  text-decoration: none;
}

table {
  border-collapse: collapse;
  border-spacing: 0;
}

h1, h2, h3, h4, h5, h6 {
  font-size: inherit;
  font-weight: inherit;
}

ul, ol {
  list-style: none;
}
```

### 3.7 Valeurs brutes autorisées

Par exception, certaines valeurs brutes sont acceptées dans le CSS applicatif :

| Valeur brute autorisée       | Contexte                                             |
| ----------------------------- | ---------------------------------------------------- |
| `0`                           | Partout (pas besoin de token pour zéro)              |
| `1px`                         | Bordures (via token `--border-width-default`)        |
| `100%`                        | Largeur/hauteur relative au parent                   |
| `100dvh`, `100vw`             | Viewport dimensions                                  |
| `auto`                        | Propriétés qui l'acceptent                           |
| `none`, `transparent`         | Reset de propriétés                                  |
| `inherit`, `currentColor`     | Héritage explicite                                   |
| `1fr`, `minmax()`, `span N`   | Propriétés de grille CSS                             |
| `calc()` combinant des tokens | Ex: `calc(100% - var(--shell-nav-width))`            |

Tout le reste (couleurs hex/rgb, px pour espacement, ms pour durées, rem pour tailles) doit passer par un token.

### 3.8 Organisation d'un fichier CSS de composant

Chaque composant a son propre fichier CSS. Le fichier suit cette structure :

```css
/* ==========================================================================
   Composant : Button (.btn)
   Dépendances tokens : --color-*, --padding-*, --radius-*, --duration-*
   ========================================================================== */

/* --- Base --- */
.btn {
  /* Layout */
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: var(--gap-xs);

  /* Dimensions */
  padding: var(--padding-sm) var(--padding-md);
  min-height: 2.25rem;

  /* Typographie */
  font-family: var(--font-sans);
  font-size: var(--text-base);
  font-weight: var(--font-weight-medium);
  line-height: var(--leading-tight);

  /* Visuel */
  background-color: var(--color-bg-default);
  color: var(--color-text-default);
  border: var(--border-width-default) solid var(--color-border-default);
  border-radius: var(--radius-sm);

  /* Comportement */
  cursor: pointer;
  transition: background-color var(--duration-fast) var(--ease-default),
              border-color var(--duration-fast) var(--ease-default),
              color var(--duration-fast) var(--ease-default);
}

/* --- États interactifs --- */
.btn:hover { }
.btn:focus-visible { }
.btn:active { }
.btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
  pointer-events: none;
}

/* --- Variantes --- */
.btn[data-variant="primary"] { }
.btn[data-variant="danger"] { }
.btn[data-variant="ghost"] { }

/* --- Tailles --- */
.btn[data-size="sm"] { }
.btn[data-size="lg"] { }

/* --- Sous-éléments --- */
.btn__icon { }
.btn__label { }
```

**Ordre des propriétés dans un bloc CSS** (cohérent avec la section 7.4 de 02-layout.md) :

1. Layout (`display`, `flex-*`, `grid-*`, `gap`, `align-*`, `justify-*`)
2. Positionnement (`position`, `top/right/bottom/left`, `z-index`)
3. Dimensions (`width`, `height`, `min-*`, `max-*`, `padding`, `margin`)
4. Typographie (`font-*`, `line-height`, `letter-spacing`, `text-*`, `color`)
5. Visuel (`background`, `border`, `border-radius`, `box-shadow`, `opacity`)
6. Comportement (`cursor`, `pointer-events`, `transition`, `animation`, `overflow`)

### 3.9 CSS Containment

#### Principe

La propriété `contain` permet d'isoler un composant du reste de l'arbre de rendu. Le navigateur peut ainsi optimiser le recalcul de style, le layout et le paint en limitant leur portée au composant concerné.

**Convention** : tout composant autonome (carte, widget, ligne de tableau, item de liste) doit déclarer `contain: content` sur son élément racine.

```css
.card {
  contain: content;
  /* ... */
}

.data-table__row {
  contain: content;
  /* ... */
}
```

`contain: content` est l'équivalent de `contain: layout paint style`. Il isole le composant sans affecter son dimensionnement dans le flux.

#### Exceptions — ne pas appliquer `contain: content`

Trois situations interdisent l'utilisation de `contain: content` :

| Situation | Raison | Composants concernés |
| --- | --- | --- |
| Le composant a des enfants en `position: fixed` | `contain: content` capture les descendants `fixed` — ils deviennent relatifs au composant, pas au viewport | Dropdown, Tooltip, Popover |
| Le composant a un overflow visible intentionnel (ex: tooltip ou badge qui déborde) | `contain: content` implique `overflow: hidden` — le débordement est silencieusement coupé | Tooltip, composants avec badge en surimpression |
| Le composant participe à un stacking context partagé complexe | `contain: content` crée un nouveau stacking context — cela multiplie les contextes et complique le débogage `z-index` | Zones avec overlay imbriqués |

Pour ces cas, utiliser `contain: layout style` (sans `paint`) ou ne pas appliquer `contain` du tout. Documenter l'exception dans le fichier CSS du composant.

```css
/* Exception documentée : contain: content retiré car le dropdown enfant
   utilise position: fixed pour se positionner par rapport au viewport */
.select-wrapper {
  /* contain: content; ← retiré intentionnellement */
  position: relative;
}
```

#### `content-visibility: auto` pour les listes longues

Pour les composants qui contiennent de longues listes (> 200 items) et qui n'utilisent pas de virtualisation JS, `content-visibility: auto` permet au navigateur de ne pas rendre les éléments hors viewport. À utiliser en complément de `contain-intrinsic-size` pour éviter le saut de scrollbar :

```css
.list__item {
  content-visibility: auto;
  contain-intrinsic-size: 0 3rem; /* hauteur estimée de l'item */
}
```

**Quand utiliser quelle approche** :

| Situation | Approche recommandée |
| --- | --- |
| Liste ≤ 200 items | `contain: content` sur chaque item, rien d'autre |
| Liste > 200 items, scrolling natif suffisant | `content-visibility: auto` + `contain-intrinsic-size` |
| Liste > 200 items, interactions complexes (tri, sélection multiple) | Pagination ou scroll virtuel JS (voir `08-checklist.md` §9) |

#### Gotchas à connaître

Ces comportements contre-intuitifs s'appliquent dès qu'un ancêtre porte `contain: content` ou `contain: layout` :

1. **Capture de `position: fixed`** : un enfant `position: fixed` est positionné par rapport au composant contenant, pas par rapport au viewport. Symptôme : un dropdown ou une modale "reste collé" à son parent au lieu de s'afficher en surimpression globale.

2. **Clipping silencieux** : `contain: content` implique `overflow: hidden`. Tout débordement (badge, tooltip, ombre portée très étendue) est coupé sans avertissement.

3. **Multiplication des stacking contexts** : chaque composant avec `contain: content` crée son propre stacking context. Si plusieurs composants coexistent sur la même zone (ex: ligne de tableau avec actions et tooltip), l'ordre des `z-index` n'est résolu qu'à l'intérieur de chaque contexte. Symptôme : un `z-index: var(--z-tooltip)` ne passe pas au-dessus d'un composant frère malgré une valeur plus haute.

### 3.10 Détection de capacité CSS — `@supports`

`@supports` permet de conditionner des déclarations CSS à la disponibilité d'une propriété ou d'une valeur. C'est le mécanisme standard pour écrire des fallbacks en CSS natif.

#### Quand utiliser `@supports`

- Quand une fonctionnalité CSS est en statut **Limited Availability** (pas encore dans Baseline Newly Available) et qu'un fallback acceptable existe.
- Quand une amélioration progressive est possible : les navigateurs qui supportent la feature ont une meilleure expérience, les autres une expérience fonctionnelle mais plus basique.

```css
/* Amélioration progressive : scroll fluide si supporté */
@supports (scroll-behavior: smooth) {
  html {
    scroll-behavior: smooth;
  }
}

/* Fallback pour une propriété récente */
.container {
  /* Fallback pour les navigateurs sans support subgrid */
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}

@supports (grid-template-rows: subgrid) {
  .container {
    grid-template-rows: subgrid;
  }
}
```

#### Règles

- Ne **pas** utiliser `@supports` pour des fonctionnalités déjà en Baseline Newly Available — elles sont garanties par les navigateurs cibles.
- Toujours documenter dans un commentaire pourquoi le `@supports` est nécessaire : statut Baseline de la feature, date prévisible de promotion.
- Un `@supports` sans fallback utile (où l'absence de support rend la page inutilisable) est un signe que la feature ne devrait pas être utilisée — vérifier le statut Baseline avec `webstatus.dev`.

```css
/* Exemple correctement documenté */
/* grid-template-rows: subgrid — Baseline Newly Available depuis 2023.
   Fallback : hauteurs fixes sur les éléments enfants. */
.card-grid__item {
  min-height: 12rem; /* fallback */
}

@supports (grid-template-rows: subgrid) {
  .card-grid {
    grid-template-rows: subgrid;
  }
  .card-grid__item {
    min-height: unset;
  }
}
```

#### Lien avec la checklist

La `08-checklist.md` exige : "aucune fonctionnalité Limited Availability sans fallback documenté". Le fallback doit être implémenté via `@supports` — pas via un polyfill JS ni via une condition commentée.

---

## 4. JavaScript

### 4.1 Modules ES

Tout le JavaScript est écrit en **modules ES** (`type="module"`). Pas de scripts globaux, pas d'IIFE, pas de namespaces manuels.

```html
<script type="module" src="js/app.js"></script>
```

```js
// js/components/dropdown.js
export function initDropdown(element) { /* ... */ }

// js/app.js
import { initDropdown } from './components/dropdown.js';
```

### 4.2 Convention de nommage

| Élément            | Convention       | Exemple                         |
| ------------------- | ---------------- | ------------------------------- |
| Variables           | camelCase        | `currentUser`, `isOpen`         |
| Constantes globales | UPPER_SNAKE_CASE | `MAX_RETRIES`, `API_BASE_URL`   |
| Fonctions           | camelCase        | `handleClick`, `fetchData`      |
| Classes             | PascalCase       | `DataTable`, `ModalDialog`      |
| Fichiers            | kebab-case       | `dropdown.js`, `data-table.js`  |
| Événements custom   | kebab-case       | `menu-toggle`, `row-select`     |

### 4.3 Interaction avec le DOM

**Sélection d'éléments** : utiliser `querySelector` / `querySelectorAll` avec des sélecteurs d'attribut `data-*` dédiés à la logique JS, préfixés par `data-js-` :

```html
<button data-variant="primary" data-js-action="save">Enregistrer</button>
```

```js
// Correct : sélection par attribut JS dédié
const saveBtn = document.querySelector('[data-js-action="save"]');

// Incorrect : sélection par classe CSS (couplage style/logique)
const saveBtn = document.querySelector('.btn--primary');

// Incorrect : sélection par ID sauf cas unique justifié
const saveBtn = document.getElementById('save-button');
```

**Pourquoi `data-js-*` ?**

- Découple le CSS du JavaScript : renommer une classe CSS ne casse pas le JS et inversement.
- Rend explicite dans le HTML ce qui est "câblé" en JS.
- Pour une IA, la règle est binaire : si c'est pour le JS, utiliser `data-js-*`.

### 4.4 Gestion des événements

- Utiliser `addEventListener`, jamais les attributs HTML inline (`onclick`, `onchange`).
- Pour les listes dynamiques, utiliser la **délégation d'événements** sur le conteneur parent.
- Les événements custom utilisent `CustomEvent` avec `detail` pour passer des données :

```js
// Émettre
element.dispatchEvent(new CustomEvent('row-select', {
  bubbles: true,
  detail: { rowId: '123' }
}));

// Écouter
container.addEventListener('row-select', (e) => {
  console.log(e.detail.rowId);
});
```

### 4.5 Manipulation des états visuels

Le JavaScript ne manipule **jamais** les propriétés `style` directement (sauf pour les valeurs calculées dynamiquement comme la position d'un popover). Les changements visuels passent par la modification d'attributs `data-*` ou ARIA :

```js
// Correct
element.setAttribute('data-open', '');
element.removeAttribute('data-open');
element.toggleAttribute('data-open');
element.setAttribute('aria-expanded', 'true');

// Incorrect
element.style.display = 'block';
element.classList.add('is-open');
element.classList.toggle('active');
```

**Exception** : les propriétés qui dépendent d'un calcul dynamique (position, dimensions calculées au runtime) peuvent être assignées via des custom properties inline :

```js
// Acceptable : custom property dynamique pour positionner un popover
popover.style.setProperty('--popover-x', `${rect.left}px`);
popover.style.setProperty('--popover-y', `${rect.bottom}px`);
```

### 4.6 Gestion des erreurs

Tout appel asynchrone (`fetch`, accès DOM risqué) doit être enveloppé dans un `try/catch` :

```js
async function loadData(url) {
  try {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}: ${response.statusText}`);
    }
    return await response.json();
  } catch (error) {
    console.error('[loadData]', error);
    // Propager ou afficher un feedback utilisateur
    throw error;
  }
}
```

### 4.7 Pas de `var`, préférence `const`

- Utiliser `const` par défaut.
- Utiliser `let` uniquement si la variable est réassignée.
- Ne jamais utiliser `var`.

### 4.8 Scheduling — `requestIdleCallback`

Pour les tâches non urgentes qui ne doivent pas bloquer l'interaction utilisateur (analytics, pré-chargement, initialisation différée, écriture en cache), utiliser `requestIdleCallback` plutôt qu'un `setTimeout` arbitraire.

```js
// Correct : tâche non urgente différée aux périodes d'inactivité
requestIdleCallback(() => {
  prefetchNextPage();
});

// Avec timeout de sécurité : la tâche s'exécute au plus tard après 2s
requestIdleCallback(() => {
  logAnalyticsEvent('page_view');
}, { timeout: 2000 });

// Incorrect pour les tâches non urgentes
setTimeout(() => {
  prefetchNextPage();
}, 100); // délai arbitraire, sans garantie sur l'état du thread
```

**Quand utiliser `requestIdleCallback`** :

| Tâche | Utiliser `requestIdleCallback` ? |
| --- | --- |
| Mise à jour d'un état visuel suite à une action utilisateur | Non — doit être immédiat |
| Appel API déclenché par l'utilisateur | Non — doit être immédiat |
| Pré-chargement de données pour la navigation suivante | Oui |
| Initialisation de composants hors-viewport au chargement | Oui |
| Envoi d'événements analytics | Oui |
| Écriture dans `localStorage` ou `sessionStorage` | Oui |

**Note** : `requestIdleCallback` est Baseline Widely Available. Il n'existe pas de fallback nécessaire pour les navigateurs cibles.

---

## 5. Structure de fichiers d'un projet

```
project/
├── index.html
├── css/
│   ├── tokens.css              ← Custom properties (:root)
│   ├── reset.css               ← Reset navigateur
│   ├── base.css                ← Styles élémentaires (body, titres, liens...)
│   ├── layout.css              ← App Shell, grille, conteneurs
│   ├── utilities.css           ← Classes utilitaires
│   ├── components/
│   │   ├── button.css
│   │   ├── input.css
│   │   ├── card.css
│   │   ├── data-table.css
│   │   ├── modal.css
│   │   ├── dropdown.css
│   │   └── ...
│   └── pages/                  ← Styles propres à une page (optionnel)
│       ├── dashboard.css
│       └── settings.css
├── js/
│   ├── app.js                  ← Point d'entrée, imports
│   ├── components/
│   │   ├── dropdown.js
│   │   ├── modal.js
│   │   ├── data-table.js
│   │   └── ...
│   ├── services/               ← Appels API, logique métier
│   │   ├── api.js
│   │   └── auth.js
│   └── utils/                  ← Fonctions utilitaires
│       ├── dom.js
│       ├── format.js
│       └── events.js
├── assets/
│   ├── icons/                  ← SVG sprites ou fichiers individuels
│   ├── images/
│   └── fonts/                  ← Polices auto-hébergées (optionnel)
└── package.json                ← Browserslist + scripts de lint
```

### 5.1 Conventions de nommage des fichiers

| Type           | Convention  | Exemple                              |
| -------------- | ----------- | ------------------------------------ |
| Fichier CSS    | kebab-case  | `data-table.css`, `modal.css`        |
| Fichier JS     | kebab-case  | `data-table.js`, `dropdown.js`       |
| Fichier HTML   | kebab-case  | `user-settings.html`                 |
| Dossier        | kebab-case  | `components/`, `services/`           |

### 5.2 Un composant = un fichier CSS + un fichier JS (si interactif)

Chaque composant du design system est défini par :

- Un fichier CSS dans `css/components/` (obligatoire).
- Un fichier JS dans `js/components/` (uniquement si le composant a un comportement interactif).
- Les deux fichiers portent le **même nom** : `dropdown.css` + `dropdown.js`.

---

## 6. `package.json` minimal

Le `package.json` sert exclusivement à l'outillage de développement (pas de dépendances de production) :

```json
{
  "name": "mon-app",
  "private": true,
  "browserslist": [
    "baseline newly available with downstream"
  ],
  "devDependencies": {
    "stylelint": "^16.x",
    "stylelint-browser-compat": "^2.x",
    "eslint": "^9.x",
    "eslint-plugin-compat": "^6.x",
    "@eslint/css": "^0.x"
  },
  "scripts": {
    "lint:css": "stylelint 'css/**/*.css'",
    "lint:js": "eslint 'js/**/*.js'",
    "lint": "npm run lint:css && npm run lint:js"
  }
}
```

**Rappel** : aucun de ces packages n'intervient dans le code livré. Ce ne sont que des garde-fous de développement.

---

## 7. Checklist rapide par fichier

Avant de valider un fichier, vérifier les points suivants :

### Fichier HTML

- [ ] `lang` défini sur `<html>`
- [ ] `data-theme` défini sur `<html>`
- [ ] Un seul `<h1>`, hiérarchie des titres respectée
- [ ] Tout `<img>` a un `alt` (vide si décoratif)
- [ ] Tout élément interactif est focusable au clavier
- [ ] Tout `<input>` est associé à un `<label>`
- [ ] Les attributs `data-*` sont utilisés pour les états dynamiques
- [ ] Les attributs `data-js-*` sont utilisés pour les hooks JavaScript
- [ ] Ordre des `<link>` CSS conforme à la section 2.2

### Fichier CSS

- [ ] Nommage BEM respecté (`.block__element`)
- [ ] Aucune valeur brute (hex, px, ms) sauf exceptions de la section 3.7
- [ ] Tous les tokens référencés existent dans `tokens.css`
- [ ] Variantes exprimées par sélecteurs d'attribut, pas par classes modifier
- [ ] Propriétés ordonnées selon la convention (section 3.8)
- [ ] `:focus-visible` défini pour tout élément interactif
- [ ] `prefers-reduced-motion` pris en compte si des animations existent
- [ ] Pas de `!important` sauf dans le reset `prefers-reduced-motion`
- [ ] `contain: content` déclaré sur l'élément racine de tout composant autonome
- [ ] Les exceptions à `contain: content` (enfants `fixed`, overflow visible, stacking context complexe) sont documentées en commentaire dans le fichier CSS du composant

### Fichier JS

- [ ] `type="module"` utilisé
- [ ] `const` par défaut, `let` si réassigné, jamais `var`
- [ ] Sélection DOM via `data-js-*`, pas via classes CSS
- [ ] Événements via `addEventListener`, pas d'attributs inline
- [ ] États visuels manipulés via attributs `data-*` / ARIA, pas via `.style` ni `.classList`
- [ ] Appels async dans `try/catch`
- [ ] Exports nommés, pas de `export default` sur les utilitaires

---

## 8. Anti-patterns à éviter

| Anti-pattern                                       | Faire à la place                                |
| -------------------------------------------------- | ----------------------------------------------- |
| `<div class="btn btn--primary btn--sm is-active">` | `<button class="btn" data-variant="primary" data-size="sm" aria-pressed="true">` |
| `element.style.display = 'none'`                   | `element.toggleAttribute('hidden')` ou `element.setAttribute('data-hidden', '')` |
| `element.classList.add('is-open')`                 | `element.setAttribute('data-open', '')`         |
| `document.querySelector('.card__title')`           | `document.querySelector('[data-js-card-title]')` |
| `color: #2563eb`                                   | `color: var(--color-primary)`                   |
| `padding: 12px 16px`                               | `padding: var(--padding-md) var(--padding-lg)`  |
| `transition: all 0.3s ease`                        | `transition: background-color var(--duration-normal) var(--ease-default)` |
| `z-index: 9999`                                    | `z-index: var(--z-modal)`                       |
| `@import url('component.css')`                     | `<link>` dans le HTML (évite le blocage en cascade) |
| `var myVar = ...`                                  | `const myVar = ...`                              |

---

## 9. Versioning de ce document

| Version | Date       | Changement                                                              |
| ------- | ---------- | ----------------------------------------------------------------------- |
| 0.1     | 2026-03-25 | Création initiale                                                           |
| 0.2     | 2026-04-11 | Ajout §3.9 CSS Containment, §3.10 @supports, §4.8 requestIdleCallback      |
| 0.3     | 2026-04-11 | Ajout `<footer class="app-shell__status">` dans le template HTML de base   |
