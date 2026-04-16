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
