# Covenant Design System

Système de design pour applications web backoffice — HTML5, CSS natif, JavaScript ES2024+, sans framework en production.

---

## Principe

Le Covenant Design System est un **contrat documenté** entre le concepteur et le développeur — humain ou IA — qui garantit la cohérence visuelle et comportementale des interfaces à condition d'être correctement suivi. Son nom reflète cette philosophie : une règle écrite est une règle applicable, vérifiable et opposable.

Il ne s'agit pas d'une bibliothèque de composants à installer. Ce dépôt contient uniquement de la **documentation** : des règles, des tokens, des spécifications de composants et des patterns, rédigés pour être lus et appliqués à la main ou par un agent IA au moment de la génération de code.

---

## Stack

| Couche             | Choix                                     | Contrainte                                             |
| ------------------ | ----------------------------------------- | ------------------------------------------------------ |
| Markup             | HTML5 sémantique                          | —                                                      |
| Styles             | CSS natif + custom properties             | Aucune valeur brute — tout passe par les tokens        |
| Logique            | JavaScript vanilla, ES2024+, modules ES   | `type="module"` obligatoire                            |
| Compatibilité      | Web Platform **Baseline Newly Available** | Plancher de référence — vérifiable via `webstatus.dev` |
| Navigateurs cibles | Chrome, Opera (moteur Chromium)           | —                                                      |
| Accessibilité      | WCAG 2.2 niveau AA                        | Non négociable                                         |

Le code applicatif ne contient aucun framework, pré-processeur ou transpileur. Les fichiers HTML, CSS et JS livrés sont directement exécutables par le navigateur.

---

## Structure des documents

```
00-fondations.md        Cadre, périmètre, stack, responsive, glossaire
01-design-tokens.md     Couleurs, typographie, espacements, ombres, rayons, z-index
02-layout.md            App Shell, grille, conteneurs, container queries, utilitaires
03-composants.md        Catalogue des composants (bouton, input, table, dropdown…)
04-patterns.md          Patterns UI (formulaire, liste, modal, drawer, toast, nav, settings…)
05-etats-feedback.md    États de chargement, erreur, succès, vide, désactivation
06-iconographie.md      Système Lucide SVG, conventions d'intégration
07-regles-code.md       Conventions HTML/CSS/JS, BEM, nommage, structure de fichiers
08-checklist.md         Grille de validation avant mise en production
```

---

## Ordre de lecture

Pour un développeur (humain ou IA) qui prend en main le système pour la première fois :

1. `00-fondations.md` — comprendre le cadre et les contraintes techniques
2. `01-design-tokens.md` — les valeurs atomiques que tout le reste consomme
3. `02-layout.md` — la structure de page et l'App Shell
4. `07-regles-code.md` — les conventions CSS, HTML et JS
5. `03-composants.md` et `04-patterns.md` — selon le besoin
6. `08-checklist.md` — valider chaque implémentation avant livraison

---

## Principes directeurs

Six principes couvrent les décisions non tranchées par une règle explicite :

1. **Fonction avant forme** — l'utilisabilité prime sur l'esthétique.
2. **Densité maîtrisée** — l'information est dense mais lisible ; pas de grands espaces vides.
3. **Prévisibilité** — un élément qui se ressemble se comporte de la même façon partout.
4. **Progressivité** — la complexité est révélée graduellement.
5. **Feedback immédiat** — toute action produit un retour visuel en moins de 100 ms.
6. **Tolérance à l'erreur** — les actions destructives demandent confirmation ; les erreurs de saisie sont détectées tôt.

---

## Utilisation avec un agent IA

Le design system est structuré pour être consommé par un agent IA au moment de la génération de code. Chaque règle est formulée de façon explicite, mesurable et autonome — sans implicite ni "bon sens" supposé.

### Utilisation directe

Charger les documents pertinents dans le contexte de l'agent avant de générer du code. L'ordre de lecture recommandé ci-dessus s'applique également à cette approche.

### Utilisation via skill agent (Claude)

Ce dépôt inclut un fichier `SKILL.md` qui sert de point d'entrée pour une utilisation en tant que skill Claude. Il embarque les contraintes universelles et une table de routage qui mappe les types de tâches aux documents de référence pertinents, évitant de charger l'intégralité de la documentation pour chaque requête.

```
SKILL.md                Point d'entrée du skill — contraintes universelles + table de routage
references/             Documents de référence accessibles via la table de routage
```

### Ce que le design system garantit — et ce qu'il ne garantit pas

Le design system garantit la cohérence visuelle et comportementale si ses règles sont suivies. Il ne garantit pas :

- la qualité du code généré par une IA — une relecture humaine reste nécessaire ;
- l'accessibilité de la page complète — les règles couvrent les composants, pas le contexte d'usage ;
- la logique métier, la sécurité ou la performance réseau.

---

## Outillage de développement

L'outillage est entièrement en `devDependencies` — aucun impact sur le bundle livré.

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

| Outil                         | Rôle                                                                |
| ----------------------------- | ------------------------------------------------------------------- |
| Browserslist                  | Déclare les navigateurs cibles, consommé par les linters            |
| Stylelint + browser-compat    | Signale les propriétés CSS hors Baseline                            |
| ESLint + eslint-plugin-compat | Signale les API JS hors Baseline                                    |
| @eslint/css + use-baseline    | Vérifie les paires propriété/valeur CSS contre les données Baseline |

---

## Viewport de référence

L'interface est conçue pour un viewport CSS de **2048 × 1056 px** — correspondant à un écran 2560 × 1440 (QHD) avec un scaling OS à 125 %. Le CSS est écrit en **desktop-first** avec des `max-width` media queries descendantes.

| Breakpoint             | Seuil     |
| ---------------------- | --------- |
| Desktop large (défaut) | ≥ 1440 px |
| Desktop standard       | ≤ 1439 px |
| Desktop compact        | ≤ 1279 px |
| Tablette               | ≤ 1023 px |
| Mobile                 | ≤ 767 px  |

---

## Versioning

Chaque document porte son propre tableau de versions en fin de fichier. Le système global suit un versioning sémantique `MAJEUR.MINEUR` :

- **Majeur** : rupture de compatibilité (renommage de token, suppression d'un composant, changement de convention structurelle).
- **Mineur** : ajout de règle, nouveau composant, clarification documentaire sans rupture.
