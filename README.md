# Backoffice UI Kit

Système de design et workflow de conception d'écrans pour applications web backoffice — HTML5, CSS natif, JavaScript ES2024+, sans framework en production.

Ce dépôt réunit deux choses distinctes mais couplées : le **Covenant Design System**, qui définit les règles visuelles et techniques, et un **workflow de maquettage** structuré en phases, conçu pour être utilisé en collaboration avec un agent IA.

---

## Structure du dépôt

```
.
├── CLAUDE.md                    Configuration Claude Code (skill, règles critiques)
├── START.md                     Protocole de démarrage — session design/maquette
├── START-IMPLEMENTATION.md      Protocole de démarrage — session implémentation
├── WORKFLOW-DESIGN-MAQUETTE.md  Référence complète du workflow de conception
├── manifest.md                  État global du projet (écrans × versions DS)
├── app-shells.md                Déclinaisons App Shell disponibles
├── catalog.md                   Registre des composants (états, statuts)
├── catalog/                     Créé progressivement en Phase 5
│   └── [composant]/
│       ├── index.html           Catalogue vivant du composant
│       └── component.css
├── screens/                     Créé progressivement en Phase 1
│   └── [nom-ecran]/
│       ├── brief.md             Phase 1 — expression du besoin
│       ├── session.md           Contexte inter-sessions
│       ├── index.html           Prototype itératif
│       ├── layout.css           Styles de mise en page
│       └── components.css       Styles composants de l'écran
└── design-system/
    ├── README.md
    ├── SKILL.md                 Point d'entrée du skill Claude (table de routage)
    ├── VERSION.md               Version courante du DS et changelog
    └── references/
        ├── 00-fondations.md
        ├── 01-design-tokens.md
        ├── 02-layout.md
        ├── 03-composants.md
        ├── 04-patterns.md
        ├── 05-etats-feedback.md
        ├── 06-iconographie.md
        ├── 07-regles-code.md
        └── 08-checklist.md
```

---

## Covenant Design System

Le Covenant Design System est un **contrat documenté** entre le concepteur et le développeur — humain ou IA — qui garantit la cohérence visuelle et comportementale des interfaces à condition d'être correctement suivi. Ce dépôt ne contient pas de bibliothèque de composants à installer : uniquement de la documentation rédigée pour être lue et appliquée à la génération de code.

### Stack

| Couche             | Choix                                     | Contrainte                                                                    |
| ------------------ | ----------------------------------------- | ----------------------------------------------------------------------------- |
| Markup             | HTML5 sémantique                          | —                                                                             |
| Styles             | CSS natif + custom properties             | Aucune valeur brute — tout passe par les tokens                               |
| Logique            | JavaScript vanilla, ES2024+, modules ES   | `type="module"` obligatoire                                                   |
| Compatibilité      | Web Platform **Baseline Newly Available** | Plancher de référence — vérifiable via [webstatus.dev](https://webstatus.dev) |
| Navigateurs cibles | Chrome, Opera (Chromium)                  | —                                                                             |
| Accessibilité      | WCAG 2.2 niveau AA                        | Non négociable                                                                |

Aucun framework, pré-processeur ou transpileur dans le code livré. Les fichiers HTML, CSS et JS produits sont directement exécutables par le navigateur.

### Documents de référence

| Document               | Contenu                                                          |
| ---------------------- | ---------------------------------------------------------------- |
| `00-fondations.md`     | Cadre, périmètre, stack, responsive, glossaire                   |
| `01-design-tokens.md`  | Couleurs, typographie, espacements, ombres, rayons, z-index      |
| `02-layout.md`         | App Shell, grille, conteneurs, container queries, utilitaires    |
| `03-composants.md`     | Catalogue des composants (bouton, input, table, dropdown…)       |
| `04-patterns.md`       | Patterns UI (formulaire, liste, modal, drawer, toast, settings…) |
| `05-etats-feedback.md` | États de chargement, erreur, succès, vide, désactivation         |
| `06-iconographie.md`   | Système Lucide SVG, conventions d'intégration                    |
| `07-regles-code.md`    | Conventions HTML/CSS/JS, BEM, nommage, structure de fichiers     |
| `08-checklist.md`      | Grille de validation avant mise en production                    |

### Utilisation avec un agent IA

Le design system inclut un fichier `design-system/SKILL.md` conçu pour une utilisation en tant que skill Claude. Il embarque les contraintes universelles et une table de routage qui mappe les types de tâches aux documents de référence pertinents, évitant de charger l'intégralité de la documentation à chaque requête.

### Ce que le design system garantit — et ce qu'il ne garantit pas

Le design system garantit la cohérence visuelle et comportementale **uniquement si ses règles sont suivies**. Il ne garantit pas la qualité du code généré par une IA (une relecture humaine reste nécessaire), l'accessibilité de la page complète au sens d'un audit avec technologies d'assistance, ni la logique métier, la sécurité ou la performance réseau.

---

## Workflow de Design Maquette

Le workflow structure la conception d'un écran de l'expression du besoin jusqu'à la maquette navigateur finalisée. Il produit un prototype HTML/CSS/JS pur, visuellement fidèle au Design System, annotable et utilisable directement comme référence d'implémentation.

Le workflow ne produit pas de code d'implémentation (logique métier, intégration API, gestion d'état réelle).

### Phases

| Phase | Nom             | Artefact produit                                                              |
| ----- | --------------- | ----------------------------------------------------------------------------- |
| 1     | **Brief**       | `brief.md` — besoin fonctionnel, données, actions, App Shell, Design Thinking |
| 2     | **Squelette**   | `index.html` initial + `layout.css` — structure de zones sans composants      |
| 3     | **Composition** | Composants placés avec données statiques représentatives                      |
| 4     | **Finition**    | Fidélité visuelle finale — tous les états, typographie, densité, WCAG         |
| 5     | **Catalogue**   | Mise à jour de `catalog.md` et `manifest.md`                                  |

La progression est séquentielle. Chaque phase a un critère de sortie explicite avant de passer à la suivante.

### Artefacts transversaux

`manifest.md`, `app-shells.md` et `catalog.md` sont partagés entre tous les écrans. Ils sont chargés en contexte à chaque session et mis à jour en fin de cycle.

### Protocole inter-sessions

Chaque session produit ou met à jour un fichier `screens/[nom-ecran]/session.md` qui distille les décisions prises, les composants identifiés et les points ouverts. Ce fichier constitue le contexte de reprise pour la session suivante.

---

## Démarrage d'une session

Charger `START.md` dans le contexte de l'agent IA en premier. Ce fichier décrit l'ordre exact de chargement des artefacts, l'identification de l'écran en cours, le chargement du Design System en mode design, et la confirmation de la phase courante.

```

# Démarrage type

1. Fournir START.md à l'agent
2. Préciser l'écran : "On travaille sur l'écran [nom]"
3. L'agent confirme le contexte chargé avant de commencer

```

---

## Outillage de développement

L'outillage est entièrement en `devDependencies` — aucun impact sur le code livré.

```json
{
  "browserslist": ["baseline newly available with downstream"],
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

---

## Licence

MIT — voir [LICENSE](./LICENSE).

```

```
