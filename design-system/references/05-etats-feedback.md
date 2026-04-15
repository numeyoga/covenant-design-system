# Design System — 05 États et Feedback

> **Prérequis** : avoir lu [00-fondations.md](./00-fondations.md), [01-design-tokens.md](./01-design-tokens.md), [03-composants.md](./03-composants.md) et [04-patterns.md](./04-patterns.md).

Ce document définit comment l'interface communique son état à l'utilisateur : chargement, succès, erreur, état vide, données périmées, et actions en cours. Chaque état a une représentation visuelle définie et un comportement d'accessibilité associé.

---

## 1. Principes

1. **Feedback immédiat** : toute action utilisateur produit un retour visuel en < 100ms. Si l'opération prend plus de temps, un indicateur de chargement apparaît.
2. **Pas de silence** : l'interface n'est jamais dans un état ambigu. L'utilisateur sait toujours si quelque chose est en cours, a réussi ou a échoué.
3. **Hiérarchie de feedback** : le canal utilisé est proportionnel à l'importance du message (voir section 2).
4. **Récupération** : tout état d'erreur propose un chemin de résolution (réessayer, corriger, contacter le support).

---

## 2. Canaux de feedback

Le design system utilise 4 canaux de feedback, classés par niveau d'interruption :

| Canal              | Interruption | Durée      | Usage                                              | Implémentation                                 |
| ------------------ | ------------ | ---------- | -------------------------------------------------- | ---------------------------------------------- |
| **Inline**         | Aucune       | Persistant | Validation de champ, statut d'une ligne de tableau | Texte, icône ou badge directement dans le flux |
| **Banner / Alert** | Faible       | Persistant | Message important sur toute la page ou une section | Composant `.alert` (voir 03-composants §11)    |
| **Toast**          | Moyenne      | Temporaire | Confirmation d'action, erreur non bloquante        | Pattern Toast (voir 04-patterns §8)            |
| **Modal**          | Forte        | Bloquant   | Confirmation destructive, erreur critique          | Pattern Modal (voir 04-patterns §6)            |

**Règle de choix** : utiliser le canal avec le **niveau d'interruption le plus bas** capable de transmettre le message.

---

## 3. États de chargement

### 3.1 Chargement de page entière

Affiché quand le contenu principal de la page n'est pas encore disponible.

```html
<div class="loading-page" role="status" aria-label="Chargement en cours">
  <div class="spinner" aria-hidden="true"></div>
  <span class="loading-page__text">Chargement…</span>
</div>
```

```css
.loading-page {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: var(--gap-md);

  height: 100%;
  min-height: 16rem;

  color: var(--color-text-muted);
  font-size: var(--text-sm);
}
```

### 3.2 Spinner

```css
.spinner {
  width: 1.5rem;
  height: 1.5rem;

  border: var(--border-width-thick) solid var(--color-border-subtle);
  border-top-color: var(--color-primary);
  border-radius: var(--radius-full);

  animation: spin var(--duration-slower) linear infinite;
}

.spinner[data-size="sm"] {
  width: 1rem;
  height: 1rem;
}

.spinner[data-size="lg"] {
  width: 2.5rem;
  height: 2.5rem;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}
```

### 3.3 Chargement de section / composant

Pour un composant qui charge ses données indépendamment (une carte, un widget de dashboard) :

```html
<article class="card">
  <header class="card__header">
    <h3 class="card__title">Activité récente</h3>
  </header>
  <div class="card__body">
    <div
      class="loading-section"
      role="status"
      aria-label="Chargement de l'activité"
    >
      <div class="spinner" data-size="sm" aria-hidden="true"></div>
    </div>
  </div>
</article>
```

```css
.loading-section {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: var(--space-8);
}
```

### 3.4 Skeleton loading

Pour les vues où la structure finale est prévisible (listes, tableaux, cartes), le skeleton loading donne un aperçu de la forme du contenu pendant le chargement.

```html
<div class="skeleton-group" role="status" aria-label="Chargement en cours">
  <div class="skeleton skeleton--text" style="--skeleton-width: 60%;"></div>
  <div class="skeleton skeleton--text" style="--skeleton-width: 80%;"></div>
  <div class="skeleton skeleton--text" style="--skeleton-width: 40%;"></div>
  <span class="sr-only">Chargement en cours</span>
</div>
```

```css
.skeleton {
  background-color: var(--color-bg-muted);
  border-radius: var(--radius-sm);
  animation: skeleton-pulse 1.5s ease-in-out infinite;
}

.skeleton--text {
  height: 0.875rem;
  width: var(--skeleton-width, 100%);
}

.skeleton--circle {
  width: 2rem;
  height: 2rem;
  border-radius: var(--radius-full);
}

.skeleton--rect {
  height: var(--skeleton-height, 4rem);
  width: var(--skeleton-width, 100%);
}

.skeleton-group {
  display: flex;
  flex-direction: column;
  gap: var(--gap-sm);
}

@keyframes skeleton-pulse {
  0%,
  100% {
    opacity: 1;
  }
  50% {
    opacity: 0.4;
  }
}
```

### 3.5 Chargement sur un bouton

Un bouton qui déclenche une opération asynchrone affiche un état "en cours" :

```html
<button
  class="btn"
  data-variant="primary"
  type="button"
  aria-disabled="true"
  data-loading
>
  <div class="spinner" data-size="sm" aria-hidden="true"></div>
  <span class="btn__label">Enregistrement…</span>
</button>
```

```css
.btn[data-loading] {
  pointer-events: none;
  opacity: 0.8;
}
```

**Règles du bouton loading** :

- Le bouton porte `aria-disabled="true"` (pas `disabled`, pour rester focusable).
- L'attribut `data-loading` est ajouté/retiré par le JS.
- Le label change pour refléter l'action en cours ("Enregistrement…" au lieu de "Enregistrer").
- Le spinner remplace l'icône le cas échéant.

### 3.6 Quand utiliser quel type de chargement

| Durée estimée | Situation                                                  | Type de chargement                                                           |
| ------------- | ---------------------------------------------------------- | ---------------------------------------------------------------------------- |
| < 1 s         | Toute situation                                            | Aucun indicateur — un flash de skeleton serait plus perturbant que l'attente |
| 1–5 s         | Page entière non chargée                                   | Loading page (spinner centré + texte)                                        |
| 1–5 s         | Section/widget indépendant dont la structure est connue    | Skeleton loading                                                             |
| 1–5 s         | Section/widget dont la structure est inconnue              | Loading section (spinner)                                                    |
| 1–10 s        | Action utilisateur déclenchée par un bouton                | Bouton loading (`data-loading`)                                              |
| > 10 s        | Upload, import, traitement par lot                         | Barre de progression avec pourcentage                                        |
| Indéfini      | Chargement de données supplémentaires (pagination, scroll) | Spinner inline en bas de liste                                               |

---

## 4. États de succès

### 4.1 Feedback après action

| Action                   | Canal de feedback | Message type                          |
| ------------------------ | ----------------- | ------------------------------------- |
| Création d'une entité    | Toast success     | "[Entité] créé(e) avec succès"        |
| Modification sauvegardée | Toast success     | "Modifications enregistrées"          |
| Suppression effectuée    | Toast success     | "[Entité] supprimé(e)"                |
| Export terminé           | Toast info        | "Export terminé — fichier téléchargé" |
| Action en masse          | Toast success     | "[N] éléments mis à jour"             |

### 4.2 Indicateur de sauvegarde automatique

Pour les formulaires avec sauvegarde automatique (auto-save) :

```html
<span class="save-indicator" role="status">
  <svg class="save-indicator__icon" aria-hidden="true">...</svg>
  <span class="save-indicator__text">Enregistré</span>
</span>
```

```css
.save-indicator {
  display: inline-flex;
  align-items: center;
  gap: var(--gap-xs);
  font-size: var(--text-xs);
  color: var(--color-text-muted);
}

.save-indicator__icon {
  width: 0.875rem;
  height: 0.875rem;
  color: var(--color-success);
}

.save-indicator[data-state="saving"] .save-indicator__text::after {
  content: "…";
  animation: ellipsis 1s steps(3) infinite;
}

.save-indicator[data-state="saving"] .save-indicator__icon {
  color: var(--color-text-placeholder);
}

.save-indicator[data-state="error"] {
  color: var(--color-danger-text);
}

.save-indicator[data-state="error"] .save-indicator__icon {
  color: var(--color-danger);
}
```

Les trois états : `data-state="saving"` (en cours), absent (enregistré), `data-state="error"` (échec).

### 4.3 Toast avec action d'annulation (Undo)

Pour les actions de faible gravité (archivage, retrait d'un tag), le toast de succès inclut un bouton "Annuler" avec une fenêtre de 5–10 secondes avant exécution définitive. Ce pattern évite une modale de confirmation pour les actions facilement réversibles.

```html
<div class="toast" data-variant="success" role="status">
  <svg class="toast__icon" aria-hidden="true"><!-- icon-check --></svg>
  <span class="toast__message">Élément archivé.</span>
  <button
    class="btn"
    data-variant="ghost"
    data-size="sm"
    type="button"
    data-js-toast-undo
  >
    Annuler
  </button>
  <button
    class="btn"
    data-variant="ghost"
    data-size="sm"
    aria-label="Fermer"
    data-js-toast-close
  >
    <svg class="btn__icon" aria-hidden="true"><!-- icon-x --></svg>
  </button>
</div>
```

**Règle** : le bouton "Annuler" est absent des toasts informatifs et des toasts d'erreur. Il n'est pertinent que sur les toasts de succès confirmant une action réversible.

---

## 5. États d'erreur

### 5.1 Hiérarchie des erreurs

| Type d'erreur                | Gravité  | Canal          | Action attendue                           |
| ---------------------------- | -------- | -------------- | ----------------------------------------- |
| Validation d'un champ        | Faible   | Inline         | Corriger le champ                         |
| Validation du formulaire     | Faible   | Inline + Alert | Corriger les champs signalés              |
| Erreur réseau (temporaire)   | Moyenne  | Toast danger   | Réessayer automatiquement ou manuellement |
| Erreur serveur (4xx)         | Moyenne  | Alert danger   | Modifier la requête ou contacter support  |
| Erreur serveur (5xx)         | Haute    | Alert danger   | Réessayer ou contacter support            |
| Erreur critique (app cassée) | Critique | Page d'erreur  | Recharger la page                         |
| Ressource introuvable (404)  | Haute    | Page d'erreur  | Retourner à l'accueil                     |

Pour les actions destructives déclenchées par l'utilisateur, appliquer les niveaux de confirmation définis dans `04-patterns.md §6.5`.

### 5.2 Erreur inline (validation de champ)

Défini dans [03-composants.md §3.5](./03-composants.md). Pour rappel :

```html
<input class="input" aria-invalid="true" aria-describedby="email-error" />
<span class="form-field__error" id="email-error" role="alert">
  L'adresse email est invalide.
</span>
```

**Règles** :

- Le message d'erreur est spécifique : "L'adresse email est invalide" plutôt que "Champ invalide".
- Le champ porte `aria-invalid="true"`.
- Le message porte `role="alert"`.
- Au submit, le focus est placé sur le **premier** champ invalide.

### 5.3 Erreur de formulaire (résumé)

Quand un formulaire comporte plusieurs erreurs, un résumé est affiché en haut du formulaire en plus des erreurs inline :

```html
<div class="alert" data-variant="danger" role="alert">
  <svg class="alert__icon" aria-hidden="true">...</svg>
  <div class="alert__content">
    <strong class="alert__title">Le formulaire contient 3 erreurs</strong>
    <ul class="alert__list">
      <li><a href="#f-email">Adresse email : format invalide</a></li>
      <li><a href="#f-lastname">Nom : ce champ est requis</a></li>
      <li><a href="#f-role">Rôle : veuillez sélectionner une valeur</a></li>
    </ul>
  </div>
</div>
```

```css
.alert__list {
  margin-top: var(--space-2);
  font-size: var(--text-sm);
}

.alert__list li {
  margin-top: var(--space-1);
}

.alert__list a {
  text-decoration: underline;
  color: inherit;
}

.alert__list a:hover {
  color: var(--color-text-default);
}
```

Les liens dans le résumé pointent vers les champs en erreur via leur `id`, permettant une navigation directe.

### 5.4 Page d'erreur (erreur critique / 404 / 500)

```html
<div class="error-page">
  <div class="error-page__content">
    <span class="error-page__code">404</span>
    <h1 class="error-page__title">Page introuvable</h1>
    <p class="error-page__description">
      La page que vous recherchez n'existe pas ou a été déplacée.
    </p>
    <div class="error-page__actions">
      <a class="btn" data-variant="primary" href="/dashboard">
        Retour au tableau de bord
      </a>
    </div>
  </div>
</div>
```

```css
.error-page {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
  min-height: 24rem;
  text-align: center;
}

.error-page__content {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: var(--gap-md);
  max-width: 28rem;
}

.error-page__code {
  font-size: var(--text-3xl);
  font-weight: var(--font-weight-bold);
  color: var(--color-text-placeholder);
  line-height: 1;
}

.error-page__title {
  font-size: var(--text-2xl);
  font-weight: var(--font-weight-bold);
  color: var(--color-text-default);
  line-height: var(--leading-tight);
}

.error-page__description {
  font-size: var(--text-base);
  color: var(--color-text-muted);
}

.error-page__actions {
  margin-top: var(--space-4);
}
```

**Pages d'erreur courantes** :

| Code | Titre            | Description                                          | Action                        |
| ---- | ---------------- | ---------------------------------------------------- | ----------------------------- |
| 404  | Page introuvable | La page n'existe pas ou a été déplacée               | Retour au tableau de bord     |
| 403  | Accès refusé     | Vous n'avez pas les droits pour accéder à cette page | Retour / Contacter admin      |
| 500  | Erreur serveur   | Une erreur inattendue s'est produite                 | Réessayer / Contacter support |

### 5.5 Erreur réseau / retry

Pour les opérations qui échouent à cause du réseau :

```js
// js/utils/retry.js
export async function fetchWithRetry(url, options = {}, maxRetries = 3) {
  let lastError;

  for (let attempt = 0; attempt < maxRetries; attempt++) {
    try {
      const response = await fetch(url, options);
      if (!response.ok) throw new Error(`HTTP ${response.status}`);
      return response;
    } catch (error) {
      lastError = error;
      if (attempt < maxRetries - 1) {
        const delay = Math.min(1000 * Math.pow(2, attempt), 10000);
        await new Promise((resolve) => setTimeout(resolve, delay));
      }
    }
  }

  throw lastError;
}
```

**Règle** : le retry utilise un backoff exponentiel (1s, 2s, 4s) plafonné à 10s. Après le dernier échec, un toast `danger` est affiché avec un bouton "Réessayer".

---

## 6. État vide

Documenté en détail dans [04-patterns.md §9](./04-patterns.md). Récapitulatif des variantes :

| Situation                            | Icône        | Titre                           | Action                |
| ------------------------------------ | ------------ | ------------------------------- | --------------------- |
| Première utilisation (aucune donnée) | Illustrative | "Aucun [entité]"                | Bouton Créer          |
| Recherche sans résultat              | Recherche    | "Aucun résultat"                | Modifier les filtres  |
| Filtre trop restrictif               | Filtre       | "Aucun résultat pour ce filtre" | Réinitialiser filtres |
| Données supprimées                   | Corbeille    | "Élément supprimé"              | Annuler (si possible) |

**Règles** :

- L'état vide remplace entièrement le contenu (pas de tableau vide avec 0 lignes).
- Le message indique toujours **pourquoi** c'est vide et **quoi faire**.
- Si c'est lié à un filtre/recherche, proposer un lien "Réinitialiser les filtres".

---

## 7. État désactivé

### 7.1 Composants désactivés

```css
/* Style commun à tous les éléments désactivés */
:disabled,
[aria-disabled="true"] {
  opacity: 0.5;
  cursor: not-allowed;
}
```

**Règles** :

- Un élément désactivé a une opacité réduite à 0.5.
- Utiliser `disabled` pour les contrôles de formulaire natifs (`input`, `button`, `select`).
- Utiliser `aria-disabled="true"` pour les éléments custom ou quand l'élément doit rester focusable (ex: un bouton dans un tooltip qui explique pourquoi il est désactivé).
- Un élément désactivé **devrait** avoir une explication accessible de pourquoi il est désactivé (tooltip ou texte adjacent).
- Ne **jamais** désactiver un lien (`<a>`). Utiliser `aria-disabled="true"` et intercepter le clic en JS si nécessaire.

### 7.2 Sections désactivées

Pour une section entière (un formulaire, un panneau) qui est temporairement non interactive :

```html
<fieldset disabled>
  <legend class="form-field__label">Paramètres avancés</legend>
  <!-- Les inputs à l'intérieur sont automatiquement désactivés -->
</fieldset>
```

Le `<fieldset disabled>` est le mécanisme natif HTML pour désactiver un groupe de contrôles. Il est préféré à l'ajout manuel de `disabled` sur chaque champ.

---

## 8. États de données

### 8.1 Données périmées / en cours de rafraîchissement

Quand des données affichées pourraient ne plus être à jour (rafraîchissement en cours, connexion perdue temporairement) :

```html
<div class="data-table-wrapper" data-stale>
  <!-- Le tableau affiche les dernières données connues avec une opacité réduite -->
  <table class="data-table">
    ...
  </table>
</div>
```

```css
[data-stale] {
  opacity: 0.6;
  pointer-events: none;
  position: relative;
}

[data-stale]::after {
  content: "";
  position: absolute;
  top: var(--space-2);
  right: var(--space-2);
  width: 1rem;
  height: 1rem;
  border: var(--border-width-thick) solid var(--color-border-subtle);
  border-top-color: var(--color-primary);
  border-radius: var(--radius-full);
  animation: spin var(--duration-slower) linear infinite;
}
```

### 8.2 Données partiellement chargées (pagination / scroll infini)

Un indicateur est affiché en bas de la liste pendant le chargement des éléments suivants :

```html
<div
  class="load-more"
  role="status"
  aria-label="Chargement des éléments suivants"
>
  <div class="spinner" data-size="sm" aria-hidden="true"></div>
  <span class="load-more__text">Chargement…</span>
</div>
```

```css
.load-more {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: var(--gap-sm);
  padding: var(--space-6);

  font-size: var(--text-sm);
  color: var(--color-text-muted);
}
```

---

## 9. Animations et transitions de feedback

### 9.0 Philosophie

Les animations de feedback obéissent à deux principes complémentaires :

**Proportionnalité.** L'animation est au service du feedback, pas de l'esthétique.
Une transition sert à signaler un changement d'état, guider l'attention, ou confirmer une action.
Elle ne sert pas à rendre l'interface "vivante".

**Concentration plutôt que dispersion.** Un seul moment animé bien exécuté — par exemple
l'apparition coordonnée des éléments d'un écran au premier chargement — produit une meilleure
qualité perçue que des micro-interactions animées sur chaque composant pris isolément.
Avant d'ajouter une animation, poser la question : est-ce le bon endroit pour concentrer
l'attention, ou est-ce une animation de plus qui dilue l'ensemble ?

**Règle opérationnelle** : ne définir qu'un seul "moment fort" par écran.
Tous les autres changements d'état sont des transitions discrètes via les tokens `--duration-fast`
ou `--duration-normal`. Si un écran possède plusieurs animations remarquables, c'est un signal
de sur-animation à corriger.

### 9.1 Transitions d'état

Les changements d'état visuels sont animés avec les tokens de transition :

| Transition                 | Durée               | Easing           |
| -------------------------- | ------------------- | ---------------- |
| Hover (fond, bordure)      | `--duration-fast`   | `--ease-default` |
| Focus ring                 | `--duration-fast`   | `--ease-default` |
| Ouverture dropdown/popover | `--duration-normal` | `--ease-out`     |
| Ouverture modale           | `--duration-normal` | `--ease-out`     |
| Ouverture drawer           | `--duration-slow`   | `--ease-out`     |
| Fermeture (tous)           | `--duration-fast`   | `--ease-in`      |
| Toast apparition           | `--duration-normal` | `--ease-out`     |
| Skeleton pulse             | 1.5s                | `ease-in-out`    |
| Spinner rotation           | `--duration-slower` | `linear`         |

### 9.2 Respect de `prefers-reduced-motion`

Défini dans [01-design-tokens.md §8.3](./01-design-tokens.md). Pour rappel, toutes les animations et transitions sont supprimées quand l'utilisateur a activé le mode "réduire les animations". Les changements d'état se produisent instantanément.

---

## 10. Récapitulatif — Arbre de décision du feedback

```
Action utilisateur
 │
 ├─ Résultat immédiat (< 100ms)
 │   └─ Transition visuelle (hover, focus, toggle) → CSS transition
 │
 ├─ Résultat rapide (100ms – 1s)
 │   └─ Afficher le résultat directement → mise à jour du DOM
 │
 ├─ Résultat lent (> 1s)
 │   ├─ Action sur un bouton → Bouton loading
 │   ├─ Chargement de section → Spinner ou Skeleton
 │   └─ Chargement de page → Loading page
 │
 ├─ Succès
 │   ├─ Création / Modification / Suppression → Toast success
 │   └─ Sauvegarde auto → Indicateur inline
 │
 └─ Erreur
     ├─ Champ invalide → Erreur inline + focus
     ├─ Formulaire invalide → Résumé d'erreurs + erreurs inline
     ├─ Erreur réseau → Toast danger + retry
     ├─ Erreur serveur → Alert danger
     └─ Erreur critique → Page d'erreur
```

---

## 11. Versioning de ce document

| Version | Date       | Changement                              |
| ------- | ---------- | --------------------------------------- |
| 0.1     | 2026-03-25 | Création initiale                       |
| 0.2     | 2026-04-15 | Ajout §9.0 — Philosophie des animations |
