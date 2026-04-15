# Design System — 01 Design Tokens

> **Prérequis** : avoir lu [00-fondations.md](./00-fondations.md).

Les design tokens sont les **valeurs atomiques** du design system. Ils sont implémentés exclusivement en **CSS custom properties** (variables CSS), déclarées sur `:root` (ou sur un conteneur thématique). Aucune valeur "magique" ne doit apparaître dans le code applicatif : toute couleur, taille, espacement, ombre ou rayon doit référencer un token.

---

## 1. Principes des tokens

1. **Nommage sémantique** : les tokens portent un nom décrivant leur _rôle_, pas leur _valeur_. On écrit `--color-primary`, pas `--color-blue-600`.
2. **Deux niveaux** : les tokens sont organisés en tokens _primitifs_ (palette brute) et tokens _sémantiques_ (rôle dans l'interface). Le code applicatif ne consomme **que** les tokens sémantiques.
3. **Thématisation** : le passage d'un thème clair à un thème sombre se fait en réassignant les tokens sémantiques aux tokens primitifs appropriés. Le code applicatif ne change pas.
4. **Unités** : les tailles de texte sont en `rem` (relatives au `font-size` racine). Les espacements sont en `rem` ou `px` selon le contexte (voir section 4). Les tokens de couleur utilisent le format `oklch` pour la manipulation et `hex` pour la déclaration statique.

---

## 2. Couleurs

> **Principe — Gris teintés** : les tokens primitifs de la gamme neutre ne sont pas des gris purs (sans saturation). Chaque niveau porte une légère teinte dans la direction de la couleur primaire du système. Sur la palette actuelle (primaire bleu, hue ≈ 220), les neutres utilisent une saturation de 8–14 % pour la même hue. Un remplacement de la couleur primaire par une autre teinte implique de recalibrer la saturation des neutres en conséquence.

### 2.1 Palette primitive (référence)

Ces tokens définissent la palette brute. Ils ne sont **jamais** utilisés directement dans le code applicatif.

```css
:root {
  /* --- Neutrals (Gris) --- */
  --palette-neutral-0:    #ffffff;
  --palette-neutral-50:   #f8f9fa;
  --palette-neutral-100:  #f1f3f5;
  --palette-neutral-200:  #e9ecef;
  --palette-neutral-300:  #dee2e6;
  --palette-neutral-400:  #ced4da;
  --palette-neutral-500:  #adb5bd;
  --palette-neutral-600:  #868e96;
  --palette-neutral-700:  #495057;
  --palette-neutral-800:  #343a40;
  --palette-neutral-900:  #212529;
  --palette-neutral-950:  #16191d;
  --palette-neutral-1000: #000000;

  /* --- Primary (Bleu) --- */
  --palette-primary-50:   #e7f0ff;
  --palette-primary-100:  #c2d9ff;
  --palette-primary-200:  #99bfff;
  --palette-primary-300:  #6da3ff;
  --palette-primary-400:  #4a8cff;
  --palette-primary-500:  #2563eb;
  --palette-primary-600:  #1d4ed8;
  --palette-primary-700:  #1a3fba;
  --palette-primary-800:  #15309a;
  --palette-primary-900:  #0f2370;

  /* --- Success (Vert) --- */
  --palette-success-50:   #e6f9ed;
  --palette-success-100:  #b3ecc8;
  --palette-success-500:  #16a34a;
  --palette-success-700:  #0d7a36;
  --palette-success-900:  #064e22;

  /* --- Warning (Orange) --- */
  --palette-warning-50:   #fff7e6;
  --palette-warning-100:  #ffe5b3;
  --palette-warning-500:  #ea8c00;
  --palette-warning-700:  #b36b00;
  --palette-warning-900:  #6b4000;

  /* --- Danger (Rouge) --- */
  --palette-danger-50:    #fef2f2;
  --palette-danger-100:   #fde2e2;
  --palette-danger-500:   #dc2626;
  --palette-danger-700:   #b91c1c;
  --palette-danger-900:   #7f1d1d;

  /* --- Info (Cyan) --- */
  --palette-info-50:      #e6fbfe;
  --palette-info-100:     #b3f2fb;
  --palette-info-500:     #0891b2;
  --palette-info-700:     #0e7490;
  --palette-info-900:     #164e63;
}
```

### 2.2 Tokens sémantiques — Thème clair (par défaut)

Ces tokens sont ceux que le code applicatif consomme. Ils décrivent un rôle, pas une couleur.

```css
:root,
[data-theme="light"] {
  /* --- Surfaces --- */
  --color-bg-page:          var(--palette-neutral-50);
  --color-bg-default:       var(--palette-neutral-0);
  --color-bg-subtle:        var(--palette-neutral-100);
  --color-bg-muted:         var(--palette-neutral-200);
  --color-bg-inverse:       var(--palette-neutral-900);

  /* --- Texte --- */
  --color-text-default:     var(--palette-neutral-900);
  --color-text-subtle:      var(--palette-neutral-700);
  --color-text-muted:       var(--palette-neutral-600);
  --color-text-placeholder: var(--palette-neutral-500);
  --color-text-inverse:     var(--palette-neutral-0);
  --color-text-on-primary:  var(--palette-neutral-0);

  /* --- Bordures --- */
  --color-border-default:   var(--palette-neutral-300);
  --color-border-subtle:    var(--palette-neutral-200);
  --color-border-strong:    var(--palette-neutral-400);
  --color-border-focus:     var(--palette-primary-500);

  /* --- Primary (actions principales) --- */
  --color-primary:          var(--palette-primary-500);
  --color-primary-hover:    var(--palette-primary-600);
  --color-primary-active:   var(--palette-primary-700);
  --color-primary-subtle:   var(--palette-primary-50);

  /* --- Sémantique (statuts) --- */
  --color-success:          var(--palette-success-500);
  --color-success-subtle:   var(--palette-success-50);
  --color-success-text:     var(--palette-success-700);

  --color-warning:          var(--palette-warning-500);
  --color-warning-subtle:   var(--palette-warning-50);
  --color-warning-text:     var(--palette-warning-700);

  --color-danger:           var(--palette-danger-500);
  --color-danger-subtle:    var(--palette-danger-50);
  --color-danger-text:      var(--palette-danger-700);

  --color-info:             var(--palette-info-500);
  --color-info-subtle:      var(--palette-info-50);
  --color-info-text:        var(--palette-info-700);

  /* --- Focus --- */
  --color-focus-ring:       var(--palette-primary-400);
}
```

### 2.3 Tokens sémantiques — Thème sombre

```css
[data-theme="dark"] {
  /* --- Surfaces --- */
  --color-bg-page:          var(--palette-neutral-950);
  --color-bg-default:       var(--palette-neutral-900);
  --color-bg-subtle:        var(--palette-neutral-800);
  --color-bg-muted:         var(--palette-neutral-700);
  --color-bg-inverse:       var(--palette-neutral-100);

  /* --- Texte --- */
  --color-text-default:     var(--palette-neutral-100);
  --color-text-subtle:      var(--palette-neutral-300);
  --color-text-muted:       var(--palette-neutral-400);
  --color-text-placeholder: var(--palette-neutral-500);
  --color-text-inverse:     var(--palette-neutral-900);
  --color-text-on-primary:  var(--palette-neutral-0);

  /* --- Bordures --- */
  --color-border-default:   var(--palette-neutral-700);
  --color-border-subtle:    var(--palette-neutral-800);
  --color-border-strong:    var(--palette-neutral-600);
  --color-border-focus:     var(--palette-primary-400);

  /* --- Primary --- */
  --color-primary:          var(--palette-primary-400);
  --color-primary-hover:    var(--palette-primary-300);
  --color-primary-active:   var(--palette-primary-500);
  --color-primary-subtle:   var(--palette-primary-900);

  /* --- Sémantique (statuts) --- */
  --color-success:          var(--palette-success-500);
  --color-success-subtle:   var(--palette-success-900);
  --color-success-text:     var(--palette-success-100);

  --color-warning:          var(--palette-warning-500);
  --color-warning-subtle:   var(--palette-warning-900);
  --color-warning-text:     var(--palette-warning-100);

  --color-danger:           var(--palette-danger-500);
  --color-danger-subtle:    var(--palette-danger-900);
  --color-danger-text:      var(--palette-danger-100);

  --color-info:             var(--palette-info-500);
  --color-info-subtle:      var(--palette-info-900);
  --color-info-text:        var(--palette-info-100);

  /* --- Focus --- */
  --color-focus-ring:       var(--palette-primary-300);
}
```

### 2.4 Règles de contraste WCAG AA

Chaque combinaison texte/fond utilisée dans l'interface doit respecter les ratios suivants :

| Combinaison                          | Ratio minimum | Vérification                     |
| ------------------------------------ | ------------- | -------------------------------- |
| `--color-text-default` sur `--color-bg-default`   | ≥ 4.5:1 | Texte courant            |
| `--color-text-default` sur `--color-bg-subtle`    | ≥ 4.5:1 | Texte sur fond alterné   |
| `--color-text-subtle` sur `--color-bg-default`    | ≥ 4.5:1 | Texte secondaire         |
| `--color-text-muted` sur `--color-bg-default`     | ≥ 4.5:1 | Texte tertiaire          |
| `--color-text-placeholder` sur `--color-bg-default` | ≥ 3:1 | Placeholder (texte large)|
| `--color-primary` sur `--color-bg-default`         | ≥ 3:1  | Élément UI interactif    |
| `--color-text-on-primary` sur `--color-primary`    | ≥ 4.5:1 | Texte sur bouton primary |
| `--color-danger` sur `--color-bg-default`          | ≥ 3:1  | Icône/bordure d'erreur   |
| `--color-border-default` sur `--color-bg-default`  | ≥ 3:1  | Bordure d'input          |

**Outil de vérification recommandé** : tout rapport de contraste doit être validé avec un outil comme [Contrast Checker (WebAIM)](https://webaim.org/resources/contrastchecker/) ou l'inspecteur d'accessibilité de Chrome DevTools.

**Règle** : si une couleur de la palette est modifiée, **toutes** les combinaisons du tableau ci-dessus doivent être re-vérifiées dans les deux thèmes (clair et sombre).

---

## 3. Typographie

### 3.1 Pile de polices

```css
:root {
  --font-sans:  "Inter", "Segoe UI", system-ui, -apple-system, sans-serif;
  --font-mono:  "JetBrains Mono", "Fira Code", ui-monospace, "Cascadia Code", "Consolas", monospace;
}
```

**Inter** est la police par défaut. Si elle n'est pas installée, le navigateur utilise la pile de repli (polices système). Inter est choisie pour sa lisibilité à petite taille, ses variantes optiques et sa licence libre (SIL Open Font License). Pour une installation sans dépendance externe, Inter peut être auto-hébergée ou chargée depuis Google Fonts.

**JetBrains Mono** est utilisée pour le code, les identifiants techniques et les données tabulaires à largeur fixe.

### 3.2 Taille de base et unités

```css
:root {
  font-size: 16px; /* 1rem = 16px — ne pas modifier */
}
```

Toutes les tailles de texte sont exprimées en `rem`. La taille racine est fixée à `16px` et ne doit **jamais** être modifiée. Cela garantit que `1rem = 16px` dans tout le système.

### 3.3 Échelle typographique

L'échelle utilise un ratio proche de 1.25 (Major Third) adapté aux besoins d'une interface dense.

```css
:root {
  --text-xs:    0.75rem;    /* 12px — Badges, labels très secondaires          */
  --text-sm:    0.8125rem;  /* 13px — Texte secondaire, aide contextuelle      */
  --text-base:  0.875rem;   /* 14px — Texte courant par défaut de l'interface  */
  --text-md:    1rem;       /* 16px — Texte mis en avant, introductions        */
  --text-lg:    1.125rem;   /* 18px — Sous-titres de section                   */
  --text-xl:    1.25rem;    /* 20px — Titres de cartes, titres de panneaux     */
  --text-2xl:   1.5rem;     /* 24px — Titres de page                           */
  --text-3xl:   1.875rem;   /* 30px — Titres principaux                        */
}
```

**Choix de 14px comme taille de base d'interface** : pour les applications backoffice à forte densité d'information, 14px offre le meilleur compromis entre lisibilité et densité. Le `font-size` racine reste à 16px (standard navigateur) — c'est le token `--text-base` qui fixe la taille par défaut des composants.

### 3.4 Hauteurs de ligne

```css
:root {
  --leading-tight:   1.25;  /* Titres, texte court            */
  --leading-normal:  1.5;   /* Texte courant, paragraphes     */
  --leading-relaxed: 1.625; /* Blocs de texte longs, lecture   */
}
```

**Règle** : tout texte courant (paragraphes, descriptions, cellules de tableau contenant du texte) utilise `--leading-normal` (1.5). Les titres utilisent `--leading-tight` (1.25).

### 3.5 Graisses

```css
:root {
  --font-weight-regular:  400;
  --font-weight-medium:   500;
  --font-weight-semibold: 600;
  --font-weight-bold:     700;
}
```

| Token                      | Usage                                              |
| -------------------------- | -------------------------------------------------- |
| `--font-weight-regular`    | Texte courant, descriptions, cellules de tableau   |
| `--font-weight-medium`     | Labels, libellés de navigation, en-têtes de table  |
| `--font-weight-semibold`   | Titres de sections, noms d'entités importants      |
| `--font-weight-bold`       | Titres de page, alertes, accentuation forte        |

### 3.6 Espacement des lettres

```css
:root {
  --tracking-tight:  -0.01em;  /* Titres ≥ --text-2xl       */
  --tracking-normal:  0;        /* Texte courant             */
  --tracking-wide:    0.025em;  /* Texte en majuscules, labels condensés */
}
```

### 3.7 Règles de hiérarchie typographique

Ces règles complètent l'échelle en §3.3 pour assurer une hiérarchie lisible dans les interfaces denses.

- **Trois niveaux de texte seulement** : `--color-text-default` (contenu principal), `--color-text-subtle` (contenu secondaire), `--color-text-muted` (métadonnées, labels). Ne pas créer de quatrième niveau.
- **Désaccentuer pour accentuer** : pour mettre en valeur un élément, réduire le poids visuel de tout ce qui l'entoure plutôt qu'augmenter sa taille ou son poids.
- **Trois graisses maximum** : `--font-weight-regular` (corps), `--font-weight-medium` (labels, navigation), `--font-weight-semibold` (titres, valeurs importantes). Ne pas utiliser `--font-weight-bold` en dehors des titres de page et alertes.
- **Données numériques tabulaires** : appliquer `font-variant-numeric: tabular-nums` sur toute colonne de tableau contenant des valeurs numériques variables (montants, pourcentages, compteurs). Cela aligne les colonnes sans recourir à une police monospace.
- **Lettre-espacement et casse** : les labels en uppercase reçoivent systématiquement `letter-spacing: var(--tracking-wide)`. Les titres ≥ `--text-2xl` reçoivent `letter-spacing: var(--tracking-tight)`.

---

## 4. Espacement

### 4.1 Unité de base

L'unité de base est **4px**. Toutes les valeurs d'espacement sont des multiples de 4px, exprimées en `rem`.

### 4.2 Échelle d'espacement

```css
:root {
  --space-0:    0;
  --space-0-5:  0.125rem;  /*  2px */
  --space-1:    0.25rem;   /*  4px */
  --space-1-5:  0.375rem;  /*  6px */
  --space-2:    0.5rem;    /*  8px */
  --space-3:    0.75rem;   /* 12px */
  --space-4:    1rem;       /* 16px */
  --space-5:    1.25rem;   /* 20px */
  --space-6:    1.5rem;    /* 24px */
  --space-8:    2rem;       /* 32px */
  --space-10:   2.5rem;    /* 40px */
  --space-12:   3rem;       /* 48px */
  --space-16:   4rem;       /* 64px */
  --space-20:   5rem;       /* 80px */
  --space-24:   6rem;       /* 96px */
}
```

### 4.3 Tokens d'espacement sémantiques

Pour les cas d'usage récurrents, des tokens sémantiques encapsulent les valeurs de l'échelle :

```css
:root {
  /* Espacement interne des composants (padding) */
  --padding-xs:     var(--space-1);    /*  4px — badges, chips         */
  --padding-sm:     var(--space-2);    /*  8px — boutons compacts      */
  --padding-md:     var(--space-3);    /* 12px — inputs, boutons       */
  --padding-lg:     var(--space-4);    /* 16px — cartes, panneaux      */
  --padding-xl:     var(--space-6);    /* 24px — sections de page      */

  /* Espacement entre éléments (gap) */
  --gap-xs:         var(--space-1);    /*  4px — entre éléments inline  */
  --gap-sm:         var(--space-2);    /*  8px — entre champs de form   */
  --gap-md:         var(--space-4);    /* 16px — entre composants       */
  --gap-lg:         var(--space-6);    /* 24px — entre sections         */
  --gap-xl:         var(--space-8);    /* 32px — entre blocs majeurs    */
}
```

### 4.4 Règles d'utilisation

- **Pas de valeurs arbitraires** : tout espacement dans le CSS applicatif doit utiliser un token `--space-*` ou `--padding-*` / `--gap-*`.
- **`gap` plutôt que `margin`** : dans les layouts flexbox et grid, utiliser la propriété `gap` pour l'espacement entre éléments. Réserver `margin` aux cas où `gap` n'est pas applicable (espacement entre composants hors d'un conteneur flex/grid).
- **Padding cohérent** : le padding d'un composant est identique sur ses quatre côtés ou suit le pattern vertical/horizontal (ex: `padding: var(--padding-sm) var(--padding-md);`).

---

## 5. Bordures et rayons

### 5.1 Épaisseur de bordure

```css
:root {
  --border-width-default: 1px;
  --border-width-thick:   2px;
}
```

### 5.2 Rayons de bordure

```css
:root {
  --radius-none:  0;
  --radius-sm:    0.25rem;   /*  4px — inputs, boutons, badges   */
  --radius-md:    0.375rem;  /*  6px — cartes, dropdowns         */
  --radius-lg:    0.5rem;    /*  8px — modales, panneaux         */
  --radius-xl:    0.75rem;   /* 12px — grandes cartes, conteneurs*/
  --radius-full:  9999px;    /* Cercle / pilule                  */
}
```

**Règle** : les éléments interactifs (boutons, inputs) utilisent `--radius-sm`. Les conteneurs (cartes, panneaux) utilisent `--radius-md` ou `--radius-lg`. Pas de rayons différents sur un même composant (pas de coin supérieur arrondi et coin inférieur droit).

---

## 6. Ombres (Élévation)

Les ombres expriment la hiérarchie visuelle par l'élévation. Le système utilise 4 niveaux :

```css
:root {
  --shadow-xs: 0 1px 2px 0 rgb(0 0 0 / 0.05);

  --shadow-sm: 0 1px 2px 0 rgb(0 0 0 / 0.08),
               0 1px 3px 0 rgb(0 0 0 / 0.12);

  --shadow-md: 0 2px 4px -1px rgb(0 0 0 / 0.08),
               0 4px 6px -1px rgb(0 0 0 / 0.10);

  --shadow-lg: 0 4px 6px -2px rgb(0 0 0 / 0.08),
               0 10px 15px -3px rgb(0 0 0 / 0.12);

  --shadow-xl: 0 8px 10px -4px rgb(0 0 0 / 0.08),
               0 20px 25px -5px rgb(0 0 0 / 0.12);
}
```

Chaque token d'ombre combine une **ombre de contact** (petit offset, forte opacité) et une **ombre ambiante** (grand offset, faible opacité), simulant un éclairage réaliste. En thème sombre, l'élévation est exprimée prioritairement par la luminosité du fond (`--color-bg-*`) plutôt que par les ombres.

| Token          | Usage                                        |
| -------------- | -------------------------------------------- |
| `--shadow-xs`  | Bordure subtile (alternative à une bordure)  |
| `--shadow-sm`  | Cartes au repos, éléments de liste           |
| `--shadow-md`  | Dropdowns, menus contextuels, popovers       |
| `--shadow-lg`  | Modales, drawers                             |
| `--shadow-xl`  | Notifications toast, éléments de premier plan|

**Thème sombre** : en thème sombre, les ombres sont moins perceptibles. L'élévation y est plutôt exprimée par la luminosité du fond (plus élevé = fond plus clair). Les tokens d'ombre restent identiques mais sont complétés par les différences de `--color-bg-*`.

---

## 7. Z-index

Un système de couches explicite évite les conflits de z-index :

```css
:root {
  --z-base:         0;
  --z-table-header: 1;    /* En-tête sticky de tableau — au-dessus des cellules, sous tout le reste */
  --z-dropdown:     100;
  --z-sticky:       200;
  --z-overlay:      300;
  --z-modal:        400;
  --z-popover:      500;
  --z-toast:        600;
  --z-tooltip:      700;
}
```

**Règle** : ne jamais utiliser un `z-index` numérique brut dans le CSS applicatif. Toujours référencer un token `--z-*`. Si un nouveau niveau est nécessaire, il doit être ajouté à cette échelle.

---

## 8. Transitions et animations

### 8.1 Durées

```css
:root {
  --duration-instant:  50ms;   /* Changement d'état immédiat (opacity)     */
  --duration-fast:     100ms;  /* Hover, focus, micro-interactions         */
  --duration-normal:   200ms;  /* Ouverture de dropdown, toggle            */
  --duration-slow:     300ms;  /* Modales, drawers, transitions de page    */
  --duration-slower:   500ms;  /* Animations complexes, charts             */
}
```

### 8.2 Courbes d'accélération (easing)

```css
:root {
  --ease-default:    cubic-bezier(0.4, 0, 0.2, 1);   /* Usage général       */
  --ease-in:         cubic-bezier(0.4, 0, 1, 1);       /* Élément qui sort    */
  --ease-out:        cubic-bezier(0, 0, 0.2, 1);       /* Élément qui entre   */
  --ease-in-out:     cubic-bezier(0.4, 0, 0.2, 1);     /* Mouvement complet   */
}
```

### 8.3 Règles

- Toute transition utilise un token `--duration-*` et un token `--ease-*`.
- Les propriétés animées doivent être listées explicitement (pas de `transition: all`).
- Respecter la préférence utilisateur `prefers-reduced-motion` :

```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

---

## 9. Préférence de thème

Le système supporte le thème clair et le thème sombre. Le thème est activé par l'attribut `data-theme` sur `<html>` ou sur un conteneur :

```html
<html data-theme="light"> <!-- ou "dark" -->
```

Pour suivre automatiquement la préférence système :

```css
@media (prefers-color-scheme: dark) {
  :root:not([data-theme="light"]) {
    /* Re-déclarer les tokens du thème sombre ici,
       identiques à [data-theme="dark"] */
  }
}
```

**Logique** : si `data-theme` est explicitement défini, il prime. Sinon, la media query `prefers-color-scheme` s'applique.

---

## 10. Récapitulatif — Convention de nommage des tokens

| Préfixe              | Catégorie      | Exemple                    |
| -------------------- | -------------- | -------------------------- |
| `--palette-*`        | Couleur brute  | `--palette-primary-500`    |
| `--color-*`          | Couleur rôle   | `--color-text-default`     |
| `--text-*`           | Taille texte   | `--text-base`              |
| `--leading-*`        | Hauteur ligne  | `--leading-normal`         |
| `--font-weight-*`    | Graisse        | `--font-weight-medium`     |
| `--tracking-*`       | Letter-spacing | `--tracking-normal`        |
| `--font-*`           | Famille police | `--font-sans`              |
| `--space-*`          | Espacement brut| `--space-4`                |
| `--padding-*`        | Padding rôle   | `--padding-md`             |
| `--gap-*`            | Gap rôle       | `--gap-sm`                 |
| `--radius-*`         | Rayon bordure  | `--radius-md`              |
| `--border-width-*`   | Épaisseur bord.| `--border-width-default`   |
| `--shadow-*`         | Ombre          | `--shadow-md`              |
| `--z-*`              | Z-index        | `--z-modal`                |
| `--duration-*`       | Durée anim.    | `--duration-normal`        |
| `--ease-*`           | Courbe anim.   | `--ease-default`           |

---

## 11. Fichier de tokens complet

En pratique, tous les tokens sont regroupés dans un fichier unique `tokens.css` importé en premier dans chaque application :

```
app/
├── css/
│   ├── tokens.css        ← Toutes les déclarations :root de ce document
│   ├── reset.css         ← Reset / normalize
│   ├── base.css          ← Styles de base (body, headings, links...)
│   └── ...
├── index.html
└── ...
```

```html
<link rel="stylesheet" href="css/tokens.css">
<link rel="stylesheet" href="css/reset.css">
<link rel="stylesheet" href="css/base.css">
```

L'ordre d'import est important : `tokens.css` doit être chargé **avant** tout autre fichier CSS.

---

## 12. Versioning de ce document

| Version | Date       | Changement        |
| ------- | ---------- | ----------------- |
| 0.1     | 2026-03-25 | Création initiale |
