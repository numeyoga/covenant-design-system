# Design System — 06 Iconographie

> **Prérequis** : avoir lu [00-fondations.md](./00-fondations.md), [01-design-tokens.md](./01-design-tokens.md) et [07-regles-code.md](./07-regles-code.md).

Ce document définit le système d'icônes : format, tailles, couleurs, accessibilité et bibliothèque de référence.

---

## 1. Principes

1. **Format SVG exclusif** : toutes les icônes sont en SVG inline. Pas de font icons, pas de PNG/WebP, pas de CSS background-image.
2. **Une seule bibliothèque** : le design system utilise une bibliothèque d'icônes open-source unique pour garantir la cohérence stylistique.
3. **Monochromes** : les icônes sont monochromes et héritent de la couleur du texte parent via `currentColor`.
4. **Toujours décoratives ou toujours labellisées** : chaque icône est soit décorative (`aria-hidden="true"`), soit porteuse de sens (auquel cas un label accessible est obligatoire).

---

## 2. Bibliothèque de référence

La bibliothèque recommandée est **Lucide** ([lucide.dev](https://lucide.dev)).

| Critère           | Valeur                                                |
| ----------------- | ----------------------------------------------------- |
| Licence           | ISC (libre, compatible commercial)                    |
| Style             | Outlined, 24×24 viewBox, stroke-width 2               |
| Nombre d'icônes   | 1500+                                                 |
| Format            | SVG individuel, sprite SVG, packages npm              |
| Cohérence         | Grille unifiée, épaisseur de trait constante           |

**Pourquoi Lucide ?**

- Successeur de Feather Icons avec une communauté active.
- Style outline neutre, bien adapté aux interfaces backoffice denses.
- Disponible en SVG brut (pas de dépendance framework).
- Épaisseur de trait configurable via l'attribut `stroke-width`.

### 2.1 Installation

Pour une utilisation Vanilla Web, deux approches :

**Approche 1 — SVG inline copié (recommandée pour les petits projets)** :

Copier le SVG depuis [lucide.dev](https://lucide.dev) directement dans le HTML.

**Approche 2 — Sprite SVG (recommandée pour les projets avec beaucoup d'icônes)** :

Générer un sprite SVG qui regroupe toutes les icônes utilisées, puis les référencer via `<use>` :

```html
<!-- Sprite chargé une fois (en haut du body ou dans un fichier externe) -->
<svg hidden aria-hidden="true" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <symbol id="icon-search" viewBox="0 0 24 24" fill="none"
            stroke="currentColor" stroke-width="2"
            stroke-linecap="round" stroke-linejoin="round">
      <circle cx="11" cy="11" r="8"/>
      <path d="m21 21-4.3-4.3"/>
    </symbol>
    <symbol id="icon-plus" viewBox="0 0 24 24" fill="none"
            stroke="currentColor" stroke-width="2"
            stroke-linecap="round" stroke-linejoin="round">
      <path d="M5 12h14"/>
      <path d="M12 5v14"/>
    </symbol>
    <!-- ... autres icônes ... -->
  </defs>
</svg>

<!-- Utilisation -->
<svg class="icon"><use href="#icon-search"></use></svg>
```

---

## 3. Intégration HTML

### 3.1 Icône décorative (accompagnant du texte)

L'icône n'ajoute pas de sens — le texte suffit.

```html
<button class="btn" data-variant="primary" type="button">
  <svg class="icon" aria-hidden="true"><use href="#icon-plus"></use></svg>
  <span class="btn__label">Ajouter</span>
</button>
```

Règles :
- `aria-hidden="true"` sur le SVG.
- L'icône ne porte **pas** de `role`, `title` ou `aria-label`.

### 3.2 Icône porteuse de sens (sans texte visible)

L'icône est le seul indice visuel de la fonction.

```html
<button class="btn" data-variant="ghost" data-size="sm" type="button"
        aria-label="Supprimer l'élément">
  <svg class="icon" aria-hidden="true"><use href="#icon-trash"></use></svg>
</button>
```

Règles :
- `aria-hidden="true"` sur le SVG (le SVG lui-même n'est pas la source du label).
- `aria-label` sur l'**élément interactif parent** (le bouton).
- Le `aria-label` décrit l'**action**, pas l'icône : "Supprimer l'élément", pas "Icône corbeille".

### 3.3 Icône informative hors d'un élément interactif

Cas rare : une icône qui transmet une information sans être dans un bouton ou un lien.

```html
<span class="status-icon" role="img" aria-label="En ligne">
  <svg class="icon" aria-hidden="true"><use href="#icon-circle-check"></use></svg>
</span>
```

Règles :
- Le `<span>` wrapper porte `role="img"` et `aria-label`.
- Le SVG reste `aria-hidden="true"`.

---

## 4. CSS des icônes

### 4.1 Classe de base

```css
.icon {
  width: 1em;
  height: 1em;
  flex-shrink: 0;

  fill: none;
  stroke: currentColor;
  stroke-width: 2;
  stroke-linecap: round;
  stroke-linejoin: round;
}
```

L'utilisation de `1em` comme taille fait que l'icône s'adapte automatiquement à la taille de texte du parent. Un bouton en `--text-sm` aura une icône plus petite qu'un bouton en `--text-lg`.

### 4.2 Tailles explicites

Pour les cas où l'icône doit avoir une taille fixe indépendante du texte :

```css
.icon[data-size="xs"]  { width: 0.75rem;  height: 0.75rem; }   /* 12px */
.icon[data-size="sm"]  { width: 1rem;     height: 1rem; }       /* 16px */
.icon[data-size="md"]  { width: 1.25rem;  height: 1.25rem; }    /* 20px */
.icon[data-size="lg"]  { width: 1.5rem;   height: 1.5rem; }     /* 24px */
.icon[data-size="xl"]  { width: 2rem;     height: 2rem; }       /* 32px */
.icon[data-size="2xl"] { width: 3rem;     height: 3rem; }       /* 48px */
```

### 4.3 Couleurs

Les icônes héritent de `currentColor` par défaut. Pour forcer une couleur spécifique :

```css
.icon[data-color="primary"]   { color: var(--color-primary); }
.icon[data-color="success"]   { color: var(--color-success); }
.icon[data-color="warning"]   { color: var(--color-warning); }
.icon[data-color="danger"]    { color: var(--color-danger); }
.icon[data-color="info"]      { color: var(--color-info); }
.icon[data-color="muted"]     { color: var(--color-text-muted); }
```

**Règle** : ne forcer une couleur que quand l'icône doit communiquer un statut indépendamment du texte. Dans tous les autres cas, laisser `currentColor` agir.

---

## 5. Icônes de référence par catégorie

Ce tableau liste les icônes Lucide recommandées pour les actions et concepts courants. Si une icône est utilisée pour un concept, elle doit **toujours** être la même dans toute l'application.

### 5.1 Actions CRUD

| Concept          | Icône Lucide       | Nom de symbole      |
| ---------------- | -------------------|----------------------|
| Créer / Ajouter  | `plus`             | `icon-plus`          |
| Modifier / Éditer| `pencil`           | `icon-pencil`        |
| Supprimer        | `trash-2`          | `icon-trash`         |
| Enregistrer      | `save`             | `icon-save`          |
| Annuler          | `x`                | `icon-x`             |
| Dupliquer        | `copy`             | `icon-copy`          |

### 5.2 Navigation

| Concept          | Icône Lucide       | Nom de symbole      |
| ---------------- | -------------------|----------------------|
| Accueil          | `home`             | `icon-home`          |
| Retour           | `arrow-left`       | `icon-arrow-left`    |
| Menu / Hamburger | `menu`             | `icon-menu`          |
| Fermer           | `x`                | `icon-x`             |
| Chevron droite   | `chevron-right`    | `icon-chevron-right` |
| Chevron bas      | `chevron-down`     | `icon-chevron-down`  |
| Lien externe     | `external-link`    | `icon-external-link` |

### 5.3 Recherche et filtres

| Concept          | Icône Lucide       | Nom de symbole      |
| ---------------- | -------------------|----------------------|
| Rechercher       | `search`           | `icon-search`        |
| Filtrer          | `filter`           | `icon-filter`        |
| Trier            | `arrow-up-down`    | `icon-sort`          |
| Réinitialiser    | `rotate-ccw`       | `icon-reset`         |

### 5.4 Statuts

| Concept          | Icône Lucide       | Nom de symbole      |
| ---------------- | -------------------|----------------------|
| Succès           | `circle-check`     | `icon-success`       |
| Erreur           | `circle-x`         | `icon-error`         |
| Avertissement    | `triangle-alert`   | `icon-warning`       |
| Information      | `info`             | `icon-info`          |
| En cours         | _(spinner CSS)_    | —                    |

### 5.5 Données et fichiers

| Concept          | Icône Lucide       | Nom de symbole      |
| ---------------- | -------------------|----------------------|
| Télécharger      | `download`         | `icon-download`      |
| Exporter         | `upload`           | `icon-upload`        |
| Fichier          | `file`             | `icon-file`          |
| Dossier          | `folder`           | `icon-folder`        |
| Tableau          | `table`            | `icon-table`         |
| Graphique        | `bar-chart-2`      | `icon-chart`         |

### 5.6 Utilisateur et système

| Concept          | Icône Lucide       | Nom de symbole      |
| ---------------- | -------------------|----------------------|
| Utilisateur      | `user`             | `icon-user`          |
| Paramètres       | `settings`         | `icon-settings`      |
| Déconnexion      | `log-out`          | `icon-logout`        |
| Verrouillé       | `lock`             | `icon-lock`          |
| Visible          | `eye`              | `icon-eye`           |
| Masqué           | `eye-off`          | `icon-eye-off`       |
| Notifications    | `bell`             | `icon-bell`          |
| Aide             | `circle-help`      | `icon-help`          |

---

## 6. Règles d'utilisation

1. **Cohérence absolue** : une icône = un concept. Si `trash-2` est utilisée pour "supprimer", elle ne doit jamais représenter autre chose.
2. **Ne jamais utiliser une icône seule comme unique indication** : les icônes d'action seules (bouton icône) doivent toujours avoir un `aria-label` **et** de préférence un tooltip.
3. **Pas de mélange de styles** : ne pas mélanger des icônes Lucide avec des icônes d'une autre bibliothèque. Si une icône manque dans Lucide, la dessiner dans le même style (outlined, 24×24, stroke-width 2).
4. **Pas d'icônes en tant que contenu textuel** : une icône ne remplace pas un texte explicatif. Elle l'accompagne.
5. **Épaisseur de trait constante** : `stroke-width: 2` pour toutes les icônes. Ne pas modifier cette valeur par composant.

---

## 7. Génération du sprite SVG

Pour les projets utilisant l'approche sprite, un script CLI peut générer le sprite à partir des fichiers SVG individuels :

```js
// scripts/build-sprite.js
import { readdir, readFile, writeFile } from 'node:fs/promises';
import { join } from 'node:path';

const ICONS_DIR = './assets/icons';
const OUTPUT = './assets/icons/sprite.svg';

async function buildSprite() {
  const files = (await readdir(ICONS_DIR)).filter(f => f.endsWith('.svg'));
  const symbols = [];

  for (const file of files) {
    const name = file.replace('.svg', '');
    const content = await readFile(join(ICONS_DIR, file), 'utf-8');

    // Extraire le contenu du SVG (sans la balise <svg> englobante)
    const inner = content
      .replace(/<svg[^>]*>/, '')
      .replace(/<\/svg>/, '')
      .trim();

    symbols.push(
      `  <symbol id="icon-${name}" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">\n    ${inner}\n  </symbol>`
    );
  }

  const sprite = `<svg xmlns="http://www.w3.org/2000/svg" hidden aria-hidden="true">\n  <defs>\n${symbols.join('\n')}\n  </defs>\n</svg>`;

  await writeFile(OUTPUT, sprite, 'utf-8');
  console.log(`Sprite generated: ${files.length} icons → ${OUTPUT}`);
}

buildSprite();
```

Commande :

```bash
node scripts/build-sprite.js
```

Ce script ne produit pas de code en production — il génère un asset statique (le fichier sprite SVG).

---

## 8. Versioning de ce document

| Version | Date       | Changement        |
| ------- | ---------- | ----------------- |
| 0.1     | 2026-03-25 | Création initiale |
