# Workflow — Design Maquette

> Ce document formalise le processus de conception d'écrans pour applications web backoffice.
> Il couvre la totalité du cycle, de l'expression du besoin jusqu'à la maquette finalisée prête à implémenter.
> Il est **indépendant du framework d'implémentation** : le prototype produit est du HTML/CSS/JS pur, respectueux du Design System.

---

## Périmètre et objectif

**Ce workflow produit :** une maquette navigateur documentée, visuellement fidèle au Design System, annotée des composants nécessaires et de leurs états — utilisable directement comme référence par un développeur ou un agent IA d'implémentation.

**Ce workflow ne produit pas :** du code d'implémentation (logique métier, intégration API, gestion d'état réelle). C'est un sujet séparé.

**Contraintes fixes :**

- Le Design System est la référence visuelle et structurelle à tout moment.
- HTML5 sémantique, CSS natif avec tokens DS, JS minimal pour l'interactivité statique uniquement.
- Aucun framework CSS, JS ou WebComponent (Bootstrap, Vue, Lit, etc.).
- BEM appliqué dès la phase de maquette pour la lisibilité des états composants.
- `data-js-*` posés comme marqueurs d'intention, non implémentés.
- L'App Shell (header + footer statusbar obligatoires) est partagé entre tous les écrans.

---

## Structure de fichiers du projet

```
project/
├── START.md                        ← Protocole de démarrage de session (ce document)
├── manifest.md                     ← Vue globale : écrans × versions DS
├── app-shells.md                   ← Déclinaisons App Shell disponibles
├── catalog.md                      ← Registre composants (états, statuts, descriptions)
├── catalog/
│   └── [composant]/
│       ├── index.html              ← Catalogue vivant du composant
│       └── component.css
├── design-system/                  ← DS versionné (référence externe)
└── screens/
    └── [nom-ecran]/
        ├── brief.md                ← Phase 1 : expression du besoin
        ├── session.md              ← Distillation inter-sessions
        ├── index.html              ← Prototype itératif
        ├── layout.css              ← Styles de mise en page
        └── components.css         ← Styles composants de cet écran
```

---

## Artefacts persistants

Ces fichiers sont **transversaux** à tous les écrans. Ils doivent être chargés en contexte à chaque session.

### `manifest.md`

Vue globale de l'état du projet. Mis à jour à chaque fin de cycle d'écran.

```markdown
# Manifest

| Écran     | Version maquette | Version DS ciblée | Phase courante | Statut   |
| --------- | ---------------- | ----------------- | -------------- | -------- |
| dashboard | 1.0              | DS 0.3            | finalisé       | ready    |
| settings  | 0.1              | DS 0.3            | composition    | en cours |
```

### `app-shells.md`

Déclinaisons d'App Shell disponibles. **Gouvernance : toute nouvelle déclinaison requiert une raison documentée et une validation explicite.** Header et footer (statusbar) sont obligatoires dans toutes les déclinaisons.

```markdown
# App Shells

## Déclinaison A — Standard

Header + main content (pleine largeur) + footer statusbar.
Usage : écrans de listes, tableaux de bord.

## Déclinaison B — Sidebar

Header + sidebar navigation + main content + footer statusbar.
Usage : applications multi-sections avec navigation latérale.

## Ajout d'une déclinaison

Documenter ici : raison fonctionnelle, différence structurelle, validation.
```

### `catalog.md`

Registre de tous les composants du projet.

```markdown
# Catalogue de composants

## [nom-composant]

- **Statut** : prototype | finalisé | ready-for-dev
- **Écran d'origine** : [nom-ecran] v[x.x]
- **Version DS** : [x.x]
- **Description** : utilité fonctionnelle, cas d'usage
- **États** : default, hover, focus, active, disabled, error, loading, ...
- **Variantes** : primary, secondary, danger, ...
- **Catalogue vivant** : `catalog/[nom-composant]/index.html`
```

---

## Phases du workflow

### Phase 0 — Initialisation de session

**Avant toute chose**, charger dans le contexte de l'agent :

| Artefact                              | Obligatoire                |
| ------------------------------------- | -------------------------- |
| `manifest.md`                         | Oui                        |
| `app-shells.md`                       | Oui                        |
| `catalog.md`                          | Oui                        |
| `design-system/` (voir routing table) | Oui — mode design          |
| `screens/[nom-ecran]/session.md`      | Oui si l'écran existe déjà |
| `screens/[nom-ecran]/index.html`      | Oui si l'écran existe déjà |
| `screens/[nom-ecran]/layout.css`      | Oui si l'écran existe déjà |
| `screens/[nom-ecran]/components.css`  | Oui si l'écran existe déjà |

**Mode design du DS :** charger `00-fondations`, `01-design-tokens`, `02-layout`, `03-composants`, `04-patterns`, `05-etats-feedback`, `06-iconographie`. Ne pas charger `07-regles-code` ni `08-checklist` — ce sont des préoccupations d'implémentation, hors scope ici.

---

### Phase 1 — Brief

**Objectif :** formaliser ce que l'écran doit accomplir avant toute décision visuelle.

**Activités :**

- Expression du besoin fonctionnel en langage naturel.
- Identification des données affichées (nature, volume estimé).
- Identification des actions utilisateur (CRUD, navigation, déclencheurs).
- Contraintes spécifiques (permissions, contexte d'usage, enchaînements d'écrans).
- Choix de la déclinaison App Shell (parmi `app-shells.md`).

**Artefact produit :** `screens/[nom-ecran]/brief.md`

```markdown
# Brief — [Nom de l'écran]

## Besoin fonctionnel

[Description libre du besoin]

## Données affichées

| Donnée | Type | Volume estimé | Obligatoire |
| ------ | ---- | ------------- | ----------- |

## Actions utilisateur

| Action | Type | Déclencheur | Destination |
| ------ | ---- | ----------- | ----------- |

## Contraintes

[Permissions, contexte, enchaînements]

## App Shell

Déclinaison choisie : [A / B / autre]
Raison : [si non-standard]

## Version DS ciblée

[x.x]

## Design Thinking

> Ces quatre points doivent être décidés explicitement avant toute décision visuelle.
> Ils restent dans les limites du Design System — ils orientent les décisions non tranchées par une règle.

**Purpose** — Quel problème cet écran résout-il, et pour qui ?
[Réponse]

**Ton** — Quelle impression l'écran doit-il donner dans les limites du DS ?
(Ex : sobre et dense, guidé et progressif, orienté action, orienté lecture...)
[Réponse]

**Interaction principale** — Quelle est l'action centrale de cet écran ?
Elle doit être immédiate et sans friction pour l'utilisateur.
[Réponse]

**Différenciateur** — Qu'est-ce qui rend cet écran meilleur que la version générique
du même écran ? Une décision de hiérarchie visuelle, une réduction de friction,
un état vide utile, une densité mieux calibrée...
[Réponse]
```

---

### Phase 2 — Squelette

**Objectif :** définir la structure de zones de la page, sans composants réels.

**Règles :**

- Utiliser la déclinaison App Shell définie en Phase 1.
- Chaque zone est représentée par un bloc coloré avec un label descriptif (`<!-- zone: toolbar -->`).
- Les classes BEM de zone sont posées (`page`, `page__header`, `page__main`, etc.).
- Aucun composant réel dans cette phase — uniquement la structure.
- Les fonds de zone utilisent des tokens DS (`--color-bg-subtle`, `--color-bg-base`, etc.).

**Livrable :** `index.html` initial + `layout.css`.

**Critère de sortie :** la structure de zones est validée avant de passer à la Phase 3. L'App Shell est reconnaissable et cohérent avec `app-shells.md`.

---

### Phase 3 — Composition

**Objectif :** placer les composants dans chaque zone, avec données statiques.

**Règles :**

- Consulter `catalog.md` **avant** de définir un nouveau composant — s'il existe, l'utiliser.
- BEM appliqué sur tous les composants (`block__element--modifier`).
- `data-js-*` posés sur les éléments actionnables comme marqueurs d'intention (non implémentés).
- Données statiques représentatives (pas de `Lorem ipsum` — des données métier réalistes).
- Les variantes et états principaux sont représentés au moins une fois dans la page.

**Itération :** les fichiers `index.html`, `layout.css`, `components.css` sont modifiés progressivement au fil de la discussion. Chaque itération est fonctionnelle et lisible dans le navigateur.

**Critère de sortie :** tous les composants sont placés, les données sont représentatives, la structure est stable.

---

### Phase 4 — Finition

**Objectif :** atteindre la fidélité visuelle finale.

**Activités :**

- Tous les états visuels des composants (hover, focus, active, disabled, error, empty, loading).
- Typographie correcte : tailles, poids, couleurs via tokens DS.
- Densité conforme : 14px base, espacements tokens, pas de grands espaces vides.
- Vérification WCAG 2.2 AA : contrastes, cibles tactiles (min 24×24px), focus visible.
- Cohérence inter-composants : alignements, espacements, hiérarchie visuelle.
- Iconographie : Lucide SVG via sprite, tailles conformes DS.

**Critère de sortie :** le prototype ouvert dans le navigateur est visuellement indiscernable d'une maquette Figma. Un développeur peut en déduire l'implémentation sans ambiguïté.

---

### Phase 5 — Catalogue

**Objectif :** mettre à jour les artefacts partagés après chaque écran finalisé.

**Activités :**

- Pour chaque composant nouveau ou modifié dans cet écran :
  - Mettre à jour `catalog.md` (statut, états, variantes, description).
  - Créer ou mettre à jour `catalog/[composant]/index.html` avec le code du composant isolé.
- Mettre à jour `manifest.md` (statut de l'écran → `finalisé`, version maquette, version DS).

**Statuts de composant :**

| Statut          | Signification                                |
| --------------- | -------------------------------------------- |
| `prototype`     | Présent dans une maquette, non stabilisé     |
| `finalisé`      | Tous les états couverts, visuellement stable |
| `ready-for-dev` | Validé, prêt pour implémentation             |

---

## Protocole inter-sessions — `session.md`

Produit ou mis à jour **en fin de chaque session de travail**. Constitue le contexte de reprise pour la session suivante.

```markdown
# Session — [Nom de l'écran]

## Métadonnées

- Version maquette : [x.x]
- Version DS ciblée : [x.x]
- Phase courante : [1 / 2 / 3 / 4 / 5]
- Date dernière session : [AAAA-MM-JJ]

## Décisions prises

- [AAAA-MM-JJ] [Description de la décision]
- ...

## Composants identifiés

| Composant | Statut             | Provenance   |
| --------- | ------------------ | ------------ |
| [nom]     | nouveau / existant | catalog v[x] |

## Points ouverts

- [ ] [Question ou décision en suspens]

## Fichiers modifiés cette session

- index.html
- layout.css
- components.css
```

---

## Versioning

### Design System

Le DS est versionné indépendamment (ex. `DS 0.3`). Chaque maquette référence la version DS ciblée dans son `brief.md` et son `session.md`.

### Maquettes

Chaque écran est versionné indépendamment. La version est incrémentée à chaque cycle complet (Phases 1→5).

### Politique de migration

| Scénario                                     | Comportement                                                                 |
| -------------------------------------------- | ---------------------------------------------------------------------------- |
| Nouvelle version DS sur écran finalisé       | Nouvelle version maquette créée. L'ancienne reste valide pour sa version DS. |
| Nouvelle version DS sur écran en cours       | Migration immédiate dans le cycle courant.                                   |
| Évolution DS mineure (ajout token)           | Pas de migration obligatoire — opportuniste.                                 |
| Évolution DS majeure (changement structurel) | Migration explicite, planifiée, tracée dans `manifest.md`.                   |

---

## Fichier START.md

À placer à la racine du projet. Instruit l'agent IA en début de chaque session.

```markdown
# START — Protocole de démarrage de session

## 1. Charger les artefacts persistants

- `manifest.md`
- `app-shells.md`
- `catalog.md`

## 2. Identifier l'écran en cours de travail

Demander à l'utilisateur si non précisé.

## 3. Charger le contexte de l'écran

Si l'écran existe déjà :

- `screens/[nom-ecran]/session.md`
- `screens/[nom-ecran]/brief.md`
- `screens/[nom-ecran]/index.html`
- `screens/[nom-ecran]/layout.css`
- `screens/[nom-ecran]/components.css`

## 4. Charger le Design System en mode design

Fichiers à charger (dans l'ordre) :

1. `00-fondations.md` (§2–§4)
2. `01-design-tokens.md`
3. `02-layout.md`
4. `03-composants.md`
5. `04-patterns.md`
6. `05-etats-feedback.md`
7. `06-iconographie.md`

Ne pas charger : `07-regles-code.md`, `08-checklist.md` (hors scope design).

## 5. Identifier la phase courante

Lire `session.md` → champ "Phase courante".
Si c'est un nouvel écran → Phase 1.

## 6. Rappel des contraintes fixes

- Aucun framework CSS/JS/WebComponent.
- Tokens DS uniquement (pas de valeurs brutes).
- BEM sur tous les composants dès la Phase 3.
- `data-js-*` posés comme marqueurs, non implémentés.
- Header et footer (statusbar) obligatoires dans l'App Shell.
- Données statiques réalistes (pas de Lorem ipsum).
```

---

## Récapitulatif des artefacts

| Artefact                         | Scope  | Mis à jour                 |
| -------------------------------- | ------ | -------------------------- |
| `manifest.md`                    | Global | Fin de Phase 5             |
| `app-shells.md`                  | Global | Sur gouvernance explicite  |
| `catalog.md`                     | Global | Phase 5                    |
| `catalog/[composant]/index.html` | Global | Phase 5                    |
| `START.md`                       | Global | Sur évolution du workflow  |
| `screens/[nom]/brief.md`         | Écran  | Phase 1 (immuable ensuite) |
| `screens/[nom]/session.md`       | Écran  | Fin de chaque session      |
| `screens/[nom]/index.html`       | Écran  | Phases 2, 3, 4 (itératif)  |
| `screens/[nom]/layout.css`       | Écran  | Phases 2, 3, 4 (itératif)  |
| `screens/[nom]/components.css`   | Écran  | Phases 3, 4 (itératif)     |
