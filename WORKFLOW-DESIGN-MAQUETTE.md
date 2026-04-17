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
│   ├── tokens.css                  ← Tous les tokens DS (copie de la version courante)
│   ├── catalog.css                 ← Chrome des pages de catalogue (layout, header, section)
│   └── [composant]/
│       ├── index.html              ← Catalogue vivant du composant
│       └── component.css          ← CSS isolé du composant (auto-suffisant, dépend de tokens.css)
├── design-system/                  ← DS versionné (référence externe)
└── screens/
    └── [nom-ecran]/
        ├── brief.md                ← Phase 1 : expression du besoin
        ├── session.md              ← Distillation inter-sessions
        ├── index.html              ← Prototype itératif
        ├── layout.css              ← Styles de mise en page + tokens additionnels
        └── components.css         ← Styles composants de cet écran (plat, tous composants)
```

**Convention `catalog/`** : chaque `component.css` est **auto-suffisant** — il contient tout le CSS nécessaire pour afficher le composant isolément (y compris les styles de composants dont il dépend). Il référence `../tokens.css` implicitement via l'ordre de chargement dans `index.html`. Un composant de catalogue qui dépend d'un autre (ex: `task-group` → `task-row`) le documente en commentaire en tête de fichier et le charge dans son `index.html`.

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

[Voir design-system/VERSION.md — actuellement DS 0.4]

## Données exemples

> Ces données seront utilisées dans les maquettes Phase 3 et 4 à la place des placeholders Lorem ipsum.
> Fournir des exemples réalistes pour chaque type de donnée affiché à l'écran.

| Champ | Exemple réaliste |
| ----- | ---------------- |
| [Nom entité principale] | [ex: Alice Martin] |
| [Date/heure] | [ex: 14 avr. 2026, 09:42] |
| [Identifiant] | [ex: #INV-2026-0042] |
| [Statut] | [ex: En cours / Validé / Rejeté] |
| [Montant/valeur numérique] | [ex: 1 248,50 €] |
| [Texte long] | [ex: phrase réaliste du domaine, 50–80 caractères] |

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
- **HTML natif avant JS** : avant de créer un composant toggle/accordion/modal/dropdown custom, vérifier `03-composants.md` §12 (Dropdown), §15 (Accordion) et `07-regles-code.md §3.12` pour les équivalents natifs (`<details>`, `popover`, `<dialog>`). Zéro JS vaut mieux qu'un JS custom.
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

**Critère de sortie :** les conditions suivantes sont toutes vérifiées avant de passer à la Phase 5 :

- [ ] Aucune valeur brute dans les fichiers CSS (grep `#[0-9a-fA-F]{3,6}` = 0 résultat hors commentaires)
- [ ] Chaque composant interactif a tous ses états représentés au moins une fois (hover, focus, disabled)
- [ ] Focus visible sur tous les éléments interactifs (double anneau visible à l'inspection)
- [ ] La checklist `08-checklist.md` sections 1 à 6 : 0 item non coché applicable
- [ ] L'App Shell est correctement structuré : topbar + main + statusbar (+ sidenav si déclinaison B)
- [ ] Aucun Lorem ipsum ni "Item 1 / Item 2" dans les données
- [ ] Les contrastes WCAG AA sont respectés sur toutes les combinaisons texte/fond visibles
- [ ] **Testé dans le navigateur** : hover, focus, dropdowns, toggles et états disabled fonctionnels visuellement — les bugs de rendu ne sont détectables qu'en navigateur

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

Le protocole de démarrage de session est implémenté dans `START.md` à la racine du projet. Ce fichier est la **source de vérité unique** pour le démarrage d'une session — se référer à lui directement, ne pas dupliquer son contenu ici.

> Lire `START.md` en premier dans chaque session de travail.

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
