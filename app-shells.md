# App Shells

> Déclinaisons d'App Shell disponibles pour le projet.
>
> **Gouvernance :** header et footer sont **obligatoires** dans toutes les déclinaisons.
> Toute nouvelle déclinaison requiert une raison fonctionnelle documentée et une validation explicite.
> L'ajout non justifié de déclinaisons est une non-conformité au workflow.

---

## Déclinaison A — Standard

**Structure :**

```
┌─────────────────────────────────────────┐
│  Header (topbar)                        │
├─────────────────────────────────────────┤
│                                         │
│  Main content (pleine largeur)          │
│                                         │
│                                         │
├─────────────────────────────────────────┤
│  Footer (statusbar)                     │
└─────────────────────────────────────────┘
```

**Usage :** écrans de listes, tableaux de bord, vues de détail pleine largeur.

**Zones BEM :**

- `.app-shell` — conteneur racine
- `.app-shell__header` — topbar fixe
- `.app-shell__main` — zone de contenu principale, scroll interne
- `.app-shell__footer` — statusbar fixe

---

## Déclinaison B — Sidebar

**Structure :**

```
┌─────────────────────────────────────────┐
│  Header (topbar)                        │
├──────────────┬──────────────────────────┤
│              │                          │
│  Sidebar     │  Main content            │
│  navigation  │                          │
│              │                          │
├──────────────┴──────────────────────────┤
│  Footer (statusbar)                     │
└─────────────────────────────────────────┘
```

**Usage :** applications multi-sections avec navigation latérale persistante.

**Zones BEM :**

- `.app-shell` — conteneur racine
- `.app-shell__header` — topbar fixe
- `.app-shell__nav` — sidebar, scroll interne si nécessaire
- `.app-shell__main` — zone de contenu principale, scroll interne
- `.app-shell__footer` — statusbar fixe

**Variantes de la sidebar :**

- Largeur fixe (défaut)
- Rétractable (`data-collapsed` sur `.app-shell__nav`)

---

## Ajout d'une déclinaison

Pour ajouter une déclinaison, documenter les points suivants **avant** de l'utiliser dans une maquette :

```markdown
## Déclinaison [Lettre] — [Nom]

**Raison fonctionnelle :**
[Pourquoi les déclinaisons existantes ne couvrent pas ce besoin]

**Validation :**
[Date] — validé par [Nicolas]

**Structure :**
[Schéma ASCII]

**Usage :**
[Cas d'usage spécifiques]

**Zones BEM :**
[Liste des zones et leurs classes]
```
