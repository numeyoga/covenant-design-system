# Design System — Document Fondateur

## 1. Qu'est-ce qu'un design system ?

Un design system est un ensemble structuré de **règles, principes, contraintes et composants réutilisables** qui gouvernent la conception et le développement d'interfaces utilisateur. Il sert de source de vérité unique (_single source of truth_) pour garantir la cohérence visuelle, fonctionnelle et technique à travers toutes les applications qui l'adoptent.

Un design system n'est **pas** :

- Une simple bibliothèque de composants UI.
- Un guide de style graphique isolé.
- Un framework CSS.

Un design system **est** :

- Un cadre de décision qui élimine l'ambiguïté lors du développement.
- Un contrat partagé entre concepteurs et développeurs (humains ou IA).
- Un système vivant qui évolue avec les besoins.

### Limites

Ce design system garantit la cohérence visuelle et comportementale **uniquement si ses règles sont correctement suivies**. Il ne constitue pas une garantie automatique de qualité :

- **Code généré par une IA** : une IA peut produire du code syntaxiquement conforme mais sémantiquement incorrect, ou omettre des règles qu'elle n'a pas lues. Tout code généré par une IA doit faire l'objet d'une relecture humaine avant mise en production.
- **Accessibilité** : les règles du design system couvrent les exigences WCAG 2.2 AA au niveau des composants et tokens. Elles ne substituent pas à un audit d'accessibilité de la page complète ni à des tests avec des technologies d'assistance réelles.
- **Qualité métier** : le design system ne couvre pas la logique applicative, la validation des données, la sécurité ou la performance réseau.

### Pourquoi ce design system existe

Ce design system est conçu pour être lu et appliqué aussi bien par un développeur humain que par une IA générative. Chaque règle doit donc être :

- **Explicite** : pas d'implicite ni de "bon sens" supposé.
- **Mesurable** : vérifiable par inspection du code ou du rendu.
- **Autonome** : compréhensible sans contexte oral supplémentaire.

---

## 2. Périmètre et objectifs

### 2.1 Types d'applications couvertes

| Caractéristique        | Valeur                                               |
| ---------------------- | ---------------------------------------------------- |
| Type                   | Applications métier, dashboards, SPA backoffice      |
| Usage                  | Fonctionnel, usage interne/personnel                 |
| Priorité               | Utilisabilité et efficacité > esthétique marketing   |
| Complexité UI attendue | Moyenne à élevée (tableaux, formulaires, graphiques) |

### 2.2 Ce que le design system ne couvre PAS

- Sites vitrines ou landing pages commerciales.
- Applications mobiles natives.
- Emails HTML.
- Documents imprimés.

### 2.3 Objectifs

1. **Cohérence** : toute application construite avec ce système doit avoir un aspect et un comportement prévisible.
2. **Efficacité** : réduire les décisions de design à prendre lors du développement.
3. **Maintenabilité** : un changement dans le système se propage à toutes les applications.
4. **Lisibilité IA** : une IA doit pouvoir générer du code conforme en lisant uniquement ces documents.

---

## 3. Contraintes techniques

### 3.1 Stack technologique — Vanilla Web

| Élément     | Choix                         | Justification                           |
| ----------- | ----------------------------- | --------------------------------------- |
| Markup      | HTML5 sémantique              | Standard, accessible, durable           |
| Styles      | CSS natif (custom properties) | Pas de dépendance, compatible Baseline  |
| Logique     | JavaScript vanilla (ES2024+)  | Pas de framework imposé, interopérable  |
| Référentiel | Web Platform Baseline 2024    | Garantit le support navigateurs récents |

**Principe Vanilla Web** : le code applicatif ne dépend d'aucun framework, pré-processeur ou transpileur en production. Les fichiers HTML, CSS et JS livrés sont directement exécutables par le navigateur. Des outils CLI de développement (linting, vérification de compatibilité) sont autorisés et recommandés, mais ils n'interviennent pas dans le bundle livré.

### 3.2 Navigateurs cibles

Les deux navigateurs cibles sont **Chrome** et **Opera**. Les deux utilisent le moteur **Chromium**, ce qui signifie qu'ils partagent le même support de fonctionnalités web à version de moteur équivalente.

Le design system cible le niveau **Baseline "Newly Available"** : fonctionnalités implémentées dans l'ensemble des navigateurs principaux du core set Baseline (Chrome, Edge, Firefox, Safari) mais depuis moins de 30 mois.

**Conséquence pratique** : puisque les navigateurs cibles sont exclusivement Chromium, le support réel est souvent en avance sur le statut Baseline. Cependant, on s'en tient au seuil Baseline "Newly Available" comme plancher de compatibilité pour deux raisons :

1. Garantir que le code reste portable si un navigateur non-Chromium est ajouté ultérieurement.
2. S'appuyer sur un référentiel documenté et outillé plutôt que sur des vérifications manuelles par version de Chrome.

**Règles d'utilisation des fonctionnalités :**

| Statut Baseline              | Utilisation autorisée                                    |
| ---------------------------- | -------------------------------------------------------- |
| Widely Available (≥ 30 mois) | Libre, sans restriction                                  |
| Newly Available (< 30 mois)  | Libre, sans restriction (c'est notre plancher)           |
| Limited Availability         | Interdit, sauf exception documentée avec fallback fourni |

**Référence de vérification** : [webstatus.dev](https://webstatus.dev)

### 3.3 Vérification de compatibilité — Outillage CLI

Le respect de la contrainte Baseline doit être vérifiable automatiquement pendant le développement, sans impacter le code livré. Voici la chaîne d'outils recommandée :

#### 3.3.1 Browserslist

Browserslist est le standard pour déclarer les navigateurs cibles. Il est consommé par les linters et outils de vérification.

**Configuration** (dans `package.json`) :

```json
{
  "browserslist": ["baseline newly available with downstream"]
}
```

La directive `with downstream` inclut les navigateurs basés sur Chromium (dont Opera) en plus du core set Baseline (Chrome, Edge, Firefox, Safari).

#### 3.3.2 Linting CSS — Stylelint

Stylelint avec le plugin `stylelint-browser-compat` permet de signaler les propriétés et valeurs CSS qui ne sont pas conformes au seuil Baseline choisi. Alternative : le plugin `stylelint-plugin-use-baseline` permet de définir directement des règles Baseline sans passer par Browserslist.

```js
// stylelint.config.js
module.exports = {
  plugins: ["stylelint-browser-compat"],
  rules: {
    "plugin/browser-compat": [
      true,
      {
        browserslist: ["baseline newly available with downstream"],
      },
    ],
  },
};
```

#### 3.3.3 Linting JS — ESLint

ESLint avec `eslint-plugin-compat` vérifie que les API JavaScript utilisées sont supportées par les navigateurs déclarés dans Browserslist.

#### 3.3.4 Linting CSS avancé — ESLint CSS

ESLint dispose aussi d'un support CSS natif avec la règle `use-baseline` (via le package `@eslint/css`). Cette règle utilise directement le package `web-features` pour vérifier les propriétés et valeurs CSS contre leur statut Baseline, y compris au niveau des paires propriété-valeur (clés BCD individuelles).

#### 3.3.5 Package `web-features` — Vérification programmatique

Pour des besoins ponctuels ou pour construire des outils personnalisés, le package npm `web-features` fournit un accès direct aux données Baseline :

```js
import { features } from "web-features";

// Vérifier le statut Baseline d'une fonctionnalité
const status = features["container-queries"]?.status.baseline;
// Retourne : "low" (Newly Available), "high" (Widely Available), ou false (Limited)

// Vérifier une clé BCD spécifique (propriété CSS, API JS, etc.)
const bcdStatus =
  features["subgrid"]?.status.by_compat_key[
    "css.properties.grid-template-columns.subgrid"
  ];
```

Ce package est utile pour :

- Créer des scripts de CI qui valident la conformité Baseline d'un projet.
- Permettre à une IA de vérifier la compatibilité d'une fonctionnalité avant de l'utiliser dans du code généré.

#### 3.3.6 Récapitulatif de la chaîne d'outils

| Outil                         | Rôle                            | Impacte le bundle ? |
| ----------------------------- | ------------------------------- | ------------------- |
| Browserslist                  | Déclare les navigateurs cibles  | Non                 |
| Stylelint + browser-compat    | Lint CSS vs Baseline            | Non                 |
| ESLint + eslint-plugin-compat | Lint JS API vs Baseline         | Non                 |
| @eslint/css + use-baseline    | Lint CSS propriétés vs Baseline | Non                 |
| `web-features` (npm)          | Vérification programmatique     | Non                 |
| webstatus.dev                 | Consultation manuelle           | Non                 |

Aucun de ces outils ne modifie, ne transpile ou ne transforme le code source. Ils servent exclusivement de garde-fous pendant le développement.

### 3.4 Accessibilité

| Critère                  | Exigence                                   |
| ------------------------ | ------------------------------------------ |
| Standard                 | WCAG 2.2 niveau AA                         |
| Contraste texte          | Ratio ≥ 4.5:1 (texte normal)               |
| Contraste texte large    | Ratio ≥ 3:1 (≥ 18pt ou 14pt bold)          |
| Contraste éléments UI    | Ratio ≥ 3:1                                |
| Navigation clavier       | Tous les éléments interactifs              |
| Focus visible            | Obligatoire, style défini                  |
| Attributs ARIA           | Utilisés quand le HTML natif ne suffit pas |
| Taille cible interactive | Minimum 24×24 CSS pixels                   |

---

## 4. Stratégie responsive — Desktop-first

### 4.1 Résolution écran vs viewport navigateur

Il est essentiel de distinguer deux concepts :

- **Résolution physique de l'écran** : le nombre réel de pixels matériels. L'écran de référence pour ce design system est un **2560 × 1440** (QHD / 1440p).
- **Viewport CSS du navigateur** : la surface utilisable par le contenu web, exprimée en **pixels CSS** (aussi appelés pixels logiques). Elle dépend du facteur de mise à l'échelle du système d'exploitation (DPR — Device Pixel Ratio) et de l'espace occupé par le chrome du navigateur (barre d'onglets, barre de favoris, barre d'adresse, scrollbar système, etc.).

**Calcul pour l'écran de référence (2560 × 1440) :**

| Scaling OS | DPR  | Viewport CSS largeur | Viewport CSS hauteur (estimée) |
| ---------- | ---- | -------------------- | ------------------------------ |
| 100%       | 1.0  | ~2560px              | ~1320px                        |
| 125%       | 1.25 | ~2048px              | ~1056px                        |
| 150%       | 1.5  | ~1707px              | ~880px                         |

Les hauteurs sont estimées en soustrayant environ 120px CSS pour le chrome du navigateur (barre d'onglets + barre d'adresse + barre de favoris). Cette valeur varie selon la configuration du navigateur.

**Règle** : le design system doit être testé et fonctionner correctement pour un viewport CSS allant de **1707px à 2560px de large**, couvrant les scaling 100% à 150% sur l'écran de référence. Le viewport de design principal est fixé à **2048 × 1056 CSS pixels** (scaling 125%, cas le plus courant sur un écran QHD).

### 4.2 Approche

Le CSS est écrit pour le plus grand écran d'abord. Les adaptations pour écrans plus petits sont appliquées via `max-width` media queries, du plus grand au plus petit.

```css
/* Style par défaut = desktop large (viewport ≥ 1440px CSS) */
.layout {
  /* ... */
}

/* Adaptations descendantes */
@media (max-width: 1439px) {
  /* desktop standard */
}
@media (max-width: 1279px) {
  /* desktop compact */
}
@media (max-width: 1023px) {
  /* tablette */
}
@media (max-width: 767px) {
  /* mobile */
}
```

### 4.3 Breakpoints

| Nom              | Plage CSS       | Variable CSS                   | Cas d'usage principal                     |
| ---------------- | --------------- | ------------------------------ | ----------------------------------------- |
| Desktop large    | ≥ 1440px        | —                              | Vue par défaut, dashboards complets       |
| Desktop standard | 1280px – 1439px | `--bp-desktop: 1439px`         | Desktop sans écran large ou scaling élevé |
| Desktop compact  | 1024px – 1279px | `--bp-desktop-compact: 1279px` | Fenêtre réduite, écran plus petit         |
| Tablette         | 768px – 1023px  | `--bp-tablet: 1023px`          | Support minimal, layout simplifié         |
| Mobile           | < 768px         | `--bp-mobile: 767px`           | Usage exceptionnel, support basique       |

**Priorité d'effort** : le design est optimisé pour Desktop large et Desktop standard. Desktop compact est un compromis acceptable. Tablette et Mobile sont des filets de sécurité, pas des cibles de conception prioritaires.

### 4.4 Viewports de référence pour le design et les tests

| Contexte              | Largeur CSS | Hauteur CSS | Correspondance                       |
| --------------------- | ----------- | ----------- | ------------------------------------ |
| **Design principal**  | 2048px      | 1056px      | Écran 2560×1440 @ scaling 125%       |
| Test desktop standard | 1440px      | 900px       | Écran 1080p ou scaling élevé sur QHD |
| Test minimum          | 1024px      | 768px       | Fenêtre réduite / écran compact      |

---

## 5. Structure du design system

Le design system est organisé en documents Markdown, chacun couvrant une couche du système. Voici l'architecture prévue :

```
design-system/
├── 00-fondations.md          ← CE DOCUMENT
├── 01-design-tokens.md       ← Couleurs, typographie, espacements, ombres, rayons
├── 02-layout.md              ← Grille, conteneurs, mise en page
├── 03-composants.md          ← Bibliothèque de composants (boutons, inputs, tables...)
├── 04-patterns.md            ← Patterns UI récurrents (formulaires, navigation, modales...)
├── 05-etats-feedback.md      ← États (loading, erreur, vide, succès), notifications
├── 06-iconographie.md        ← Système d'icônes, conventions
├── 07-regles-code.md         ← Conventions CSS, nommage, structure de fichiers
└── 08-checklist.md           ← Checklist de conformité pour valider une implémentation
```

### Ordre de lecture recommandé

1. **00-fondations** (ce document) : comprendre le cadre.
2. **01-design-tokens** : les valeurs atomiques du système.
3. **02-layout** : comment structurer une page.
4. **07-regles-code** : comment écrire le CSS/HTML/JS.
5. **03 à 06** : les composants et patterns, dans l'ordre du besoin.
6. **08-checklist** : valider avant livraison.

---

## 6. Principes de design

Ces principes guident toute décision non couverte explicitement par une règle :

1. **Fonction avant forme** : si un choix esthétique dégrade l'utilisabilité, l'utilisabilité gagne.
2. **Densité maîtrisée** : les applications backoffice affichent beaucoup d'information. Privilégier la densité lisible plutôt que les grands espaces vides.
3. **Prévisibilité** : un élément qui se ressemble doit se comporter de la même manière partout.
4. **Progressivité** : révéler la complexité progressivement (disclosure, accordéons, onglets).
5. **Feedback immédiat** : toute action utilisateur doit produire un retour visuel en < 100ms.
6. **Tolérance à l'erreur** : les actions destructives demandent confirmation. Les erreurs de saisie sont détectées tôt.
7. **Intentionnalité** : la conformité aux règles garantit la cohérence, pas la qualité perçue.
   Chaque écran doit faire l'objet d'une décision explicite sur trois points :
   - sa **hiérarchie visuelle** (qu'est-ce qui attire l'œil en premier, en deuxième ?)
   - sa **densité** (le niveau d'information affiché est-il justifié par le besoin ?)
   - son **interaction principale** (quelle est l'action centrale, et est-elle évidente sans effort ?)

   Un écran dont ces trois points n'ont pas été décidés explicitement est un écran générique.
   La conformité au design system est une condition nécessaire, pas suffisante.

---

## 7. Glossaire

| Terme                | Définition dans ce contexte                                                         |
| -------------------- | ----------------------------------------------------------------------------------- |
| Design token         | Valeur atomique (couleur, taille, espacement) stockée dans une variable CSS         |
| Composant            | Élément UI réutilisable avec markup, styles et comportement définis                 |
| Pattern              | Combinaison de composants résolvant un problème UX récurrent                        |
| Baseline             | Ensemble de fonctionnalités web supportées par tous les navigateurs majeurs         |
| Newly Available      | Fonctionnalité Baseline présente dans tous les navigateurs majeurs depuis < 30 mois |
| Widely Available     | Fonctionnalité Baseline présente dans tous les navigateurs majeurs depuis ≥ 30 mois |
| Limited Availability | Fonctionnalité non encore présente dans tous les navigateurs majeurs                |
| DPR                  | Device Pixel Ratio — rapport entre pixels physiques et pixels CSS                   |
| Viewport CSS         | Surface utilisable par le contenu web, en pixels logiques (CSS)                     |
| Chrome (navigateur)  | Navigateur web de Google, basé sur Chromium                                         |
| Chrome (UI)          | Éléments d'interface du navigateur (barres, onglets) qui réduisent le viewport      |
| Browserslist         | Outil déclaratif pour spécifier les navigateurs cibles d'un projet                  |
| Breakpoint           | Seuil de largeur d'écran déclenchant un changement de mise en page                  |
| Desktop-first        | Stratégie où le CSS par défaut cible les grands écrans                              |
| Fallback             | Solution de repli pour navigateurs ne supportant pas une fonctionnalité             |
| WCAG                 | Web Content Accessibility Guidelines (normes d'accessibilité W3C)                   |
| BCD                  | Browser Compat Data — jeu de données MDN sur la compatibilité navigateurs           |
| `web-features`       | Package npm fournissant les données Baseline de manière programmatique              |

---

## 8. Versioning de ce document

| Version | Date       | Changement                                                                   |
| ------- | ---------- | ---------------------------------------------------------------------------- |
| 0.1     | 2026-03-25 | Création initiale                                                            |
| 0.2     | 2026-03-25 | Viewport adapté à l'écran 2560×1440, Baseline Newly Available, outillage CLI |
| 0.3     | 2026-04-11 | Ajout section Limites                                                        |
| 0.4     | 2026-04-15 | Ajout principe 7 — Intentionnalité                                           |
