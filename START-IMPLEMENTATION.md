# START — Protocole de démarrage de session d'implémentation

> Ce fichier est le point d'entrée pour transformer une maquette finalisée en code de production conforme au Design System.
> Il complète `START.md` (qui couvre la phase de design/maquettage).
> Charger ce fichier en premier dans toute session d'implémentation.

---

## Différence avec le mode design

| Mode design (START.md)                  | Mode implémentation (ce fichier)            |
| --------------------------------------- | ------------------------------------------- |
| Produit une maquette HTML/CSS statique  | Produit du code de production structuré     |
| `data-js-*` posés comme marqueurs       | `data-js-*` câblés en JS                   |
| Données statiques dans le HTML          | Données injectées par la logique métier     |
| `07-regles-code.md` non chargé          | `07-regles-code.md` **obligatoire**         |
| `08-checklist.md` non chargé           | `08-checklist.md` **obligatoire**           |

---

## 1. Charger les artefacts de la maquette source

Lire les fichiers de l'écran à implémenter :

```
screens/[nom-ecran]/session.md       ← Phase courante, décisions, composants identifiés
screens/[nom-ecran]/brief.md         ← Besoin fonctionnel, données, actions
screens/[nom-ecran]/index.html       ← Maquette de référence visuelle
screens/[nom-ecran]/layout.css       ← Styles de mise en page de la maquette
screens/[nom-ecran]/components.css   ← Styles composants de la maquette
```

**Prérequis** : la maquette doit être en Phase 5 (Catalogue) — statut `finalisé` dans `manifest.md`. Ne pas implémenter une maquette non finalisée.

---

## 2. Charger les artefacts partagés

```
manifest.md        ← Version DS ciblée de cet écran
app-shells.md      ← Structure App Shell utilisée
catalog.md         ← Composants disponibles et leurs états
```

---

## 3. Charger le Design System en mode implémentation

Charger **tous** les fichiers DS, dans cet ordre :

| Ordre | Fichier                                          | Rôle en implémentation                      |
| ----- | ------------------------------------------------ | ------------------------------------------- |
| 1     | `design-system/references/00-fondations.md`      | Stack, compatibilité Baseline, accessibilité|
| 2     | `design-system/references/01-design-tokens.md`   | Tous les tokens à utiliser dans le code     |
| 3     | `design-system/references/02-layout.md`          | Structure CSS de l'App Shell et grilles     |
| 4     | `design-system/references/03-composants.md`      | CSS et HTML de référence de chaque composant|
| 5     | `design-system/references/04-patterns.md`        | Patterns à implémenter (modals, forms, etc.)|
| 6     | `design-system/references/05-etats-feedback.md`  | États loading/erreur/succès/vide            |
| 7     | `design-system/references/06-iconographie.md`    | Sprite SVG, tailles, accessibilité icônes   |
| 8     | `design-system/references/07-regles-code.md`     | **Conventions HTML/CSS/JS — obligatoire**   |
| 9     | `design-system/references/08-checklist.md`       | **Grille de validation — obligatoire**      |

---

## 4. Définir la structure de fichiers cible

L'implémentation produit cette structure (conforme à `07-regles-code.md §5`) :

```
[nom-ecran]/
├── index.html
├── css/
│   ├── tokens.css          ← Tous les tokens DS (copier depuis la maquette ou centraliser)
│   ├── reset.css           ← Reset navigateur
│   ├── base.css            ← Styles élémentaires
│   ├── layout.css          ← App Shell, grille
│   ├── utilities.css       ← Classes utilitaires
│   └── components/
│       ├── [composant].css ← Un fichier par composant
│       └── ...
├── js/
│   ├── app.js              ← Point d'entrée (type="module")
│   ├── components/
│   │   └── [composant].js  ← Logique de chaque composant interactif
│   └── services/
│       └── api.js          ← Appels API (à remplir avec la logique métier)
└── icons/
    └── sprite.svg          ← Sprite Lucide SVG
```

---

## 5. Rappel des contraintes d'implémentation

Ces règles s'appliquent **en plus** des contraintes de la maquette :

- **Tokens uniquement** : aucune valeur brute dans le CSS produit
- **BEM strict** : `.block__element`, variantes via `data-*`, jamais de `--modifier`
- **Focus double anneau** : voir `07-regles-code.md §3.5`
- **`data-js-*` câblés** : tous les marqueurs de la maquette sont maintenant sélecteurs JS réels
- **`addEventListener` uniquement** : pas d'attributs `onclick` inline
- **`<dialog>` natif** : toutes les modales et drawers utilisent `<dialog>` avec `.showModal()`
- **`contain: content`** sur tout composant autonome (sauf exceptions documentées)
- **`prefers-reduced-motion`** : toutes les animations sont désactivées si l'utilisateur le demande
- **Données dynamiques** : les données statiques de la maquette sont remplacées par les appels API — les `data-js-*` de la maquette sont les points d'injection

---

## 6. Processus de validation avant livraison

Avant de considérer l'implémentation comme terminée, passer **chaque item** de `08-checklist.md`. Aucun item ne doit être laissé non coché sans justification documentée.

```
npm run lint:css   ← Conformité CSS vs Baseline + syntaxe
npm run lint:js    ← Conformité JS API vs Baseline + syntaxe
```

---

## 7. Résumé de démarrage

Confirmer à l'utilisateur avant de commencer :

```
Contexte d'implémentation chargé :
- Écran : [nom]
- Version DS ciblée : [x.x] (depuis manifest.md)
- Phase maquette : finalisée
- Composants à implémenter : [liste depuis catalog.md]
- Fichiers JS à câbler (data-js-*) : [liste depuis index.html de la maquette]
- Points ouverts : [depuis session.md]
```
