# Design System — 08 Checklist de Conformité

> Ce document est le dernier de la série. Il sert de **grille de validation** avant de considérer une page ou un composant comme conforme au design system.

Chaque section correspond à un document du design system. Les items sont formulés comme des assertions vérifiables par inspection visuelle ou par lecture du code source. Un item non coché signifie une non-conformité à corriger.

---

## Mode d'emploi

1. **Quand l'utiliser** : avant toute mise en production, revue de code, ou validation d'un composant généré par une IA.
2. **Qui l'utilise** : le développeur (humain ou IA) qui a produit le code.
3. **Comment** : parcourir chaque section applicable. Cocher chaque item. Toute non-conformité doit être corrigée ou justifiée par une exception documentée.

---

## 1. Fondations (00-fondations.md)

### Stack et compatibilité

- [ ] Le code est en HTML5, CSS natif et JavaScript vanilla (pas de framework, pré-processeur ou transpileur dans le code livré)
- [ ] Aucune fonctionnalité CSS/JS à statut "Limited Availability" n'est utilisée sans fallback documenté
- [ ] La vérification Browserslist est configurée avec `baseline newly available with downstream`

### Responsive

- [ ] L'affichage est testé et correct à 2048px de large (viewport de design principal)
- [ ] L'affichage est testé et correct à 1440px de large (desktop standard)
- [ ] L'affichage est testé et correct à 1024px de large (test minimum)
- [ ] Les media queries utilisent l'approche `max-width` (desktop-first)
- [ ] Les breakpoints utilisés sont ceux définis dans le design system (1439px, 1279px, 1023px, 767px)

---

## 2. Design Tokens (01-design-tokens.md)

### Couleurs

- [ ] Aucune couleur hex/rgb/hsl brute dans le CSS applicatif — uniquement des tokens `--color-*`
- [ ] Le code applicatif ne référence **aucun** token primitif `--palette-*` directement
- [ ] Les combinaisons texte/fond respectent les ratios de contraste WCAG AA (≥ 4.5:1 texte normal, ≥ 3:1 éléments UI)
- [ ] Le thème sombre fonctionne si `data-theme="dark"` est appliqué sur `<html>`

### Typographie

- [ ] Le `font-size` racine est `16px` (non modifié)
- [ ] Toutes les tailles de texte utilisent des tokens `--text-*`
- [ ] Les graisses utilisent des tokens `--font-weight-*`
- [ ] La hauteur de ligne des paragraphes et textes courants est `--leading-normal` (1.5)

### Espacement

- [ ] Aucune valeur de `padding`, `margin` ou `gap` brute en px — uniquement des tokens `--space-*`, `--padding-*` ou `--gap-*`
- [ ] L'espacement entre éléments dans les layouts flex/grid utilise `gap`, pas `margin`

### Bordures et ombres

- [ ] Les rayons de bordure utilisent des tokens `--radius-*`
- [ ] Les ombres utilisent des tokens `--shadow-*`
- [ ] Les `z-index` utilisent des tokens `--z-*` (aucune valeur numérique brute)
- [ ] Les en-têtes de tableau sticky utilisent `--z-table-header` (pas `--z-base`)

### Transitions

- [ ] Les durées de transition utilisent des tokens `--duration-*`
- [ ] Les courbes d'accélération utilisent des tokens `--ease-*`
- [ ] Aucun `transition: all` — les propriétés animées sont listées explicitement
- [ ] `prefers-reduced-motion` est respecté (animations désactivées)

---

## 3. Layout (02-layout.md)

### App Shell

- [ ] La page utilise la structure App Shell (topbar + sidenav + main + statusbar)
- [ ] L'App Shell occupe `100dvh`
- [ ] Le scroll global est désactivé (`overflow: hidden` sur `.app-shell`)
- [ ] Seules les zones désignées scrollent (main, sidenav, zones internes)
- [ ] La barre de statut est présente dans le markup (`<footer class="app-shell__status">`)
- [ ] Topbar, sidenav et statusbar restent visibles et fixes lors du scroll du contenu principal

### Grille

- [ ] La grille de contenu utilise CSS Grid avec 12 colonnes
- [ ] Les éléments de grille utilisent les classes `.col-*` ou `.grid-cols-*`
- [ ] Les grilles adaptatives utilisent `.grid-auto` avec `auto-fit` + `minmax`

### Conteneurs

- [ ] Les vues de type dashboard/tableau/liste n'utilisent pas de conteneur à largeur limitée
- [ ] Les formulaires centrés utilisent `.container--md` ou `.container--lg`

### Container Queries

- [ ] Les composants réutilisables dans des panneaux redimensionnables utilisent Container Queries (pas Media Queries)

---

## 4. Règles de Code (07-regles-code.md)

### HTML

- [ ] `<html>` a un attribut `lang` et un attribut `data-theme`
- [ ] L'ordre des `<link>` CSS est conforme : tokens → reset → base → layout → utilities → components → pages
- [ ] Le HTML utilise les éléments sémantiques appropriés (`nav`, `main`, `header`, `section`, `table`, etc.)
- [ ] Un seul `<h1>` par page, hiérarchie des titres sans saut de niveau
- [ ] Tout `<img>` a un attribut `alt` (vide si décoratif)
- [ ] Tout `<input>` est associé à un `<label>` via `for`/`id`
- [ ] Les états dynamiques utilisent des attributs `data-*` ou ARIA, pas des classes CSS
- [ ] Le JavaScript est lié via `<script type="module">`

### CSS

- [ ] Nommage BEM respecté : `.block`, `.block__element`
- [ ] Variantes par sélecteurs d'attribut (`[data-variant]`, `[data-size]`), pas par classes modifier
- [ ] Pas de `!important` (sauf `prefers-reduced-motion`)
- [ ] Propriétés ordonnées : Layout → Position → Dimensions → Typo → Visuel → Comportement
- [ ] Chaque composant a son propre fichier dans `css/components/`
- [ ] Les classes utilitaires ne concernent que le layout et l'espacement (pas de couleurs, typo ou bordures en utilitaires)
- [ ] Focus visible implémenté via technique double anneau (`box-shadow` dans `:focus-visible`) sur tous les éléments interactifs — voir `07-regles-code.md §3.5`
- [ ] Aucun `outline: none` sans le double anneau `box-shadow` dans `:focus-visible`

### JavaScript

- [ ] `const` par défaut, `let` si réassigné, jamais `var`
- [ ] Sélection DOM via `[data-js-*]`, pas via classes CSS
- [ ] Événements via `addEventListener`, pas d'attributs inline
- [ ] États visuels via attributs `data-*` / ARIA, pas `.style` ni `.classList`
- [ ] Appels async dans `try/catch`
- [ ] Modules ES (`import`/`export`)

---

## 5. Composants (03-composants.md)

### Boutons

- [ ] Tout bouton a un `type` explicite (`button` ou `submit`)
- [ ] Un seul bouton `primary` par vue
- [ ] Les boutons icône seule ont un `aria-label`
- [ ] Les icônes dans les boutons portent `aria-hidden="true"`
- [ ] Un seul bouton `primary` par section visible (jamais deux côte à côte)
- [ ] Les boutons destructifs sont physiquement séparés des boutons de confirmation (pas dans le même groupe)
- [ ] Les actions destructives de la page de paramètres sont isolées dans une section "Zone de danger"
- [ ] Dans les menus dropdown, les actions destructives sont les dernières items, après un séparateur

### Formulaires

- [ ] Chaque champ est dans un wrapper `.form-field`
- [ ] Les champs en erreur portent `aria-invalid="true"` et `aria-describedby`
- [ ] Les messages d'erreur portent `role="alert"`
- [ ] Les champs requis utilisent l'attribut natif `required`
- [ ] Les groupes de radio sont dans un `<fieldset>` avec `<legend>`

### Data Table

- [ ] Le tableau utilise `<table>` natif avec `<thead>`, `<tbody>`, `<th scope="col">`
- [ ] Le wrapper porte `role="region"`, `aria-label` et `tabindex="0"`
- [ ] Les boutons de tri portent `aria-sort`
- [ ] Les lignes sélectionnables portent `aria-selected`
- [ ] Les boutons d'action par ligne ont un `aria-label` identifiant l'entité

### Dropdown / Tabs

- [ ] Le trigger du dropdown porte `aria-expanded` et `aria-haspopup`
- [ ] Le menu porte `role="menu"`, les items `role="menuitem"`
- [ ] Navigation clavier : flèches haut/bas (dropdown), gauche/droite (tabs), Escape pour fermer
- [ ] Les tabs : seul l'onglet actif est dans le flux de tabulation, les autres portent `tabindex="-1"`

### Toggle

- [ ] L'input porte `role="switch"`
- [ ] L'input est visuellement masqué mais reste accessible (pas de `display: none`)

---

## 6. Patterns (04-patterns.md)

### Page Header

- [ ] Le breadcrumb est un `<nav>` avec `aria-label`
- [ ] La page courante dans le breadcrumb porte `aria-current="page"`
- [ ] Le bouton `primary` est le plus à droite

### Formulaire

- [ ] Les actions sont présentes en haut (Page Header) et en bas du formulaire
- [ ] Le formulaire porte `novalidate` (validation custom JS)
- [ ] Les champs sont groupés en sections logiques
- [ ] Au submit invalide, le focus est placé sur le premier champ en erreur

### Liste

- [ ] La toolbar porte `role="toolbar"` et `aria-label`
- [ ] La pagination est masquée si le nombre d'éléments ≤ taille d'une page
- [ ] La page courante porte `aria-current="page"`

### Modal / Drawer

- [ ] Utilise `<dialog>` natif avec `.showModal()`
- [ ] Le `<dialog>` porte `aria-labelledby` pointant vers le titre
- [ ] La modale de suppression utilise un bouton `danger`, pas `primary`
- [ ] Les modales ne sont pas imbriquées

### Toast

- [ ] Le conteneur porte `aria-live="polite"`
- [ ] Chaque toast porte `role="status"`
- [ ] Les toasts d'erreur restent affichés jusqu'à fermeture manuelle
- [ ] Maximum 3 toasts visibles simultanément

### Empty State

- [ ] L'état vide remplace le contenu (pas de tableau vide à côté)
- [ ] Le message indique pourquoi c'est vide et propose une action

### Sélection en masse

- [ ] La colonne checkbox est la première (la plus à gauche)
- [ ] La barre d'actions flottante est utilisée (pas la toolbar)
- [ ] Les actions non-destructives en masse s'exécutent immédiatement avec toast
- [ ] Les actions destructives en masse déclenchent une modal précisant le nombre d'éléments

### Page de paramètres

- [ ] Sauvegarde explicite par carte (bouton désactivé jusqu'à modification)
- [ ] Auto-save limité aux toggles isolés à faible risque
- [ ] Zone de danger isolée en bas de page avec bordure rouge
- [ ] Avertissement `beforeunload` implémenté

### Sidebar Navigation

- [ ] La page active porte `aria-current="page"`
- [ ] Les sous-menus utilisent `aria-expanded` et `hidden`

---

## 7. États et Feedback (05-etats-feedback.md)

### Chargement

- [ ] Les indicateurs de chargement portent `role="status"` et `aria-label`
- [ ] Le bouton en cours d'action porte `aria-disabled="true"` et `data-loading`
- [ ] Le skeleton loading a un texte `sr-only` "Chargement en cours"

### Erreurs

- [ ] Les messages d'erreur de champ sont spécifiques (pas de "Champ invalide" générique)
- [ ] Les formulaires avec erreurs multiples affichent un résumé avec liens vers les champs
- [ ] Les erreurs réseau utilisent un retry avec backoff exponentiel
- [ ] Les pages d'erreur (404, 500) proposent une action de retour

### Succès

- [ ] Les actions CRUD confirmées affichent un toast `success`
- [ ] La sauvegarde automatique a un indicateur d'état visible

### Désactivation

- [ ] Les éléments désactivés ont `opacity: 0.5`
- [ ] Les groupes de champs désactivés utilisent `<fieldset disabled>`
- [ ] Les éléments désactivés ont une explication accessible de pourquoi ils le sont

---

## 8. Iconographie (06-iconographie.md)

- [ ] Toutes les icônes sont en SVG inline (pas de font icons, pas d'images)
- [ ] Les icônes proviennent exclusivement de Lucide (ou sont dessinées dans le même style)
- [ ] Les icônes décoratives portent `aria-hidden="true"`
- [ ] Les icônes porteuses de sens ont un `aria-label` sur l'élément interactif parent
- [ ] La taille des icônes utilise `1em` ou un attribut `data-size`
- [ ] La couleur des icônes utilise `currentColor` par défaut
- [ ] `stroke-width` est constant à `2` sur toutes les icônes
- [ ] Une icône = un concept dans toute l'application

---

## 9. Performance (transversal)

- [ ] Pas de `@import` CSS — tous les fichiers CSS sont chargés via `<link>` dans le HTML
- [ ] Les scripts JS sont en `type="module"` (defer implicite)
- [ ] Les images ont des dimensions explicites (`width`/`height`) pour éviter le layout shift
- [ ] Les listes longues (> 200 éléments) utilisent la pagination ou le scroll virtuel
- [ ] Tout composant autonome déclare `contain: content` sur son élément racine
- [ ] Les exceptions à `contain: content` (enfants `fixed`, overflow visible, stacking context complexe) sont documentées dans le fichier CSS du composant
- [ ] Les composants avec enfants `position: fixed` (Dropdown, Tooltip) ne portent **pas** `contain: content`
- [ ] Le style de focus utilise la technique double anneau (`box-shadow: 0 0 0 2px bg, 0 0 0 4px focus-ring`) plutôt qu'un simple `outline`

---

## 10. Validation automatisée

Au-delà de cette checklist manuelle, les outils CLI suivants doivent passer sans erreur :

| Commande           | Vérifie                                       | Bloquant ? |
| ------------------ | --------------------------------------------- | ---------- |
| `npm run lint:css` | Conformité CSS vs Baseline, syntaxe Stylelint | Oui        |
| `npm run lint:js`  | Conformité JS API vs Baseline, syntaxe ESLint | Oui        |
| Contrast checker   | Ratios de contraste WCAG AA sur les tokens    | Oui        |
| HTML validator     | Validité sémantique du HTML                   | Oui        |

---

## 11. Versioning de ce document

| Version | Date       | Changement                                          |
| ------- | ---------- | --------------------------------------------------- |
| 0.1     | 2026-03-25 | Création initiale                                    |
| 0.2     | 2026-04-11 | Ajout items CSS Containment dans section Performance |
| 0.3     | 2026-04-11 | Ajout items status bar et scroll containment (§3)    |
| 0.4     | 2026-04-16 | Focus double anneau cohérent avec 07-regles-code §3.5 |
