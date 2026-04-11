# Design System — 02 Layout

> **Prérequis** : avoir lu [00-fondations.md](./00-fondations.md) et [01-design-tokens.md](./01-design-tokens.md).

Ce document définit comment structurer spatialement une page et ses régions. Il couvre le système de grille, les conteneurs, les patterns de mise en page et les règles d'agencement des composants.

---

## 1. Principes de layout

1. **CSS Grid pour la structure, Flexbox pour l'alignement** : la mise en page globale (page, régions, grilles de contenu) utilise CSS Grid. L'alignement interne des composants (barre d'outils, groupe de boutons, cellule de tableau) utilise Flexbox.
2. **Pas de framework de grille externe** : le layout repose entièrement sur CSS Grid natif et les custom properties définies dans ce document.
3. **Densité maîtrisée** : sur un viewport de 2048px, l'espace doit être utilisé efficacement. Pas de colonnes vides ni de marges latérales excessives. Les applications backoffice remplissent l'écran.
4. **Conteneurs fluides, breakpoints discrets** : les conteneurs s'adaptent fluidement à la largeur disponible. Les changements de structure (nombre de colonnes, disposition) ne se produisent qu'aux breakpoints définis dans les fondations.

---

## 2. Structure de page — App Shell

Toutes les applications couvertes par ce design system utilisent un **App Shell** : une structure fixe composée de régions persistantes (navigation, en-tête) et d'une zone de contenu principale qui défile.

### 2.1 Régions de l'App Shell

```
┌─────────────────────────────────────────────────────────┐
│                      Top Bar                            │
├──────────┬──────────────────────────────────────────────┤
│          │                                              │
│  Side    │              Main Content                    │
│  Nav     │              (scrollable)                    │
│          │                                              │
│ (fixed)  │                                              │
│          │                                              │
├──────────┴──────────────────────────────────────────────┤
│                    Status Bar (optionnel)                │
└─────────────────────────────────────────────────────────┘
```

| Région       | Rôle                                             | Comportement                                    |
| ------------ | ------------------------------------------------ | ----------------------------------------------- |
| Top Bar      | Identité de l'app, recherche globale, user menu  | Fixe en haut, pleine largeur                    |
| Side Nav     | Navigation principale, arborescence               | Fixe à gauche, rétractable (collapsed/expanded) |
| Main Content | Zone de travail, contenu applicatif               | Scrollable verticalement                        |
| Status Bar   | Informations système, actions globales (optionnel)| Fixe en bas, pleine largeur                     |

### 2.2 Implémentation CSS Grid

```css
.app-shell {
  display: grid;
  grid-template-columns: var(--shell-nav-width) 1fr;
  grid-template-rows: var(--shell-topbar-height) 1fr var(--shell-statusbar-height, 0px);
  grid-template-areas:
    "topbar  topbar"
    "sidenav main"
    "status  status";
  height: 100dvh;
  overflow: hidden;
}

.app-shell__topbar {
  grid-area: topbar;
  position: sticky;
  top: 0;
  z-index: var(--z-sticky);
}

.app-shell__sidenav {
  grid-area: sidenav;
  overflow-y: auto;
}

.app-shell__main {
  grid-area: main;
  overflow-y: auto;
  padding: var(--padding-lg);
}

.app-shell__status {
  grid-area: status;
}
```

### 2.3 Dimensions de l'App Shell

```css
:root {
  /* Top Bar */
  --shell-topbar-height:    3rem;     /* 48px */

  /* Side Nav */
  --shell-nav-width:        16rem;    /* 256px — expanded   */
  --shell-nav-width-collapsed: 3.5rem; /* 56px — collapsed  */

  /* Status Bar (optionnel, 0 par défaut) */
  --shell-statusbar-height: 0px;

  /* Padding du Main Content */
  --shell-main-padding:     var(--padding-lg);  /* 16px */
}
```

**Note** : `100dvh` (dynamic viewport height) est Baseline Widely Available. Il gère correctement les barres d'adresse mobiles, mais sert surtout ici à occuper exactement la hauteur du viewport navigateur sur desktop.

### 2.4 Side Nav rétractable

La navigation latérale supporte deux états : expanded (256px) et collapsed (56px, icônes seules). Le basculement est piloté par un attribut `data-nav-collapsed` :

```css
.app-shell[data-nav-collapsed] {
  grid-template-columns: var(--shell-nav-width-collapsed) 1fr;
}
```

```js
// Toggle de la navigation
document.querySelector('.app-shell').toggleAttribute('data-nav-collapsed');
```

### 2.5 Adaptation responsive de l'App Shell

```css
/* Desktop standard (1280–1439px) : rien ne change */

/* Desktop compact (1024–1279px) : nav collapsée par défaut */
@media (max-width: 1279px) {
  .app-shell {
    grid-template-columns: var(--shell-nav-width-collapsed) 1fr;
  }
}

/* Tablette (768–1023px) : nav en overlay */
@media (max-width: 1023px) {
  .app-shell {
    grid-template-columns: 1fr;
    grid-template-areas:
      "topbar"
      "main"
      "status";
  }

  .app-shell__sidenav {
    position: fixed;
    left: 0;
    top: var(--shell-topbar-height);
    bottom: 0;
    width: var(--shell-nav-width);
    transform: translateX(-100%);
    transition: transform var(--duration-normal) var(--ease-out);
    z-index: var(--z-overlay);
  }

  .app-shell__sidenav[data-open] {
    transform: translateX(0);
  }
}

/* Mobile (< 768px) : identique tablette */
```

---

## 3. Système de grille de contenu

À l'intérieur du Main Content, un système de grille flexible permet d'organiser le contenu en colonnes.

### 3.1 Grille de base

```css
.grid {
  display: grid;
  gap: var(--gap-md);  /* 16px par défaut */
  grid-template-columns: repeat(12, 1fr);
}
```

La grille utilise **12 colonnes** — un standard qui permet des divisions en 2, 3, 4 et 6 sans fractions complexes.

### 3.2 Placement des éléments

Le placement se fait via la propriété `grid-column` avec la notation `span` :

```css
.col-1   { grid-column: span 1; }
.col-2   { grid-column: span 2; }
.col-3   { grid-column: span 3; }   /* 1/4 */
.col-4   { grid-column: span 4; }   /* 1/3 */
.col-5   { grid-column: span 5; }
.col-6   { grid-column: span 6; }   /* 1/2 */
.col-7   { grid-column: span 7; }
.col-8   { grid-column: span 8; }   /* 2/3 */
.col-9   { grid-column: span 9; }   /* 3/4 */
.col-10  { grid-column: span 10; }
.col-11  { grid-column: span 11; }
.col-12  { grid-column: span 12; }  /* pleine largeur */
```

### 3.3 Grilles utilitaires courantes

Pour les cas d'usage les plus fréquents, des classes de grille prédéfinies évitent de manipuler les colonnes manuellement :

```css
/* Grilles à colonnes égales */
.grid-cols-1  { grid-template-columns: 1fr; }
.grid-cols-2  { grid-template-columns: repeat(2, 1fr); }
.grid-cols-3  { grid-template-columns: repeat(3, 1fr); }
.grid-cols-4  { grid-template-columns: repeat(4, 1fr); }
.grid-cols-6  { grid-template-columns: repeat(6, 1fr); }

/* Grilles à layout fixe (sidebar + contenu) */
.grid-sidebar-start {
  grid-template-columns: minmax(200px, 1fr) 3fr;
}

.grid-sidebar-end {
  grid-template-columns: 3fr minmax(200px, 1fr);
}

/* Grille auto-fit (colonnes qui s'adaptent) */
.grid-auto {
  grid-template-columns: repeat(auto-fit, minmax(var(--grid-auto-min, 280px), 1fr));
}
```

### 3.4 Gap de grille

```css
.gap-xs   { gap: var(--gap-xs); }   /*  4px */
.gap-sm   { gap: var(--gap-sm); }   /*  8px */
.gap-md   { gap: var(--gap-md); }   /* 16px — défaut */
.gap-lg   { gap: var(--gap-lg); }   /* 24px */
.gap-xl   { gap: var(--gap-xl); }   /* 32px */
```

### 3.5 Adaptation responsive de la grille

```css
@media (max-width: 1279px) {
  .grid { grid-template-columns: repeat(8, 1fr); }
  .col-9, .col-10, .col-11, .col-12 { grid-column: 1 / -1; }
}

@media (max-width: 1023px) {
  .grid { grid-template-columns: repeat(4, 1fr); }
  .col-5, .col-6, .col-7, .col-8 { grid-column: 1 / -1; }
}

@media (max-width: 767px) {
  .grid { grid-template-columns: 1fr; }
  [class*="col-"] { grid-column: 1 / -1; }
}
```

---

## 4. Conteneurs

### 4.1 Principe

Les conteneurs limitent la largeur du contenu et le centrent horizontalement. Pour les applications backoffice, les conteneurs sont optionnels : la plupart des vues occupent 100% de la largeur disponible. Les conteneurs sont utiles pour les pages de type "formulaire centré" ou "page de détails".

### 4.2 Définition

```css
.container {
  width: 100%;
  margin-inline: auto;
  padding-inline: var(--padding-lg);
}

.container--sm   { max-width: 640px; }
.container--md   { max-width: 896px; }
.container--lg   { max-width: 1152px; }
.container--xl   { max-width: 1440px; }
.container--full { max-width: none; }   /* pleine largeur, padding conservé */
```

**Règle** : pour les vues de type dashboard, tableau ou liste, ne pas utiliser de conteneur à largeur limitée. Utiliser `.container--full` ou pas de conteneur du tout. Les conteneurs à largeur limitée sont réservés aux formulaires, pages de détails et contenus éditoriaux.

### 4.3 Container Queries

Pour les composants qui doivent s'adapter à la taille de leur conteneur parent plutôt qu'au viewport, le design system utilise les CSS Container Queries (Baseline Newly Available) :

```css
/* Déclarer un conteneur */
.card-container {
  container-type: inline-size;
  container-name: card;
}

/* Adapter un composant à son conteneur */
@container card (min-width: 400px) {
  .card__layout {
    flex-direction: row;
  }
}

@container card (max-width: 399px) {
  .card__layout {
    flex-direction: column;
  }
}
```

**Quand utiliser Container Queries vs Media Queries** :

| Critère                                  | Media Query          | Container Query      |
| ---------------------------------------- | -------------------- | -------------------- |
| Changement de structure de page          | ✓                    |                      |
| Adaptation d'un composant réutilisable   |                      | ✓                    |
| Composant dans un panneau redimensionnable |                    | ✓                    |
| Nombre de colonnes de la grille principale | ✓                  |                      |

---

## 5. Patterns de layout

### 5.1 Page avec en-tête + contenu

Le pattern le plus courant pour une page applicative :

```
┌────────────────────────────────────────────┐
│  Page Header                               │
│  [Breadcrumb] [Titre] [Actions]            │
├────────────────────────────────────────────┤
│                                            │
│  Contenu de page                           │
│  (grille, tableau, formulaire...)          │
│                                            │
└────────────────────────────────────────────┘
```

```css
.page {
  display: flex;
  flex-direction: column;
  gap: var(--gap-lg);
  height: 100%;
}

.page__header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-shrink: 0;
  gap: var(--gap-md);
  min-height: 3rem;
}

.page__header-start {
  display: flex;
  flex-direction: column;
  gap: var(--gap-xs);
}

.page__header-end {
  display: flex;
  align-items: center;
  gap: var(--gap-sm);
}

.page__body {
  flex: 1;
  min-height: 0; /* Permet le scroll interne */
}
```

### 5.2 Layout Master-Detail (liste + détail)

Pattern classique des applications backoffice où une liste à gauche affiche les détails de l'élément sélectionné à droite :

```
┌──────────────────┬─────────────────────────┐
│                  │                         │
│  Master (liste)  │  Detail (contenu)       │
│  1/3 largeur     │  2/3 largeur            │
│                  │                         │
└──────────────────┴─────────────────────────┘
```

```css
.master-detail {
  display: grid;
  grid-template-columns: minmax(280px, 1fr) 2fr;
  gap: 0;
  height: 100%;
}

.master-detail__master {
  overflow-y: auto;
  border-right: var(--border-width-default) solid var(--color-border-default);
}

.master-detail__detail {
  overflow-y: auto;
  padding: var(--padding-lg);
}

/* Desktop compact : master plus étroit */
@media (max-width: 1279px) {
  .master-detail {
    grid-template-columns: minmax(220px, 0.8fr) 2fr;
  }
}

/* Tablette : empilé verticalement ou onglets */
@media (max-width: 1023px) {
  .master-detail {
    grid-template-columns: 1fr;
    grid-template-rows: auto 1fr;
  }
}
```

### 5.3 Layout Dashboard (grille de widgets)

Pattern pour les tableaux de bord avec des cartes de tailles variables :

```css
.dashboard {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
  gap: var(--gap-md);
}

/* Widget qui occupe 2 colonnes */
.dashboard__widget--wide {
  grid-column: span 2;
}

/* Widget qui occupe 2 rangées */
.dashboard__widget--tall {
  grid-row: span 2;
}

@media (max-width: 1279px) {
  .dashboard {
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  }
  .dashboard__widget--wide {
    grid-column: span 1;
  }
}
```

### 5.4 Layout Formulaire

Les formulaires suivent une grille spécifique plus resserrée :

```css
.form-layout {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: var(--gap-md) var(--gap-lg);
  max-width: 896px;
}

/* Champ pleine largeur */
.form-layout__field--full {
  grid-column: 1 / -1;
}

/* Section du formulaire (titre + séparateur) */
.form-layout__section {
  grid-column: 1 / -1;
  padding-top: var(--space-4);
  border-top: var(--border-width-default) solid var(--color-border-subtle);
}

/* Actions du formulaire (boutons) */
.form-layout__actions {
  grid-column: 1 / -1;
  display: flex;
  justify-content: flex-end;
  gap: var(--gap-sm);
  padding-top: var(--space-6);
}

@media (max-width: 767px) {
  .form-layout {
    grid-template-columns: 1fr;
  }
}
```

---

## 6. Utilitaires de layout

### 6.1 Flexbox

```css
.flex          { display: flex; }
.flex-col      { flex-direction: column; }
.flex-wrap     { flex-wrap: wrap; }
.flex-1        { flex: 1 1 0%; }
.flex-auto     { flex: 1 1 auto; }
.flex-none     { flex: none; }
.flex-shrink-0 { flex-shrink: 0; }

/* Alignement */
.items-start    { align-items: flex-start; }
.items-center   { align-items: center; }
.items-end      { align-items: flex-end; }
.items-stretch  { align-items: stretch; }
.items-baseline { align-items: baseline; }

.justify-start   { justify-content: flex-start; }
.justify-center  { justify-content: center; }
.justify-end     { justify-content: flex-end; }
.justify-between { justify-content: space-between; }
```

### 6.2 Overflow

```css
.overflow-auto    { overflow: auto; }
.overflow-hidden  { overflow: hidden; }
.overflow-y-auto  { overflow-y: auto; }
.overflow-x-auto  { overflow-x: auto; }
```

### 6.3 Position

```css
.relative  { position: relative; }
.absolute  { position: absolute; }
.fixed     { position: fixed; }
.sticky    { position: sticky; top: 0; }
```

### 6.4 Taille

```css
.w-full    { width: 100%; }
.h-full    { height: 100%; }
.min-h-0   { min-height: 0; }  /* Indispensable pour le scroll dans flex/grid */
.min-w-0   { min-width: 0; }   /* Évite le débordement de contenu dans flex   */
```

---

## 7. Règles et contraintes

### 7.1 Pas de largeurs fixes dans le contenu

Les composants à l'intérieur du Main Content ne doivent **jamais** avoir de `width` fixe en pixels (sauf les icônes et avatars). Ils s'adaptent à la grille ou au conteneur parent.

**Exception** : le Side Nav et la Top Bar ont des dimensions fixes définies par les tokens `--shell-*`.

### 7.2 `min-height: 0` et `min-width: 0`

CSS Grid et Flexbox imposent par défaut `min-height: auto` et `min-width: auto`, ce qui empêche le scroll interne dans les éléments enfants. Ajouter `min-height: 0` (ou `min-width: 0`) sur tout conteneur flex/grid dont un enfant doit scroller.

### 7.3 Scroll

- Le scroll global de la page est **désactivé** (`overflow: hidden` sur `.app-shell`).
- Seules les régions désignées scrollent : `.app-shell__main`, `.app-shell__sidenav`, et les zones de contenu internes.
- Un conteneur scrollable doit toujours avoir une hauteur contrainte (via `flex: 1` + `min-height: 0`, ou `height` explicite).

### 7.4 Ordre des propriétés de layout dans le CSS

Pour la lisibilité, les propriétés de layout sont déclarées dans cet ordre au sein d'un bloc CSS :

1. `display`
2. `grid-*` / `flex-*` (propriétés de grille ou flex)
3. `gap`
4. `align-*` / `justify-*`
5. `position`
6. `top` / `right` / `bottom` / `left` / `inset`
7. `width` / `height` / `min-*` / `max-*`
8. `margin`
9. `padding`
10. `overflow`

---

## 8. Référence rapide — Choix du pattern de layout

| Besoin                                     | Pattern recommandé       | Section |
| ------------------------------------------ | ------------------------ | ------- |
| Structure globale de l'application         | App Shell                | 2       |
| Page avec titre + contenu                  | Page Header + Body       | 5.1     |
| Liste + vue détaillée                      | Master-Detail            | 5.2     |
| Tableau de bord avec cartes                | Dashboard Grid           | 5.3     |
| Formulaire à plusieurs champs             | Form Layout              | 5.4     |
| Contenu en colonnes régulières             | `.grid` + `.col-*`       | 3       |
| Colonnes qui s'adaptent à l'espace         | `.grid-auto`             | 3.3     |
| Composant qui s'adapte à son parent        | Container Query          | 4.3     |
| Contenu centré à largeur limitée           | `.container--md/lg`      | 4       |
| Contenu pleine largeur                     | `.container--full` ou pas de conteneur | 4 |

---

## 9. Versioning de ce document

| Version | Date       | Changement        |
| ------- | ---------- | ----------------- |
| 0.1     | 2026-03-25 | Création initiale |
