# Design System — 04 Patterns

> **Prérequis** : avoir lu [00-fondations.md](./00-fondations.md), [01-design-tokens.md](./01-design-tokens.md), [02-layout.md](./02-layout.md), [03-composants.md](./03-composants.md) et [07-regles-code.md](./07-regles-code.md).

Un pattern est une **combinaison de composants et de règles de layout** qui résout un problème UX récurrent. Là où un composant est une brique isolée (un bouton, un input), un pattern décrit comment assembler ces briques pour répondre à un besoin utilisateur concret (un formulaire de création, une recherche filtrée, une confirmation de suppression).

---

## 1. Principes des patterns

1. **Composabilité** : un pattern assemble des composants existants. Il n'introduit pas de nouveaux éléments visuels — seulement de nouvelles combinaisons.
2. **Prévisibilité** : le même problème UX est résolu de la même manière dans toutes les applications.
3. **Autonomie** : chaque pattern est documenté avec son markup complet, prêt à copier-coller. Il ne repose sur aucun état global implicite.

---

## 2. Page Header

### 2.1 Rôle

Bandeau en haut de chaque vue du Main Content. Il identifie la page, fournit le contexte de navigation (breadcrumb) et regroupe les actions principales.

### 2.2 Structure

```
┌────────────────────────────────────────────────────────────────┐
│  [Breadcrumb]                                                  │
│  Titre de page                         [Action 2] [Action 1]  │
│  Description optionnelle                                       │
└────────────────────────────────────────────────────────────────┘
```

### 2.3 Markup

```html
<div class="page-header">
  <div class="page-header__start">
    <nav class="breadcrumb" aria-label="Fil d'Ariane">
      <ol class="breadcrumb__list">
        <li class="breadcrumb__item">
          <a class="breadcrumb__link" href="/dashboard">Tableau de bord</a>
        </li>
        <li class="breadcrumb__item">
          <a class="breadcrumb__link" href="/users">Utilisateurs</a>
        </li>
        <li class="breadcrumb__item" aria-current="page">
          <span class="breadcrumb__current">Alice Martin</span>
        </li>
      </ol>
    </nav>
    <h1 class="page-header__title">Alice Martin</h1>
    <p class="page-header__description">Compte créé le 14 janvier 2025</p>
  </div>
  <div class="page-header__end">
    <button class="btn" data-variant="secondary" type="button">Exporter</button>
    <button class="btn" data-variant="primary" type="button">Modifier</button>
  </div>
</div>
```

### 2.4 CSS

```css
.page-header {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: var(--gap-md);
  flex-wrap: wrap;
}

.page-header__start {
  display: flex;
  flex-direction: column;
  gap: var(--space-1);
  min-width: 0;
}

.page-header__title {
  font-size: var(--text-2xl);
  font-weight: var(--font-weight-bold);
  color: var(--color-text-default);
  line-height: var(--leading-tight);
}

.page-header__description {
  font-size: var(--text-sm);
  color: var(--color-text-muted);
}

.page-header__end {
  display: flex;
  align-items: center;
  gap: var(--gap-sm);
  flex-shrink: 0;
}

/* --- Breadcrumb --- */
.breadcrumb__list {
  display: flex;
  align-items: center;
  gap: var(--gap-xs);
  font-size: var(--text-sm);
}

.breadcrumb__item:not(:last-child)::after {
  content: "/";
  margin-left: var(--space-1);
  color: var(--color-text-placeholder);
}

.breadcrumb__link {
  color: var(--color-text-muted);
  transition: color var(--duration-fast) var(--ease-default);
}

.breadcrumb__link:hover {
  color: var(--color-primary);
}

.breadcrumb__current {
  color: var(--color-text-default);
  font-weight: var(--font-weight-medium);
}
```

### 2.5 Règles

- Le breadcrumb est optionnel sur les pages de premier niveau (dashboard).
- La description est optionnelle.
- Le bouton `primary` (s'il existe) est toujours **à droite** dans le groupe d'actions.
- Sur desktop compact (< 1280px), les actions passent sous le titre via `flex-wrap`.

---

## 3. Toolbar (barre d'outils)

### 3.1 Rôle

Barre horizontale placée au-dessus d'un tableau ou d'une liste. Elle regroupe les actions contextuelles : recherche, filtres, actions groupées, pagination.

### 3.2 Structure

```
┌──────────────────────────────────────────────────────────────┐
│  [🔍 Recherche       ]  [Filtre ▾] [Filtre ▾]  │  [Actions] │
└──────────────────────────────────────────────────────────────┘
```

### 3.3 Markup

```html
<div class="toolbar" role="toolbar" aria-label="Actions sur la liste">
  <div class="toolbar__start">
    <div class="toolbar__search">
      <svg class="toolbar__search-icon" aria-hidden="true">...</svg>
      <input class="input" data-size="sm" type="search"
             placeholder="Rechercher..." aria-label="Rechercher dans la liste"
             data-js-toolbar-search>
    </div>
    <div class="toolbar__filters">
      <div class="dropdown" data-js-dropdown>
        <button class="btn" data-variant="secondary" data-size="sm"
                aria-expanded="false" aria-haspopup="true"
                data-js-dropdown-trigger>
          Statut
          <svg class="btn__icon" aria-hidden="true">...</svg>
        </button>
        <div class="dropdown__menu" role="menu" hidden>
          <button class="dropdown__item" role="menuitem">Actif</button>
          <button class="dropdown__item" role="menuitem">Inactif</button>
          <button class="dropdown__item" role="menuitem">Tous</button>
        </div>
      </div>
    </div>
  </div>
  <div class="toolbar__end">
    <button class="btn" data-variant="secondary" data-size="sm" type="button">
      <svg class="btn__icon" aria-hidden="true">...</svg>
      <span class="btn__label">Exporter</span>
    </button>
    <button class="btn" data-variant="primary" data-size="sm" type="button">
      <svg class="btn__icon" aria-hidden="true">...</svg>
      <span class="btn__label">Ajouter</span>
    </button>
  </div>
</div>
```

### 3.4 CSS

```css
.toolbar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: var(--gap-md);
  flex-wrap: wrap;
  padding: var(--padding-sm) 0;
}

.toolbar__start {
  display: flex;
  align-items: center;
  gap: var(--gap-sm);
  flex: 1;
  min-width: 0;
}

.toolbar__search {
  position: relative;
  max-width: 20rem;
  flex: 1;
}

.toolbar__search-icon {
  position: absolute;
  left: var(--space-2);
  top: 50%;
  transform: translateY(-50%);
  width: 1rem;
  height: 1rem;
  color: var(--color-text-placeholder);
  pointer-events: none;
}

.toolbar__search .input {
  padding-left: var(--space-8);
}

.toolbar__filters {
  display: flex;
  align-items: center;
  gap: var(--gap-xs);
}

.toolbar__end {
  display: flex;
  align-items: center;
  gap: var(--gap-sm);
  flex-shrink: 0;
}
```

### 3.5 Règles

- La toolbar porte `role="toolbar"` et `aria-label`.
- Le champ de recherche filtre la liste de manière réactive (debounce de 300ms recommandé).
- Les filtres actifs doivent être visuellement distingués (par ex. `data-variant="primary"` sur le bouton du filtre actif ou un badge de comptage).

---

## 4. Formulaire (création / édition)

### 4.1 Rôle

Pattern pour les formulaires de création ou d'édition d'une entité. Utilise le layout formulaire défini dans [02-layout.md §5.4](./02-layout.md).

### 4.2 Structure

```
┌─────────────────────────────────────────────┐
│  Page Header                    [Annuler] [Enregistrer]
├─────────────────────────────────────────────┤
│                                             │
│  ┌─ Section : Informations générales ─────┐ │
│  │  [Nom         ] [Prénom       ]        │ │
│  │  [Email                       ]        │ │
│  └────────────────────────────────────────┘ │
│                                             │
│  ┌─ Section : Paramètres ─────────────────┐ │
│  │  [Rôle ▾      ] [Statut ▾     ]       │ │
│  │  [□ Activer les notifications  ]       │ │
│  └────────────────────────────────────────┘ │
│                                             │
│                    [Annuler] [Enregistrer]   │
└─────────────────────────────────────────────┘
```

### 4.3 Markup

```html
<div class="page">
  <div class="page-header">
    <div class="page-header__start">
      <nav class="breadcrumb" aria-label="Fil d'Ariane">...</nav>
      <h1 class="page-header__title">Créer un utilisateur</h1>
    </div>
    <div class="page-header__end">
      <button class="btn" data-variant="secondary" type="button" data-js-action="cancel">Annuler</button>
      <button class="btn" data-variant="primary" type="submit" form="user-form">Enregistrer</button>
    </div>
  </div>

  <form id="user-form" class="form-layout" novalidate data-js-form>
    <!-- Section 1 -->
    <div class="form-layout__section">
      <h2 class="form-section__title">Informations générales</h2>
    </div>

    <div class="form-field">
      <label class="form-field__label" for="f-lastname">Nom</label>
      <input class="input" type="text" id="f-lastname" name="lastname" required>
    </div>

    <div class="form-field">
      <label class="form-field__label" for="f-firstname">Prénom</label>
      <input class="input" type="text" id="f-firstname" name="firstname" required>
    </div>

    <div class="form-field form-layout__field--full">
      <label class="form-field__label" for="f-email">Email</label>
      <input class="input" type="email" id="f-email" name="email" required>
    </div>

    <!-- Section 2 -->
    <div class="form-layout__section">
      <h2 class="form-section__title">Paramètres</h2>
    </div>

    <div class="form-field">
      <label class="form-field__label" for="f-role">Rôle</label>
      <div class="select-wrapper">
        <select class="select" id="f-role" name="role">
          <option value="user">Utilisateur</option>
          <option value="admin">Administrateur</option>
        </select>
      </div>
    </div>

    <div class="form-field">
      <label class="form-field__label" for="f-status">Statut</label>
      <div class="select-wrapper">
        <select class="select" id="f-status" name="status">
          <option value="active">Actif</option>
          <option value="inactive">Inactif</option>
        </select>
      </div>
    </div>

    <div class="form-field form-layout__field--full">
      <label class="checkbox">
        <input class="checkbox__input" type="checkbox" name="notifications" value="1">
        <span class="checkbox__label">Activer les notifications par email</span>
      </label>
    </div>

    <!-- Actions de bas de formulaire -->
    <div class="form-layout__actions">
      <button class="btn" data-variant="secondary" type="button" data-js-action="cancel">Annuler</button>
      <button class="btn" data-variant="primary" type="submit">Enregistrer</button>
    </div>
  </form>
</div>
```

### 4.4 CSS complémentaire

```css
.form-section__title {
  font-size: var(--text-lg);
  font-weight: var(--font-weight-semibold);
  color: var(--color-text-default);
  line-height: var(--leading-tight);
}
```

### 4.5 Règles

- Les actions apparaissent en **haut** (Page Header) **et** en **bas** du formulaire. Les deux jeux d'actions sont synchronisés.
- L'attribut `novalidate` est présent sur le `<form>`. La validation est gérée en JavaScript avec des messages d'erreur explicites (pas de bulles navigateur natives).
- L'attribut `form="user-form"` sur le bouton submit du Page Header l'associe au formulaire sans qu'il soit à l'intérieur du `<form>`.
- Les champs sont groupés en sections logiques séparées par `.form-layout__section`.
- Un champ qui occupe toute la largeur porte `.form-layout__field--full`.
- Le bouton Annuler est `secondary`, le bouton Enregistrer est `primary`.

### 4.6 Validation côté client

```js
// js/patterns/form-validation.js
export function initFormValidation(form) {
  form.addEventListener('submit', (e) => {
    e.preventDefault();
    clearErrors(form);

    const fields = form.querySelectorAll('[required], [pattern], [type="email"]');
    let firstInvalid = null;

    fields.forEach(field => {
      if (!field.validity.valid) {
        showFieldError(field, getErrorMessage(field));
        if (!firstInvalid) firstInvalid = field;
      }
    });

    if (firstInvalid) {
      firstInvalid.focus();
      return;
    }

    // Formulaire valide — soumettre
    form.dispatchEvent(new CustomEvent('form-valid', {
      bubbles: true,
      detail: { data: new FormData(form) }
    }));
  });
}

function clearErrors(form) {
  form.querySelectorAll('[aria-invalid]').forEach(field => {
    field.removeAttribute('aria-invalid');
    field.removeAttribute('aria-describedby');
  });
  form.querySelectorAll('.form-field__error').forEach(el => el.remove());
}

function showFieldError(field, message) {
  field.setAttribute('aria-invalid', 'true');

  const errorId = field.id + '-error';
  field.setAttribute('aria-describedby', errorId);

  const errorEl = document.createElement('span');
  errorEl.className = 'form-field__error';
  errorEl.id = errorId;
  errorEl.setAttribute('role', 'alert');
  errorEl.textContent = message;

  field.closest('.form-field')?.appendChild(errorEl);
}

function getErrorMessage(field) {
  if (field.validity.valueMissing) return 'Ce champ est requis.';
  if (field.validity.typeMismatch) return 'Le format est invalide.';
  if (field.validity.tooShort) return `Minimum ${field.minLength} caractères.`;
  if (field.validity.patternMismatch) return 'Le format ne correspond pas au modèle attendu.';
  return 'Valeur invalide.';
}
```

---

## 5. Liste avec recherche et filtres

### 5.1 Rôle

Pattern complet pour une page de type "liste d'entités" — le pattern le plus fréquent dans une application backoffice.

### 5.2 Structure

```
┌──────────────────────────────────────────────────────────────┐
│  Page Header                                  [Créer]        │
├──────────────────────────────────────────────────────────────┤
│  Toolbar : [🔍 Recherche] [Filtre] [Filtre]    [Exporter]   │
├──────────────────────────────────────────────────────────────┤
│  Data Table                                                  │
│  ┌──────────────────────────────────────────────────────────┐│
│  │ Nom ▲   │ Email          │ Rôle     │ Statut  │ Actions ││
│  │─────────│────────────────│──────────│─────────│─────────││
│  │ Alice   │ alice@ex.com   │ Admin    │ ● Actif │ ⋯       ││
│  │ Bob     │ bob@ex.com     │ User     │ ● Inact │ ⋯       ││
│  └──────────────────────────────────────────────────────────┘│
├──────────────────────────────────────────────────────────────┤
│  Pagination : ◀ 1 2 3 ... 12 ▶        Affichage 1–20 sur 235│
└──────────────────────────────────────────────────────────────┘
```

### 5.3 Markup

```html
<div class="page">
  <!-- Page Header -->
  <div class="page-header">
    <div class="page-header__start">
      <h1 class="page-header__title">Utilisateurs</h1>
      <p class="page-header__description">235 utilisateurs</p>
    </div>
    <div class="page-header__end">
      <button class="btn" data-variant="primary" type="button">
        <svg class="btn__icon" aria-hidden="true">...</svg>
        <span class="btn__label">Créer</span>
      </button>
    </div>
  </div>

  <!-- Toolbar -->
  <div class="toolbar" role="toolbar" aria-label="Filtres et actions">
    <!-- ... (voir section 3) -->
  </div>

  <!-- Data Table -->
  <div class="data-table-wrapper" role="region" aria-label="Liste des utilisateurs" tabindex="0">
    <!-- ... (voir 03-composants.md §9) -->
  </div>

  <!-- Pagination -->
  <nav class="pagination" aria-label="Pagination">
    <div class="pagination__info">
      Affichage <strong>1–20</strong> sur <strong>235</strong>
    </div>
    <div class="pagination__controls">
      <button class="btn" data-variant="ghost" data-size="sm" disabled aria-label="Page précédente">
        <svg class="btn__icon" aria-hidden="true">...</svg>
      </button>
      <button class="btn" data-variant="ghost" data-size="sm" aria-current="page" aria-label="Page 1">1</button>
      <button class="btn" data-variant="ghost" data-size="sm" aria-label="Page 2">2</button>
      <button class="btn" data-variant="ghost" data-size="sm" aria-label="Page 3">3</button>
      <span class="pagination__ellipsis">…</span>
      <button class="btn" data-variant="ghost" data-size="sm" aria-label="Page 12">12</button>
      <button class="btn" data-variant="ghost" data-size="sm" aria-label="Page suivante">
        <svg class="btn__icon" aria-hidden="true">...</svg>
      </button>
    </div>
  </nav>
</div>
```

### 5.4 CSS — Pagination

```css
.pagination {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: var(--gap-md);
  padding: var(--padding-md) 0;
}

.pagination__info {
  font-size: var(--text-sm);
  color: var(--color-text-muted);
}

.pagination__controls {
  display: flex;
  align-items: center;
  gap: var(--gap-xs);
}

.pagination__controls .btn[aria-current="page"] {
  background-color: var(--color-primary-subtle);
  color: var(--color-primary);
  font-weight: var(--font-weight-semibold);
}

.pagination__ellipsis {
  padding: 0 var(--space-1);
  color: var(--color-text-placeholder);
}
```

### 5.5 Règles

- Le compteur total est affiché dans la description du Page Header **et** dans la zone de pagination.
- La pagination est au format "fenêtre" : pages adjacentes à la page courante + première et dernière page.
- Si le nombre total d'éléments est ≤ 20 (ou la taille d'une page), la pagination est masquée.
- La page courante porte `aria-current="page"`.
- Les boutons Précédent/Suivant sont `disabled` quand on est sur la première/dernière page.

---

## 6. Modal (dialogue)

### 6.1 Rôle

Les modales interrompent le flux pour demander une action ou une confirmation. Elles utilisent l'élément natif `<dialog>` (Baseline Widely Available).

### 6.2 Markup

```html
<dialog class="modal" data-js-modal aria-labelledby="modal-title">
  <div class="modal__header">
    <h2 class="modal__title" id="modal-title">Confirmer la suppression</h2>
    <button class="btn" data-variant="ghost" data-size="sm"
            aria-label="Fermer" data-js-modal-close>
      <svg class="btn__icon" aria-hidden="true">...</svg>
    </button>
  </div>
  <div class="modal__body">
    <p>Êtes-vous sûr de vouloir supprimer cet utilisateur ?
       Cette action est irréversible.</p>
  </div>
  <div class="modal__footer">
    <button class="btn" data-variant="secondary" type="button" data-js-modal-close>Annuler</button>
    <button class="btn" data-variant="danger" type="button" data-js-modal-confirm>Supprimer</button>
  </div>
</dialog>
```

### 6.3 CSS

```css
.modal {
  padding: 0;
  border: none;
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-xl);
  max-width: 32rem;
  width: calc(100% - var(--space-8));

  background-color: var(--color-bg-default);
  color: var(--color-text-default);
}

.modal::backdrop {
  background-color: rgb(0 0 0 / 0.5);
}

.modal__header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: var(--gap-md);
  padding: var(--padding-lg);

  border-bottom: var(--border-width-default) solid var(--color-border-subtle);
}

.modal__title {
  font-size: var(--text-lg);
  font-weight: var(--font-weight-semibold);
  line-height: var(--leading-tight);
}

.modal__body {
  padding: var(--padding-lg);
  font-size: var(--text-base);
  line-height: var(--leading-normal);
}

.modal__footer {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  gap: var(--gap-sm);
  padding: var(--padding-md) var(--padding-lg);

  border-top: var(--border-width-default) solid var(--color-border-subtle);
}

/* Tailles */
.modal[data-size="sm"] { max-width: 24rem; }
.modal[data-size="lg"] { max-width: 48rem; }
.modal[data-size="xl"] { max-width: 64rem; }
```

### 6.4 Comportement JS

```js
// js/components/modal.js
export function initModal(dialog) {
  const closeBtns = dialog.querySelectorAll('[data-js-modal-close]');
  const confirmBtn = dialog.querySelector('[data-js-modal-confirm]');

  closeBtns.forEach(btn => {
    btn.addEventListener('click', () => dialog.close());
  });

  if (confirmBtn) {
    confirmBtn.addEventListener('click', () => {
      dialog.dispatchEvent(new CustomEvent('modal-confirm', { bubbles: true }));
      dialog.close();
    });
  }

  // Fermer sur clic du backdrop
  dialog.addEventListener('click', (e) => {
    if (e.target === dialog) dialog.close();
  });

  // Fermer sur Escape est géré nativement par <dialog>
}

// Ouvrir une modale
export function openModal(dialog) {
  dialog.showModal();
}
```

### 6.5 Règles

- Toujours utiliser `<dialog>` natif avec `.showModal()`, pas de div + overlay custom.
- `showModal()` gère automatiquement le `::backdrop`, le piège de focus (_focus trap_) et la fermeture par Escape.
- Le titre porte un `id` référencé par `aria-labelledby` sur le `<dialog>`.
- La modale de confirmation de suppression utilise un bouton `danger`, pas `primary`.
- Les modales ne doivent **pas** être imbriquées.
- Le focus est automatiquement placé sur le premier élément focusable à l'ouverture (comportement natif de `<dialog>`).

### 6.6 Variantes courantes

| Variante          | `data-size` | Contenu typique                              |
| ----------------- | ----------- | -------------------------------------------- |
| Confirmation      | `sm`        | Message + 2 boutons (annuler/confirmer)      |
| Formulaire simple | _(défaut)_  | Formulaire court (3-5 champs)                |
| Formulaire long   | `lg`        | Formulaire avec sections                     |
| Contenu riche     | `xl`        | Aperçu, comparaison, tableau de détails      |

---

## 7. Drawer (panneau latéral)

### 7.1 Rôle

Panneau qui s'ouvre depuis le bord droit de l'écran. Utilisé pour les détails d'une entité, les formulaires d'édition rapide ou les filtres avancés.

### 7.2 Markup

```html
<dialog class="drawer" data-js-drawer aria-labelledby="drawer-title">
  <div class="drawer__header">
    <h2 class="drawer__title" id="drawer-title">Détails de l'utilisateur</h2>
    <button class="btn" data-variant="ghost" data-size="sm"
            aria-label="Fermer" data-js-modal-close>
      <svg class="btn__icon" aria-hidden="true">...</svg>
    </button>
  </div>
  <div class="drawer__body">
    <!-- Contenu scrollable -->
  </div>
  <div class="drawer__footer">
    <button class="btn" data-variant="secondary" type="button" data-js-modal-close>Fermer</button>
    <button class="btn" data-variant="primary" type="button">Enregistrer</button>
  </div>
</dialog>
```

### 7.3 CSS

```css
.drawer {
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: auto;

  display: flex;
  flex-direction: column;

  width: min(32rem, calc(100% - var(--space-8)));
  max-width: none;
  max-height: 100dvh;
  height: 100dvh;
  margin: 0;
  padding: 0;
  border: none;
  border-left: var(--border-width-default) solid var(--color-border-subtle);

  background-color: var(--color-bg-default);
  box-shadow: var(--shadow-xl);

  transition: transform var(--duration-slow) var(--ease-out);
}

.drawer:not([open]) {
  transform: translateX(100%);
}

.drawer::backdrop {
  background-color: rgb(0 0 0 / 0.3);
}

.drawer__header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: var(--gap-md);
  padding: var(--padding-lg);
  flex-shrink: 0;

  border-bottom: var(--border-width-default) solid var(--color-border-subtle);
}

.drawer__title {
  font-size: var(--text-lg);
  font-weight: var(--font-weight-semibold);
  line-height: var(--leading-tight);
}

.drawer__body {
  flex: 1;
  overflow-y: auto;
  padding: var(--padding-lg);
}

.drawer__footer {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  gap: var(--gap-sm);
  padding: var(--padding-md) var(--padding-lg);
  flex-shrink: 0;

  border-top: var(--border-width-default) solid var(--color-border-subtle);
}
```

### 7.4 Règles

- Le drawer utilise `<dialog>` avec `.showModal()`, comme la modale.
- Il occupe 100% de la hauteur du viewport.
- Sa largeur est `min(32rem, 100% - 32px)`.
- Le contenu du body est scrollable indépendamment.
- Focus trap et fermeture Escape sont natifs via `<dialog>`.

---

## 8. Toast / Notification

### 8.1 Rôle

Message temporaire non bloquant qui apparaît pour confirmer une action ou signaler une erreur. Il disparaît automatiquement après un délai ou sur action utilisateur.

### 8.2 Markup

```html
<!-- Conteneur de toasts (un seul par page, dans l'App Shell) -->
<div class="toast-container" aria-live="polite" aria-label="Notifications">
  <!-- Les toasts sont injectés ici dynamiquement -->
</div>

<!-- Structure d'un toast individuel -->
<div class="toast" data-variant="success" role="status">
  <svg class="toast__icon" aria-hidden="true">...</svg>
  <span class="toast__message">Utilisateur créé avec succès.</span>
  <button class="btn" data-variant="ghost" data-size="sm"
          aria-label="Fermer la notification" data-js-toast-close>
    <svg class="btn__icon" aria-hidden="true">...</svg>
  </button>
</div>
```

### 8.3 CSS

```css
.toast-container {
  position: fixed;
  bottom: var(--space-6);
  right: var(--space-6);
  z-index: var(--z-toast);

  display: flex;
  flex-direction: column-reverse;
  gap: var(--gap-sm);

  max-width: 24rem;
  pointer-events: none;
}

.toast {
  display: flex;
  align-items: center;
  gap: var(--gap-sm);
  padding: var(--padding-md) var(--padding-lg);

  background-color: var(--color-bg-default);
  border: var(--border-width-default) solid var(--color-border-subtle);
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-lg);

  pointer-events: auto;

  animation: toast-enter var(--duration-normal) var(--ease-out);
}

@keyframes toast-enter {
  from {
    opacity: 0;
    transform: translateY(var(--space-4));
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.toast__icon {
  width: 1.25rem;
  height: 1.25rem;
  flex-shrink: 0;
}

.toast[data-variant="success"] .toast__icon { color: var(--color-success); }
.toast[data-variant="danger"]  .toast__icon { color: var(--color-danger); }
.toast[data-variant="warning"] .toast__icon { color: var(--color-warning); }
.toast[data-variant="info"]    .toast__icon { color: var(--color-info); }

.toast__message {
  flex: 1;
  font-size: var(--text-sm);
  color: var(--color-text-default);
}
```

### 8.4 Comportement JS

```js
// js/patterns/toast.js
const TOAST_DURATION = 5000;

export function showToast(container, { message, variant = 'info', duration = TOAST_DURATION }) {
  const toast = document.createElement('div');
  toast.className = 'toast';
  toast.setAttribute('data-variant', variant);
  toast.setAttribute('role', 'status');

  toast.innerHTML = `
    <svg class="toast__icon" aria-hidden="true"><!-- icône selon variant --></svg>
    <span class="toast__message">${message}</span>
    <button class="btn" data-variant="ghost" data-size="sm"
            aria-label="Fermer la notification" data-js-toast-close>
      <svg class="btn__icon" aria-hidden="true"><!-- icône close --></svg>
    </button>
  `;

  const closeBtn = toast.querySelector('[data-js-toast-close]');
  closeBtn.addEventListener('click', () => removeToast(toast));

  container.appendChild(toast);

  if (duration > 0) {
    setTimeout(() => removeToast(toast), duration);
  }
}

function removeToast(toast) {
  toast.style.opacity = '0';
  toast.style.transform = 'translateY(var(--space-2))';
  toast.style.transition = `opacity var(--duration-fast) var(--ease-in),
                            transform var(--duration-fast) var(--ease-in)`;
  toast.addEventListener('transitionend', () => toast.remove(), { once: true });
}
```

### 8.5 Règles

- Le conteneur porte `aria-live="polite"` pour que les lecteurs d'écran annoncent les nouveaux toasts.
- Chaque toast a `role="status"`.
- Durée par défaut : 5 secondes. Les toasts d'erreur restent affichés jusqu'à fermeture manuelle (`duration: 0`).
- Maximum 3 toasts visibles simultanément. Les plus anciens sont supprimés en premier.
- Position : coin inférieur droit, empilement vertical inversé (le plus récent en bas).

---

## 9. Empty State (état vide)

### 9.1 Rôle

Affiché quand une liste, un tableau ou une section n'a aucun contenu. Il guide l'utilisateur vers l'action pertinente.

### 9.2 Markup

```html
<div class="empty-state">
  <svg class="empty-state__icon" aria-hidden="true">...</svg>
  <h3 class="empty-state__title">Aucun utilisateur</h3>
  <p class="empty-state__description">
    Commencez par créer votre premier utilisateur.
  </p>
  <button class="btn" data-variant="primary" type="button">
    <svg class="btn__icon" aria-hidden="true">...</svg>
    <span class="btn__label">Créer un utilisateur</span>
  </button>
</div>
```

### 9.3 CSS

```css
.empty-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: var(--gap-md);

  padding: var(--space-16) var(--padding-lg);
  text-align: center;
}

.empty-state__icon {
  width: 3rem;
  height: 3rem;
  color: var(--color-text-placeholder);
}

.empty-state__title {
  font-size: var(--text-lg);
  font-weight: var(--font-weight-semibold);
  color: var(--color-text-default);
}

.empty-state__description {
  font-size: var(--text-base);
  color: var(--color-text-muted);
  max-width: 28rem;
}
```

### 9.4 Règles

- Le bouton d'action est toujours `primary`.
- L'icône est illustrative (grande, couleur muted), pas fonctionnelle.
- La description doit indiquer **pourquoi** c'est vide **et** **quoi faire**.
- L'empty state remplace le Data Table (il n'est pas affiché à côté d'un tableau vide).

---

## 10. Sidebar Navigation

### 10.1 Rôle

Navigation principale de l'application, placée dans le Side Nav de l'App Shell. Supporte les items simples, les groupes avec titre et les sous-menus collapsibles.

### 10.2 Markup

```html
<nav class="app-shell__sidenav" aria-label="Navigation principale">
  <div class="sidenav">
    <!-- Groupe -->
    <div class="sidenav__group">
      <span class="sidenav__group-title">Principal</span>
      <ul class="sidenav__list">
        <li>
          <a class="sidenav__item" href="/dashboard" aria-current="page">
            <svg class="sidenav__icon" aria-hidden="true">...</svg>
            <span class="sidenav__label">Tableau de bord</span>
          </a>
        </li>
        <li>
          <a class="sidenav__item" href="/users">
            <svg class="sidenav__icon" aria-hidden="true">...</svg>
            <span class="sidenav__label">Utilisateurs</span>
            <span class="badge" data-variant="neutral">12</span>
          </a>
        </li>
      </ul>
    </div>

    <!-- Groupe avec sous-menu collapsible -->
    <div class="sidenav__group">
      <span class="sidenav__group-title">Configuration</span>
      <ul class="sidenav__list">
        <li>
          <button class="sidenav__item" aria-expanded="false"
                  data-js-sidenav-toggle>
            <svg class="sidenav__icon" aria-hidden="true">...</svg>
            <span class="sidenav__label">Paramètres</span>
            <svg class="sidenav__chevron" aria-hidden="true">...</svg>
          </button>
          <ul class="sidenav__sublist" hidden>
            <li><a class="sidenav__subitem" href="/settings/general">Général</a></li>
            <li><a class="sidenav__subitem" href="/settings/security">Sécurité</a></li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</nav>
```

### 10.3 CSS

```css
.sidenav {
  display: flex;
  flex-direction: column;
  gap: var(--gap-lg);
  padding: var(--padding-md);
}

.sidenav__group-title {
  display: block;
  padding: var(--space-1) var(--padding-sm);
  font-size: var(--text-xs);
  font-weight: var(--font-weight-semibold);
  color: var(--color-text-muted);
  text-transform: uppercase;
  letter-spacing: var(--tracking-wide);
}

.sidenav__list {
  display: flex;
  flex-direction: column;
  gap: var(--space-0-5);
}

.sidenav__item {
  display: flex;
  align-items: center;
  gap: var(--gap-sm);
  width: 100%;
  padding: var(--padding-sm);

  font-size: var(--text-sm);
  font-weight: var(--font-weight-regular);
  color: var(--color-text-subtle);

  border-radius: var(--radius-sm);
  cursor: pointer;
  transition: background-color var(--duration-fast) var(--ease-default),
              color var(--duration-fast) var(--ease-default);
}

.sidenav__item:hover {
  background-color: var(--color-bg-subtle);
  color: var(--color-text-default);
}

.sidenav__item[aria-current="page"] {
  background-color: var(--color-primary-subtle);
  color: var(--color-primary);
  font-weight: var(--font-weight-medium);
}

.sidenav__icon {
  width: 1.125rem;
  height: 1.125rem;
  flex-shrink: 0;
}

.sidenav__label {
  flex: 1;
  min-width: 0;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.sidenav__chevron {
  width: 0.75rem;
  height: 0.75rem;
  flex-shrink: 0;
  transition: transform var(--duration-fast) var(--ease-default);
}

.sidenav__item[aria-expanded="true"] .sidenav__chevron {
  transform: rotate(90deg);
}

.sidenav__sublist {
  display: flex;
  flex-direction: column;
  gap: var(--space-0-5);
  padding-left: calc(var(--padding-sm) + 1.125rem + var(--gap-sm));
}

.sidenav__sublist[hidden] {
  display: none;
}

.sidenav__subitem {
  display: block;
  padding: var(--padding-xs) var(--padding-sm);

  font-size: var(--text-sm);
  color: var(--color-text-muted);

  border-radius: var(--radius-sm);
  transition: color var(--duration-fast) var(--ease-default),
              background-color var(--duration-fast) var(--ease-default);
}

.sidenav__subitem:hover {
  color: var(--color-text-default);
  background-color: var(--color-bg-subtle);
}

.sidenav__subitem[aria-current="page"] {
  color: var(--color-primary);
  font-weight: var(--font-weight-medium);
}

/* === Nav collapsée (icônes seules) === */
.app-shell[data-nav-collapsed] .sidenav__label,
.app-shell[data-nav-collapsed] .sidenav__group-title,
.app-shell[data-nav-collapsed] .sidenav__chevron,
.app-shell[data-nav-collapsed] .sidenav__sublist,
.app-shell[data-nav-collapsed] .badge {
  display: none;
}

.app-shell[data-nav-collapsed] .sidenav__item {
  justify-content: center;
  padding: var(--padding-sm);
}
```

### 10.4 Règles

- La page active est identifiée par `aria-current="page"` sur le lien correspondant.
- En mode collapsed, seules les icônes sont visibles. Les labels sont masqués via CSS.
- Les sous-menus sont toggles par `aria-expanded` + `hidden`.
- Un badge de comptage peut être ajouté à côté du label pour indiquer des éléments non lus ou en attente.

---

## 11. Récapitulatif des patterns

| Pattern                      | Composants utilisés                          | Section |
| ---------------------------- | -------------------------------------------- | ------- |
| Page Header                  | Breadcrumb, Button                           | 2       |
| Toolbar                      | Input, Button, Dropdown                      | 3       |
| Formulaire création/édition  | Form Field, Input, Select, Checkbox, Button  | 4       |
| Liste avec recherche/filtres | Page Header, Toolbar, Data Table, Pagination | 5       |
| Modal                        | Dialog (natif), Button                       | 6       |
| Drawer                       | Dialog (natif), Button                       | 7       |
| Toast / Notification         | Alert (variante), Button                     | 8       |
| Empty State                  | Button                                       | 9       |
| Sidebar Navigation           | Button, Badge                                | 10      |

---

## 12. Versioning de ce document

| Version | Date       | Changement        |
| ------- | ---------- | ----------------- |
| 0.1     | 2026-03-25 | Création initiale |
