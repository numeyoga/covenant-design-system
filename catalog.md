# Catalogue de composants

> Registre de tous les composants utilisés dans le projet maquette.
> Mis à jour à la fin de chaque Phase 5 (Catalogue).
>
> **Règle :** avant de créer un nouveau composant dans une maquette, vérifier ici
> si un composant existant couvre le besoin. Si oui, le réutiliser tel quel ou documenter
> la raison de la variante.

---

## Statuts

| Statut | Signification |
|---|---|
| `prototype` | Présent dans une maquette, non stabilisé — peut changer |
| `finalisé` | Tous les états visuels couverts, visuellement stable |
| `ready-for-dev` | Validé par révision, prêt pour implémentation |

---

## Index

<!-- Mettre à jour cet index à chaque ajout de composant -->

| Composant | Statut | Écran d'origine | Version DS |
|-----------|--------|-----------------|------------|
| `btn` | `ready-for-dev` | — (composant de base DS) | DS 0.4 |
| `badge` | `finalisé` | todos v0.4 | DS 0.4 |
| `input` | `finalisé` | todos v0.4 | DS 0.4 |
| `checkbox` | `finalisé` | todos v0.4 | DS 0.4 |
| `dropdown` | `finalisé` | todos v0.4 | DS 0.4 |
| `task-row` | `finalisé` | todos v0.4 | DS 0.4 |
| `task-group` | `finalisé` | todos v0.4 | DS 0.4 |
| `sidenav` | `finalisé` | todos v0.4 | DS 0.4 |

---

## Composants

<!-- 
Modèle d'entrée — copier/coller pour chaque nouveau composant :

---

### [nom-composant]

- **Classe BEM principale** : `.nom-composant`
- **Statut** : prototype | finalisé | ready-for-dev
- **Écran d'origine** : [nom-ecran] v[x.x]
- **Version DS ciblée** : DS [x.x]
- **Description** : [utilité fonctionnelle, cas d'usage]

**États**
| État | Implémenté | Notes |
|------|-----------|-------|
| default | ✓ / — | |
| hover | ✓ / — | |
| focus | ✓ / — | |
| active | ✓ / — | |
| disabled | ✓ / — | |
| error | ✓ / — | |
| loading | ✓ / — | |

**Variantes** (`data-variant`)
| Variante | Description |
|----------|-------------|
| — | — |

**Catalogue vivant** : `catalog/[nom-composant]/index.html`

-->

---

### btn (exemple de référence)

- **Classe BEM principale** : `.btn`
- **Statut** : `ready-for-dev`
- **Écran d'origine** : — (composant de base du DS)
- **Version DS ciblée** : DS 0.4
- **Description** : Bouton d'action. Élément interactif déclenchant une action dans la vue courante. Ne pas utiliser pour la navigation (utiliser `<a>` à la place).

**États**
| État | Implémenté | Notes |
|------|-----------|-------|
| default | ✓ | Style de base par variante |
| hover | ✓ | Assombrissement du fond |
| focus | ✓ | Double anneau focus ring |
| active | ✓ | Enfoncement léger |
| disabled | ✓ | `opacity: 0.5`, `cursor: not-allowed` |
| loading | ✓ | `data-loading` + spinner inline + `aria-disabled="true"` |

**Variantes** (`data-variant`)
| Variante | Description |
|----------|-------------|
| `primary` | Action principale — **une seule par section visible** |
| `secondary` | Actions importantes secondaires |
| `danger` | Actions destructives uniquement |
| `ghost` | Actions tertiaires, barres d'outils, Annuler |
| `link` | Action inline dans du texte |

**Tailles** (`data-size`)
| Taille | Hauteur | Usage |
|--------|---------|-------|
| _(défaut)_ | 36px | Usage courant |
| `sm` | 28px | Tableaux, barres d'outils |
| `lg` | 44px | Actions principales de page |

**Catalogue vivant** : `catalog/btn/index.html`

---

### badge

- **Classe BEM principale** : `.badge`
- **Statut** : `finalisé`
- **Écran d'origine** : todos v0.4
- **Version DS ciblée** : DS 0.4
- **Description** : Étiquette inline compacte. Signale un statut, un compte ou une catégorie. Non interactif par défaut.

**Variantes** (`data-variant`)
| Variante | Couleur | Usage |
|----------|---------|-------|
| `neutral` | Gris | Compteurs, tags génériques |
| `info` | Bleu | Information contextuelle |
| `success` | Vert | Succès, validé |
| `warning` | Orange | Attention |
| `danger` | Rouge | Erreur, critique, en retard |

---

### input

- **Classe BEM principale** : `.input`
- **Statut** : `finalisé`
- **Écran d'origine** : todos v0.4
- **Version DS ciblée** : DS 0.4
- **Description** : Champ de saisie texte. Utilisé pour la recherche et les formulaires.

**États**
| État | Implémenté | Notes |
|------|-----------|-------|
| default | ✓ | Bordure `--color-border-default` |
| hover | ✓ | Bordure `--color-border-strong` |
| focus | ✓ | Double anneau + bordure bleue |
| disabled | — | À couvrir |

**Tailles** (`data-size`)
| Taille | Hauteur | Usage |
|--------|---------|-------|
| _(défaut)_ | 36px | Formulaires |
| `sm` | 28px | Barres d'outils |

---

### checkbox

- **Classe BEM principale** : `.checkbox__input`
- **Statut** : `finalisé`
- **Écran d'origine** : todos v0.4
- **Version DS ciblée** : DS 0.4
- **Description** : Case à cocher native stylisée via `accent-color`. Utilisée dans les lignes de tâches.

---

### dropdown

- **Classe BEM principale** : `.dropdown`
- **Statut** : `finalisé`
- **Écran d'origine** : todos v0.4
- **Version DS ciblée** : DS 0.4
- **Description** : Menu contextuel flottant. Le trigger est un `.btn`, le menu est un `<div role="menu">` avec `hidden` par défaut.

**États**
| État | Implémenté | Notes |
|------|-----------|-------|
| fermé | ✓ | `hidden` sur `.dropdown__menu` |
| ouvert | ✓ | `hidden` retiré, `aria-expanded="true"` |
| item hover | ✓ | `--color-bg-subtle` |
| item danger | ✓ | `data-variant="danger"` → texte rouge |

**Modificateurs**
| Classe | Description |
|--------|-------------|
| `.dropdown__menu--end` | Alignement à droite du trigger |

---

### task-row

- **Classe BEM principale** : `.task-row`
- **Statut** : `finalisé`
- **Écran d'origine** : todos v0.4
- **Version DS ciblée** : DS 0.4
- **Description** : Ligne de tâche dans une liste groupée. Affiche titre, priorité, métadonnées (échéance, tags, projet) et actions contextuelles apparaissant au hover. Peut contenir des sous-tâches inline (`.task-row__subtasks`).

**États**
| État | Implémenté | Notes |
|------|-----------|-------|
| default | ✓ | Fond transparent |
| hover | ✓ | Fond `--color-bg-subtle`, handle + actions visibles |
| in-progress | ✓ | `data-state="in-progress"` → titre `--color-primary` |
| done | ✓ | `data-state="done"` → barré, opacité 0.7, projet non cliquable |
| overdue | ✓ | `data-overdue="true"` sur `.task-row__due` → rouge danger |

**Variantes de priorité** (`data-priority` sur `.task-row__priority-dot`)
| Valeur | Couleur |
|--------|---------|
| `high` | Rouge — `--color-danger` |
| `normal` | Orange — `--color-warning` |
| `low` | Bleu — `--color-info` |

**Catalogue vivant** : `catalog/task-row/index.html`

---

### task-group

- **Classe BEM principale** : `.task-group`
- **Statut** : `finalisé`
- **Écran d'origine** : todos v0.4
- **Version DS ciblée** : DS 0.4
- **Description** : Groupe de `task-row` avec en-tête pliable. L'en-tête affiche un point de priorité coloré, le titre et le compteur. Le toggle utilise `aria-expanded` + `aria-controls`.

**États**
| État | Implémenté | Notes |
|------|-----------|-------|
| développé | ✓ | `aria-expanded="true"` sur le toggle |
| replié | ✓ | `aria-expanded="false"` → chevron pivoté à -90° |
| groupe terminées | ✓ | `.task-group__title--done` → titre en muted |

**Catalogue vivant** : `catalog/task-group/index.html`

---

### sidenav

- **Classe BEM principale** : `.sidenav`
- **Statut** : `finalisé`
- **Écran d'origine** : todos v0.4
- **Version DS ciblée** : DS 0.4
- **Description** : Navigation latérale verticale. Organisée en groupes (`sidenav__group`) avec en-têtes et listes d'items. Extension DS : `.sidenav__color-dot` pour les projets/catégories colorés (couleur via `--dot-color` CSS custom property inline).

**États**
| État | Implémenté | Notes |
|------|-----------|-------|
| default | ✓ | Fond transparent |
| hover | ✓ | Fond `--color-bg-muted` |
| actif | ✓ | `aria-current="page"` → fond bleu subtil, texte primaire |
| collapsed | ✓ | `data-nav-collapsed` sur `.app-shell` → labels masqués |
