# Design System — 03 Composants

> **Prérequis** : avoir lu [00-fondations.md](./00-fondations.md), [01-design-tokens.md](./01-design-tokens.md), [02-layout.md](./02-layout.md) et [07-regles-code.md](./07-regles-code.md).

Ce document est le catalogue des composants UI du design system. Chaque composant est défini par son **markup HTML**, son **CSS** (références aux tokens), ses **états**, ses **variantes**, ses **exigences d'accessibilité** et, le cas échéant, son **comportement JavaScript**.

### Navigation rapide — Sections

> Utiliser ces références pour cibler la lecture selon la tâche (voir aussi la table de routage dans `SKILL.md`).

| §  | Composant                       | Classe BEM     | Référence SKILL.md                          |
| -- | ------------------------------- | -------------- | ------------------------------------------- |
| 1  | Conventions globales            | —              | Toujours applicable                         |
| 2  | Button                          | `.btn`         | "Buttons"                                   |
| 3  | Input / Champs de formulaire    | `.input`       | "Inputs, textarea, select"                  |
| 4  | Textarea                        | `.textarea`    | "Inputs, textarea, select"                  |
| 5  | Select                          | `.select`      | "Inputs, textarea, select"                  |
| 6  | Checkbox et Radio               | `.checkbox`, `.radio` | "Checkbox, radio, toggle"            |
| 7  | Toggle Switch                   | `.toggle`      | "Checkbox, radio, toggle"                   |
| 8  | Card                            | `.card`        | "Cards"                                     |
| 9  | Data Table                      | `.data-table`  | "Data tables"                               |
| 10 | Badge                           | `.badge`       | "Badges"                                    |
| 11 | Alert                           | `.alert`       | "Alerts"                                    |
| 12 | Dropdown / Menu                 | `.dropdown`    | "Dropdowns / menus"                         |
| 13 | Tabs                            | `.tabs`        | "Tabs"                                      |
| 14 | Tooltip                         | `.tooltip`     | "Tooltips"                                  |
| 15 | Accordion / Section repliable   | `<details>`    | "Accordion, collapsible sections"           |
| 16 | Récapitulatif                   | —              | Référence rapide                            |

---

## 1. Conventions appliquées à tous les composants

Ces règles s'ajoutent à celles de [07-regles-code.md](./07-regles-code.md) et s'appliquent à chaque composant décrit dans ce document :

1. **Un fichier CSS par composant** dans `css/components/`.
2. **Un fichier JS par composant interactif** dans `js/components/`.
3. **Variantes** exprimées par `data-variant`.
4. **Tailles** exprimées par `data-size` (valeurs : `sm`, `md` par défaut implicite, `lg`).
5. **États dynamiques** exprimés par attributs HTML natifs (`disabled`, `open`, `hidden`) ou ARIA (`aria-expanded`, `aria-selected`, `aria-pressed`, etc.).
6. **Tokens uniquement** : aucune valeur brute dans le CSS du composant.
7. **Focus visible** : tout composant interactif hérite du style `:focus-visible` global. Si un composant nécessite un style de focus spécifique, il est documenté.
8. **CSS Containment** : tout composant autonome déclare `contain: content` sur son élément racine. Les exceptions (composants avec enfants `position: fixed`, overflow visible intentionnel ou stacking context partagé) doivent être documentées dans le fichier CSS du composant. Voir `07-regles-code.md §3.9` pour le détail.

### Tailles standard

Sauf mention contraire, les trois tailles suivantes sont disponibles :

| Attribut           | Hauteur cible | Padding                              | Font-size          | Usage                         |
| ------------------ | ------------- | ------------------------------------ | ------------------ | ----------------------------- |
| _(absent = md)_    | 36px (2.25rem)| `var(--padding-sm) var(--padding-md)` | `var(--text-base)` | Usage courant                 |
| `data-size="sm"`   | 28px (1.75rem)| `var(--padding-xs) var(--padding-sm)` | `var(--text-sm)`   | Tableaux, barres d'outils     |
| `data-size="lg"`   | 44px (2.75rem)| `var(--padding-md) var(--padding-lg)` | `var(--text-md)`   | Actions principales de page   |

---

## 2. Button — `.btn`

### 2.1 Markup

```html
<!-- Bouton standard -->
<button class="btn" data-variant="primary" type="button">
  Enregistrer
</button>

<!-- Bouton avec icône -->
<button class="btn" data-variant="secondary" type="button">
  <svg class="btn__icon" aria-hidden="true">...</svg>
  <span class="btn__label">Exporter</span>
</button>

<!-- Bouton icône seule -->
<button class="btn" data-variant="ghost" data-size="sm" type="button" aria-label="Fermer">
  <svg class="btn__icon" aria-hidden="true">...</svg>
</button>
```

### 2.2 Variantes

| `data-variant`  | Fond                        | Texte                        | Bordure                      | Usage                              |
| --------------- | --------------------------- | ---------------------------- | ---------------------------- | ---------------------------------- |
| `primary`       | `--color-primary`           | `--color-text-on-primary`    | `transparent`                | Action principale de la vue        |
| `secondary`     | `--color-bg-default`        | `--color-text-default`       | `--color-border-default`     | Action secondaire                  |
| `danger`        | `--color-danger`            | `--color-text-on-primary`    | `transparent`                | Suppression, action irréversible   |
| `ghost`         | `transparent`               | `--color-text-default`       | `transparent`                | Action tertiaire, barres d'outils  |
| `link`          | `transparent`               | `--color-primary`            | `transparent`                | Action inline dans du texte        |

**Règle** : une vue ne contient qu'**un seul** bouton `primary`. Les autres actions sont `secondary` ou `ghost`.

**Hiérarchie des actions — règle absolue**

Quatre niveaux d'action sont disponibles :

| Niveau | Variante | Usage |
|---|---|---|
| Primaire | `data-variant="primary"` | L'action la plus importante de la section — **une seule par section visible** |
| Secondaire | `data-variant="secondary"` | Actions importantes mais non prioritaires |
| Tertiaire | `data-variant="ghost"` | Actions de faible importance, barres d'outils, Annuler |
| Destructive | `data-variant="danger"` | Actions irréversibles uniquement |

Si aucune action n'est clairement prioritaire, utiliser `secondary` pour toutes plutôt que de promouvoir arbitrairement une en `primary`.

### 2.3 CSS

```css
/* === Base === */
.btn {
  /* contain: content isole le composant pour les performances de rendu.
     Exception : si un badge ou un tooltip déborde visuellement du bouton,
     retirer contain et utiliser contain: layout style à la place. */
  contain: content;

  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: var(--gap-xs);

  padding: var(--padding-sm) var(--padding-md);
  min-height: 2.25rem;

  font-family: var(--font-sans);
  font-size: var(--text-base);
  font-weight: var(--font-weight-medium);
  line-height: var(--leading-tight);
  white-space: nowrap;

  border: var(--border-width-default) solid transparent;
  border-radius: var(--radius-sm);
  cursor: pointer;
  transition: background-color var(--duration-fast) var(--ease-default),
              border-color var(--duration-fast) var(--ease-default),
              color var(--duration-fast) var(--ease-default),
              box-shadow var(--duration-fast) var(--ease-default);
}

/* === Variantes === */
.btn[data-variant="primary"] {
  background-color: var(--color-primary);
  color: var(--color-text-on-primary);
}
.btn[data-variant="primary"]:hover {
  background-color: var(--color-primary-hover);
}
.btn[data-variant="primary"]:active {
  background-color: var(--color-primary-active);
}

.btn[data-variant="secondary"] {
  background-color: var(--color-bg-default);
  color: var(--color-text-default);
  border-color: var(--color-border-default);
}
.btn[data-variant="secondary"]:hover {
  background-color: var(--color-bg-subtle);
  border-color: var(--color-border-strong);
}

.btn[data-variant="danger"] {
  background-color: var(--color-danger);
  color: var(--color-text-on-primary);
}
.btn[data-variant="danger"]:hover {
  background-color: var(--color-danger-text);
}

.btn[data-variant="ghost"] {
  background-color: transparent;
  color: var(--color-text-default);
}
.btn[data-variant="ghost"]:hover {
  background-color: var(--color-bg-subtle);
}

.btn[data-variant="link"] {
  background-color: transparent;
  color: var(--color-primary);
  padding-inline: 0;
  min-height: auto;
  border-radius: 0;
}
.btn[data-variant="link"]:hover {
  text-decoration: underline;
}

/* === Tailles === */
.btn[data-size="sm"] {
  padding: var(--padding-xs) var(--padding-sm);
  min-height: 1.75rem;
  font-size: var(--text-sm);
}
.btn[data-size="lg"] {
  padding: var(--padding-md) var(--padding-lg);
  min-height: 2.75rem;
  font-size: var(--text-md);
}

/* === États === */
.btn:focus-visible {
  outline: none;
  box-shadow: 0 0 0 2px var(--color-bg-default),
              0 0 0 4px var(--color-focus-ring);
}

.btn:disabled,
.btn[aria-disabled="true"] {
  opacity: 0.5;
  cursor: not-allowed;
  pointer-events: none;
}

.btn[aria-pressed="true"] {
  background-color: var(--color-bg-muted);
}

/* === Sous-éléments === */
.btn__icon {
  width: 1em;
  height: 1em;
  flex-shrink: 0;
}
```

> La technique double anneau (anneau blanc interne + anneau coloré externe) garantit la visibilité du focus sur n'importe quelle couleur de fond, contrairement à un simple `outline`.

### 2.4 Accessibilité

- Un bouton icône seule **doit** avoir un `aria-label`.
- L'icône porte `aria-hidden="true"`.
- Un bouton toggle (on/off) utilise `aria-pressed`.
- Un bouton qui ouvre un menu/dropdown utilise `aria-expanded` et `aria-haspopup`.
- Le type `type="button"` est obligatoire sauf pour la soumission de formulaire (`type="submit"`).

---

## 3. Input / Champs de formulaire — `.input`

### 3.1 Markup

```html
<!-- Champ texte -->
<div class="form-field">
  <label class="form-field__label" for="email">Adresse email</label>
  <input class="input" type="email" id="email" name="email"
         placeholder="ex: nom@domaine.com">
  <span class="form-field__hint">Utilisé pour la connexion.</span>
</div>

<!-- Champ en erreur -->
<div class="form-field" data-state="error">
  <label class="form-field__label" for="password">Mot de passe</label>
  <input class="input" type="password" id="password" name="password"
         aria-invalid="true" aria-describedby="password-error">
  <span class="form-field__error" id="password-error" role="alert">
    Le mot de passe doit contenir au moins 8 caractères.
  </span>
</div>

<!-- Champ avec préfixe/suffixe -->
<div class="form-field">
  <label class="form-field__label" for="price">Prix</label>
  <div class="input-group">
    <span class="input-group__addon">CHF</span>
    <input class="input" type="number" id="price" name="price" step="0.01">
  </div>
</div>
```

### 3.2 CSS — Input

```css
.input {
  contain: content;

  display: block;
  width: 100%;
  padding: var(--padding-sm) var(--padding-md);
  min-height: 2.25rem;

  font-family: var(--font-sans);
  font-size: var(--text-base);
  line-height: var(--leading-normal);
  color: var(--color-text-default);

  background-color: var(--color-bg-default);
  border: var(--border-width-default) solid var(--color-border-default);
  border-radius: var(--radius-sm);

  box-shadow: inset 0 1px 2px rgb(0 0 0 / 0.05);

  transition: border-color var(--duration-fast) var(--ease-default),
              box-shadow var(--duration-fast) var(--ease-default);
}

.input::placeholder {
  color: var(--color-text-placeholder);
}

.input:hover {
  border-color: var(--color-border-strong);
}

.input:focus-visible {
  border-color: var(--color-border-focus);
  box-shadow: inset 0 1px 2px rgb(0 0 0 / 0.05),
              0 0 0 2px var(--color-bg-default),
              0 0 0 4px var(--color-focus-ring);
  outline: none;
}

.input:disabled {
  background-color: var(--color-bg-subtle);
  color: var(--color-text-muted);
  cursor: not-allowed;
}

.input[aria-invalid="true"] {
  border-color: var(--color-danger);
}

.input[aria-invalid="true"]:focus-visible {
  outline-color: var(--color-danger);
}

/* Tailles */
.input[data-size="sm"] {
  padding: var(--padding-xs) var(--padding-sm);
  min-height: 1.75rem;
  font-size: var(--text-sm);
}

.input[data-size="lg"] {
  padding: var(--padding-md) var(--padding-lg);
  min-height: 2.75rem;
  font-size: var(--text-md);
}
```

> L'ombre inset crée un effet de champ encaissé qui distingue visuellement les inputs des boutons et des cartes.

### 3.3 CSS — Form Field (wrapper)

```css
.form-field {
  display: flex;
  flex-direction: column;
  gap: var(--gap-xs);
}

.form-field__label {
  font-size: var(--text-sm);
  font-weight: var(--font-weight-medium);
  color: var(--color-text-default);
}

.form-field__hint {
  font-size: var(--text-xs);
  color: var(--color-text-muted);
}

.form-field__error {
  font-size: var(--text-xs);
  color: var(--color-danger-text);
}
```

### 3.4 CSS — Input Group (préfixe/suffixe)

```css
.input-group {
  display: flex;
  align-items: stretch;
}

.input-group .input {
  border-radius: 0;
  flex: 1;
  min-width: 0;
}

.input-group .input:first-child  { border-radius: var(--radius-sm) 0 0 var(--radius-sm); }
.input-group .input:last-child   { border-radius: 0 var(--radius-sm) var(--radius-sm) 0; }
.input-group .input:only-child   { border-radius: var(--radius-sm); }

.input-group__addon {
  display: flex;
  align-items: center;
  padding: 0 var(--padding-sm);

  font-size: var(--text-sm);
  color: var(--color-text-muted);

  background-color: var(--color-bg-subtle);
  border: var(--border-width-default) solid var(--color-border-default);
}

.input-group__addon:first-child {
  border-right: 0;
  border-radius: var(--radius-sm) 0 0 var(--radius-sm);
}

.input-group__addon:last-child {
  border-left: 0;
  border-radius: 0 var(--radius-sm) var(--radius-sm) 0;
}
```

### 3.5 Accessibilité — Champs de formulaire

- Tout `<input>` est associé à un `<label>` via `for`/`id`. Pas de label implicite (wrapping) pour garantir la compatibilité avec tous les lecteurs d'écran.
- Un champ en erreur porte `aria-invalid="true"` et `aria-describedby` pointant vers le message d'erreur.
- Le message d'erreur porte `role="alert"` pour être annoncé immédiatement par les lecteurs d'écran.
- Les champs requis portent `required` (attribut natif) et non `aria-required`.
- Un hint (texte d'aide) est relié au champ via `aria-describedby`.

---

## 4. Textarea — `.textarea`

### 4.1 Markup

```html
<div class="form-field">
  <label class="form-field__label" for="notes">Notes</label>
  <textarea class="textarea" id="notes" name="notes" rows="4"></textarea>
</div>
```

### 4.2 CSS

```css
.textarea {
  contain: content;

  display: block;
  width: 100%;
  padding: var(--padding-sm) var(--padding-md);
  min-height: 5rem;

  font-family: var(--font-sans);
  font-size: var(--text-base);
  line-height: var(--leading-normal);
  color: var(--color-text-default);

  background-color: var(--color-bg-default);
  border: var(--border-width-default) solid var(--color-border-default);
  border-radius: var(--radius-sm);
  resize: vertical;

  transition: border-color var(--duration-fast) var(--ease-default);
}

/* Les états (:hover, :focus-visible, :disabled, [aria-invalid]) sont
   identiques à .input — voir section 3.2. */
```

---

## 5. Select — `.select`

### 5.1 Markup

```html
<div class="form-field">
  <label class="form-field__label" for="country">Pays</label>
  <div class="select-wrapper">
    <select class="select" id="country" name="country">
      <option value="">— Choisir —</option>
      <option value="ch">Suisse</option>
      <option value="fr">France</option>
    </select>
  </div>
</div>
```

### 5.2 CSS

```css
.select-wrapper {
  contain: content;

  position: relative;
  display: inline-flex;
  width: 100%;
}

.select {
  display: block;
  width: 100%;
  padding: var(--padding-sm) var(--space-8) var(--padding-sm) var(--padding-md);
  min-height: 2.25rem;

  font-family: var(--font-sans);
  font-size: var(--text-base);
  line-height: var(--leading-normal);
  color: var(--color-text-default);

  background-color: var(--color-bg-default);
  border: var(--border-width-default) solid var(--color-border-default);
  border-radius: var(--radius-sm);

  appearance: none;
  cursor: pointer;
  transition: border-color var(--duration-fast) var(--ease-default);
}

/* Flèche du select */
.select-wrapper::after {
  content: "";
  position: absolute;
  right: var(--space-3);
  top: 50%;
  width: 0.5rem;
  height: 0.5rem;
  border-right: var(--border-width-thick) solid var(--color-text-muted);
  border-bottom: var(--border-width-thick) solid var(--color-text-muted);
  transform: translateY(-65%) rotate(45deg);
  pointer-events: none;
}

/* Les états (:hover, :focus-visible, :disabled) sont
   identiques à .input — voir section 3.2. */
```

---

## 6. Checkbox et Radio — `.checkbox`, `.radio`

### 6.1 Markup

```html
<!-- Checkbox -->
<label class="checkbox">
  <input class="checkbox__input" type="checkbox" name="terms" value="1">
  <span class="checkbox__label">J'accepte les conditions</span>
</label>

<!-- Radio -->
<fieldset>
  <legend class="form-field__label">Statut</legend>
  <div class="flex flex-col" style="gap: var(--gap-sm);">
    <label class="radio">
      <input class="radio__input" type="radio" name="status" value="active">
      <span class="radio__label">Actif</span>
    </label>
    <label class="radio">
      <input class="radio__input" type="radio" name="status" value="inactive">
      <span class="radio__label">Inactif</span>
    </label>
  </div>
</fieldset>
```

### 6.2 CSS

```css
.checkbox,
.radio {
  contain: content;

  display: inline-flex;
  align-items: center;
  gap: var(--gap-sm);
  cursor: pointer;
  font-size: var(--text-base);
  color: var(--color-text-default);
}

.checkbox__input,
.radio__input {
  width: 1.125rem;
  height: 1.125rem;
  flex-shrink: 0;
  cursor: pointer;

  accent-color: var(--color-primary);
}

.checkbox__input:disabled,
.radio__input:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.checkbox__input:disabled + .checkbox__label,
.radio__input:disabled + .radio__label {
  opacity: 0.5;
  cursor: not-allowed;
}
```

**Note** : `accent-color` est Baseline Widely Available. Il style les contrôles natifs sans les reconstruire en HTML/CSS custom, ce qui préserve l'accessibilité native.

---

## 7. Toggle Switch — `.toggle`

### 7.1 Markup

```html
<label class="toggle">
  <input class="toggle__input" type="checkbox" role="switch" aria-checked="false">
  <span class="toggle__track"></span>
  <span class="toggle__label">Notifications actives</span>
</label>
```

### 7.2 CSS

```css
.toggle {
  contain: content;

  display: inline-flex;
  align-items: center;
  gap: var(--gap-sm);
  cursor: pointer;
}

.toggle__input {
  position: absolute;
  width: 1px;
  height: 1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
}

.toggle__track {
  position: relative;
  width: 2.5rem;
  height: 1.375rem;
  flex-shrink: 0;

  background-color: var(--color-bg-muted);
  border-radius: var(--radius-full);
  transition: background-color var(--duration-fast) var(--ease-default);
}

.toggle__track::after {
  content: "";
  position: absolute;
  top: 0.125rem;
  left: 0.125rem;
  width: 1.125rem;
  height: 1.125rem;

  background-color: var(--color-bg-default);
  border-radius: var(--radius-full);
  box-shadow: var(--shadow-xs);
  transition: transform var(--duration-fast) var(--ease-default);
}

.toggle__input:checked + .toggle__track {
  background-color: var(--color-primary);
}

.toggle__input:checked + .toggle__track::after {
  transform: translateX(1.125rem);
}

.toggle__input:focus-visible + .toggle__track {
  outline: 2px solid var(--color-focus-ring);
  outline-offset: 2px;
}

.toggle__input:disabled + .toggle__track {
  opacity: 0.5;
  cursor: not-allowed;
}

.toggle__label {
  font-size: var(--text-base);
  color: var(--color-text-default);
}
```

### 7.3 Accessibilité

- L'input porte `role="switch"` (Baseline Newly Available) et `aria-checked`.
- Le label visible est le `<span class="toggle__label">`. L'association est implicite via le `<label>` parent.
- L'input est visuellement masqué mais reste dans le flux d'accessibilité (pas de `display: none`).

---

## 8. Card — `.card`

### 8.1 Markup

```html
<article class="card">
  <header class="card__header">
    <h3 class="card__title">Résumé mensuel</h3>
    <button class="btn" data-variant="ghost" data-size="sm" aria-label="Options">
      <svg class="btn__icon" aria-hidden="true">...</svg>
    </button>
  </header>
  <div class="card__body">
    <p>Contenu de la carte...</p>
  </div>
  <footer class="card__footer">
    <button class="btn" data-variant="secondary" data-size="sm">Détails</button>
  </footer>
</article>
```

### 8.2 CSS

```css
.card {
  contain: content;

  display: flex;
  flex-direction: column;

  background-color: var(--color-bg-default);
  border: var(--border-width-default) solid var(--color-border-subtle);
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-xs);

  overflow: hidden;
}

.card__header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: var(--gap-sm);
  padding: var(--padding-md) var(--padding-lg);

  border-bottom: var(--border-width-default) solid var(--color-border-subtle);
}

.card__title {
  font-size: var(--text-base);
  font-weight: var(--font-weight-semibold);
  color: var(--color-text-default);
}

.card__body {
  padding: var(--padding-lg);
  flex: 1;
}

.card__footer {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  gap: var(--gap-sm);
  padding: var(--padding-md) var(--padding-lg);

  border-top: var(--border-width-default) solid var(--color-border-subtle);
}
```

---

## 9. Data Table — `.data-table`

Le composant le plus critique pour les applications backoffice. Les tableaux de données utilisent le HTML natif `<table>` pour la sémantique et l'accessibilité.

### 9.1 Markup

```html
<div class="data-table-wrapper" role="region" aria-label="Liste des utilisateurs" tabindex="0">
  <table class="data-table">
    <thead class="data-table__head">
      <tr>
        <th class="data-table__th" scope="col">
          <button class="data-table__sort" data-js-sort="name" aria-sort="ascending">
            Nom
            <svg class="data-table__sort-icon" aria-hidden="true">...</svg>
          </button>
        </th>
        <th class="data-table__th" scope="col">Email</th>
        <th class="data-table__th" scope="col">Rôle</th>
        <th class="data-table__th" scope="col">
          <span class="sr-only">Actions</span>
        </th>
      </tr>
    </thead>
    <tbody class="data-table__body">
      <tr class="data-table__row">
        <td class="data-table__td">Alice Martin</td>
        <td class="data-table__td">alice@example.com</td>
        <td class="data-table__td"><span class="badge" data-variant="info">Admin</span></td>
        <td class="data-table__td data-table__td--actions">
          <button class="btn" data-variant="ghost" data-size="sm" aria-label="Modifier Alice Martin">
            <svg class="btn__icon" aria-hidden="true">...</svg>
          </button>
        </td>
      </tr>
    </tbody>
  </table>
</div>
```

### 9.2 CSS

```css
/* Wrapper scrollable horizontalement si le tableau dépasse */
.data-table-wrapper {
  overflow-x: auto;
  border: var(--border-width-default) solid var(--color-border-subtle);
  border-radius: var(--radius-md);
}

.data-table {
  width: 100%;
  border-collapse: collapse;
  font-size: var(--text-sm);
}

/* En-tête */
.data-table__head {
  background-color: var(--color-bg-subtle);
  position: sticky;
  top: 0;
  z-index: var(--z-table-header);
}

.data-table__th {
  padding: var(--padding-sm) var(--padding-md);

  font-size: var(--text-xs);
  font-weight: var(--font-weight-medium);
  color: var(--color-text-subtle);
  text-align: left;
  text-transform: uppercase;
  letter-spacing: var(--tracking-wide);
  white-space: nowrap;

  border-bottom: var(--border-width-default) solid var(--color-border-default);
}

/* Cellules */
.data-table__td {
  padding: var(--padding-sm) var(--padding-md);
  color: var(--color-text-default);
  border-bottom: var(--border-width-default) solid var(--color-border-subtle);
  vertical-align: middle;
}

.data-table__td--actions {
  text-align: right;
  white-space: nowrap;
  width: 1%;
}

/* Lignes */
.data-table__row {
  contain: content;
}

.data-table__row:hover {
  background-color: var(--color-bg-subtle);
}

.data-table__row[aria-selected="true"] {
  background-color: var(--color-primary-subtle);
}

/* Tri */
.data-table__sort {
  display: inline-flex;
  align-items: center;
  gap: var(--gap-xs);
  cursor: pointer;
  font: inherit;
  color: inherit;
  text-transform: inherit;
  letter-spacing: inherit;
}

.data-table__sort-icon {
  width: 0.75rem;
  height: 0.75rem;
  opacity: 0.4;
  transition: opacity var(--duration-fast) var(--ease-default);
}

.data-table__sort[aria-sort] .data-table__sort-icon {
  opacity: 1;
}

/* Table dense */
.data-table[data-density="compact"] .data-table__th,
.data-table[data-density="compact"] .data-table__td {
  padding: var(--padding-xs) var(--padding-sm);
}
```

### 9.3 Accessibilité — Data Table

- Le wrapper porte `role="region"`, `aria-label` et `tabindex="0"` pour permettre le scroll au clavier.
- Les en-têtes portent `scope="col"`.
- Les boutons de tri portent `aria-sort` (`ascending`, `descending` ou `none`).
- Les lignes sélectionnables portent `aria-selected`.
- La colonne d'actions a un en-tête `<span class="sr-only">Actions</span>`.
- Les boutons d'action sur chaque ligne ont un `aria-label` qui identifie l'entité (`aria-label="Modifier Alice Martin"`).

**Règles sur les actions de ligne**

- **≤ 3 actions fréquentes** : afficher des boutons icône inline avec tooltip + un menu kebab (⋮) pour les actions supplémentaires.
- **≥ 4 actions ou tableau dense** : utiliser uniquement le menu kebab — aucun bouton inline.
- Le menu kebab est placé dans la **dernière colonne, aligné à droite**, sans libellé d'en-tête (remplacé par `<span class="sr-only">Actions</span>`).
- Dans le menu kebab, les actions destructives sont **les dernières**, séparées par un `<hr class="dropdown__separator">`, avec `data-variant="danger"`.
- Quand l'action principale d'une ligne est la navigation vers le détail, rendre **la ligne entière cliquable** et supprimer les boutons d'action visibles.

Ajouter `font-variant-numeric: tabular-nums` sur toute colonne contenant des valeurs numériques :

```css
.data-table__td--numeric {
  text-align: right;
  font-variant-numeric: tabular-nums;
}
```

---

## 10. Badge — `.badge`

### 10.1 Markup

```html
<span class="badge" data-variant="info">Admin</span>
<span class="badge" data-variant="success">Actif</span>
<span class="badge" data-variant="danger">Expiré</span>
<span class="badge" data-variant="warning">En attente</span>
<span class="badge" data-variant="neutral">Brouillon</span>
```

### 10.2 CSS

```css
.badge {
  contain: content;

  display: inline-flex;
  align-items: center;
  gap: var(--gap-xs);
  padding: var(--space-0-5) var(--space-2);

  font-size: var(--text-xs);
  font-weight: var(--font-weight-medium);
  line-height: var(--leading-tight);
  white-space: nowrap;

  border-radius: var(--radius-full);
}

.badge[data-variant="neutral"] {
  background-color: var(--color-bg-muted);
  color: var(--color-text-subtle);
}

.badge[data-variant="info"] {
  background-color: var(--color-info-subtle);
  color: var(--color-info-text);
}

.badge[data-variant="success"] {
  background-color: var(--color-success-subtle);
  color: var(--color-success-text);
}

.badge[data-variant="warning"] {
  background-color: var(--color-warning-subtle);
  color: var(--color-warning-text);
}

.badge[data-variant="danger"] {
  background-color: var(--color-danger-subtle);
  color: var(--color-danger-text);
}
```

---

## 11. Alert — `.alert`

### 11.1 Markup

```html
<div class="alert" data-variant="warning" role="alert">
  <svg class="alert__icon" aria-hidden="true">...</svg>
  <div class="alert__content">
    <strong class="alert__title">Attention</strong>
    <p class="alert__message">Votre session expire dans 5 minutes.</p>
  </div>
  <button class="btn" data-variant="ghost" data-size="sm" aria-label="Fermer l'alerte">
    <svg class="btn__icon" aria-hidden="true">...</svg>
  </button>
</div>
```

### 11.2 CSS

```css
.alert {
  contain: content;

  display: flex;
  align-items: flex-start;
  gap: var(--gap-sm);
  padding: var(--padding-md) var(--padding-lg);

  border: var(--border-width-default) solid transparent;
  border-radius: var(--radius-md);
}

.alert[data-variant="info"] {
  background-color: var(--color-info-subtle);
  border-color: var(--color-info);
  color: var(--color-info-text);
}

.alert[data-variant="success"] {
  background-color: var(--color-success-subtle);
  border-color: var(--color-success);
  color: var(--color-success-text);
}

.alert[data-variant="warning"] {
  background-color: var(--color-warning-subtle);
  border-color: var(--color-warning);
  color: var(--color-warning-text);
}

.alert[data-variant="danger"] {
  background-color: var(--color-danger-subtle);
  border-color: var(--color-danger);
  color: var(--color-danger-text);
}

.alert__icon {
  width: 1.25rem;
  height: 1.25rem;
  flex-shrink: 0;
  margin-top: var(--space-0-5);
}

.alert__content {
  flex: 1;
  min-width: 0;
}

.alert__title {
  font-weight: var(--font-weight-semibold);
  font-size: var(--text-base);
}

.alert__message {
  font-size: var(--text-sm);
  margin-top: var(--space-1);
}
```

### 11.3 Accessibilité

- Les alertes dynamiques (apparaissent après le chargement) utilisent `role="alert"` pour une annonce immédiate.
- Les alertes statiques (déjà visibles au chargement) utilisent `role="status"` ou pas de rôle.

---

## 12. Dropdown / Menu — `.dropdown`

> **Implémentation recommandée** : Popover API + CSS Anchor Positioning (Baseline 2024 / Chrome 125+).
> Ces deux APIs rendent le menu dans le **top layer** du navigateur : ni `overflow: hidden`, ni `contain: layout` ne peuvent le clipper ou capturer son positionnement.
> Voir `07-regles-code.md §3.11` pour le détail du problème et de la solution.

### 12.1 Markup

Chaque instance nécessite un `id` unique sur le menu. L'`anchor-name` lie visuellement le menu à son trigger — c'est une exception documentée aux inline styles (voir `07-regles-code.md §3.7`).

```html
<!-- Dropdown standard — menu aligné à gauche du trigger -->
<div class="dropdown">
  <button class="btn" data-variant="secondary" type="button"
          popovertarget="menu-actions"
          style="anchor-name: --anc-actions"
          aria-expanded="false" aria-haspopup="menu"
          data-js-dropdown-trigger>
    Actions
    <svg class="icon" aria-hidden="true">...</svg>
  </button>
  <div class="dropdown__menu" id="menu-actions" popover
       style="position-anchor: --anc-actions"
       role="menu">
    <button class="dropdown__item" role="menuitem" type="button">Modifier</button>
    <button class="dropdown__item" role="menuitem" type="button">Dupliquer</button>
    <hr class="dropdown__separator">
    <button class="dropdown__item" role="menuitem" type="button"
            data-variant="danger">Supprimer</button>
  </div>
</div>

<!-- Dropdown aligné à droite (fin de toolbar, actions d'une ligne) -->
<div class="dropdown">
  <button class="btn" data-variant="ghost" type="button"
          popovertarget="menu-sort"
          style="anchor-name: --anc-sort"
          aria-expanded="false" aria-haspopup="menu"
          data-js-dropdown-trigger>
    Trier
    <svg class="icon" aria-hidden="true">...</svg>
  </button>
  <div class="dropdown__menu dropdown__menu--end" id="menu-sort" popover
       style="position-anchor: --anc-sort"
       role="menu">
    <button class="dropdown__item dropdown__item--active" role="menuitem" type="button">Par date</button>
    <button class="dropdown__item" role="menuitem" type="button">Par nom</button>
  </div>
</div>
```

### 12.2 CSS

```css
/* Pas de position: relative — le menu est dans le top layer (Popover API),
   aucun ancêtre ne peut le clipper via overflow ni contain. */
.dropdown {
  display: inline-flex;
}

/* Reset des styles UA du popover + positionnement ancré */
.dropdown__menu[popover] {
  margin: 0;
  border: none;
  color: inherit;

  /* CSS Anchor Positioning (Chrome 125+, Edge 125+) */
  position: fixed;
  inset: unset;
  top: anchor(bottom);
  left: anchor(left);
  margin-block-start: var(--space-1);
  position-try-fallbacks: flip-block; /* bascule au-dessus si pas de place en bas */

  min-width: 11rem;
  padding: var(--space-2) var(--space-1);
  background-color: var(--color-bg-default);
  border: var(--border-width-default) solid var(--color-border-default);
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-dropdown);
  z-index: var(--z-dropdown);
}

/* Menu aligné à droite du trigger */
.dropdown__menu--end[popover] {
  left: unset;
  right: anchor(right);
}

.dropdown__item {
  display: flex;
  align-items: center;
  gap: var(--gap-sm);
  width: 100%;
  padding: var(--padding-sm) var(--padding-md);
  font-size: var(--text-sm);
  color: var(--color-text-default);
  text-align: left;
  white-space: nowrap;
  border-radius: var(--radius-sm);
  cursor: pointer;
  transition: background-color var(--duration-instant) var(--ease-default);
}
.dropdown__item .icon { color: var(--color-text-muted); }

.dropdown__item:hover,
.dropdown__item:focus-visible {
  background-color: var(--color-bg-subtle);
}

.dropdown__item--active {
  color: var(--color-primary);
  font-weight: var(--font-weight-medium);
}

.dropdown__item[data-variant="danger"] { color: var(--color-danger-text); }
.dropdown__item[data-variant="danger"]:hover { background-color: var(--color-danger-subtle); }

.dropdown__separator {
  height: 0;
  margin: var(--space-2) var(--padding-md);
  border: 0;
  border-top: var(--border-width-default) solid var(--color-border-subtle);
}

/* Rotation du chevron quand le menu est ouvert */
[data-js-dropdown-trigger] .icon:last-child {
  transition: transform var(--duration-fast) var(--ease-default);
}
[data-js-dropdown-trigger][aria-expanded="true"] .icon:last-child {
  transform: rotate(180deg);
}
```

### 12.3 Comportement JS

Le Popover API gère nativement : light-dismiss (clic extérieur), Escape, et une seule instance ouverte à la fois (type `auto` par défaut). Le JS se limite à synchroniser `aria-expanded` et fermer le menu au clic d'un item.

```js
// js/components/dropdown.js
export function initDropdowns(root = document) {
  // Synchronise aria-expanded via l'événement toggle du Popover API
  root.querySelectorAll('[popover].dropdown__menu').forEach(menu => {
    menu.addEventListener('toggle', e => {
      const trigger = root.querySelector(`[popovertarget="${menu.id}"]`);
      if (trigger) {
        trigger.setAttribute('aria-expanded', e.newState === 'open' ? 'true' : 'false');
      }
    });
  });

  // Ferme le menu quand un item est cliqué
  root.addEventListener('click', e => {
    const item = e.target.closest('.dropdown__item');
    if (item) item.closest('[popover]')?.hidePopover();
  });
}
```

> **Navigation clavier (production)** : la navigation ArrowDown/ArrowUp entre les `[role="menuitem"]` n'est pas gérée par le Popover API et doit être ajoutée manuellement pour une conformité WCAG 2.2 complète. Elle est hors scope des maquettes de prototypage.

### 12.4 Accessibilité

- Le trigger porte `aria-expanded` (géré via JS `toggle` event) et `aria-haspopup="menu"`.
- Le menu porte `role="menu"`, chaque item porte `role="menuitem"` et `type="button"`.
- Escape et clic extérieur ferment le menu **nativement** via le Popover API (type `auto`).
- Les `id` de menus et `popovertarget` correspondants doivent être **uniques dans le document**.
- Les actions destructives sont **en dernière position**, séparées par `.dropdown__separator`, avec `data-variant="danger"`.

---

## 13. Tabs — `.tabs`

### 13.1 Markup

```html
<div class="tabs" data-js-tabs>
  <div class="tabs__list" role="tablist" aria-label="Sections">
    <button class="tabs__tab" role="tab" aria-selected="true"
            aria-controls="panel-general" id="tab-general">
      Général
    </button>
    <button class="tabs__tab" role="tab" aria-selected="false"
            aria-controls="panel-security" id="tab-security" tabindex="-1">
      Sécurité
    </button>
    <button class="tabs__tab" role="tab" aria-selected="false"
            aria-controls="panel-notifs" id="tab-notifs" tabindex="-1">
      Notifications
    </button>
  </div>
  <div class="tabs__panel" role="tabpanel" id="panel-general"
       aria-labelledby="tab-general">
    <!-- Contenu -->
  </div>
  <div class="tabs__panel" role="tabpanel" id="panel-security"
       aria-labelledby="tab-security" hidden>
    <!-- Contenu -->
  </div>
  <div class="tabs__panel" role="tabpanel" id="panel-notifs"
       aria-labelledby="tab-notifs" hidden>
    <!-- Contenu -->
  </div>
</div>
```

### 13.2 CSS

```css
.tabs__list {
  display: flex;
  gap: 0;
  border-bottom: var(--border-width-default) solid var(--color-border-default);
}

.tabs__tab {
  padding: var(--padding-sm) var(--padding-md);

  font-size: var(--text-sm);
  font-weight: var(--font-weight-medium);
  color: var(--color-text-muted);
  white-space: nowrap;

  border-bottom: var(--border-width-thick) solid transparent;
  margin-bottom: calc(-1 * var(--border-width-default));

  cursor: pointer;
  transition: color var(--duration-fast) var(--ease-default),
              border-color var(--duration-fast) var(--ease-default);
}

.tabs__tab:hover {
  color: var(--color-text-default);
}

.tabs__tab[aria-selected="true"] {
  color: var(--color-primary);
  border-bottom-color: var(--color-primary);
}

.tabs__panel {
  padding: var(--padding-lg) 0;
}

.tabs__panel[hidden] {
  display: none;
}
```

### 13.3 Comportement JS

```js
// js/components/tabs.js
export function initTabs(root) {
  const tabs = root.querySelectorAll('[role="tab"]');
  const panels = root.querySelectorAll('[role="tabpanel"]');

  function activate(tab) {
    tabs.forEach(t => {
      t.setAttribute('aria-selected', 'false');
      t.setAttribute('tabindex', '-1');
    });
    panels.forEach(p => p.hidden = true);

    tab.setAttribute('aria-selected', 'true');
    tab.removeAttribute('tabindex');

    const panel = root.querySelector('#' + tab.getAttribute('aria-controls'));
    if (panel) panel.hidden = false;
  }

  tabs.forEach(tab => {
    tab.addEventListener('click', () => activate(tab));
  });

  // Navigation clavier : flèches gauche/droite
  root.querySelector('[role="tablist"]').addEventListener('keydown', (e) => {
    const tabArr = [...tabs];
    const index = tabArr.indexOf(document.activeElement);

    if (e.key === 'ArrowRight') {
      e.preventDefault();
      const next = tabArr[(index + 1) % tabArr.length];
      next.focus();
      activate(next);
    }
    if (e.key === 'ArrowLeft') {
      e.preventDefault();
      const prev = tabArr[(index - 1 + tabArr.length) % tabArr.length];
      prev.focus();
      activate(prev);
    }
  });
}
```

### 13.4 Accessibilité

- Le conteneur d'onglets porte `role="tablist"` avec `aria-label`.
- Chaque onglet porte `role="tab"`, `aria-selected` et `aria-controls`.
- Chaque panneau porte `role="tabpanel"` et `aria-labelledby`.
- Seul l'onglet actif est dans le flux de tabulation. Les autres portent `tabindex="-1"`.
- Navigation par flèches gauche/droite dans la tablist.

---

## 14. Tooltip — `.tooltip`

### 14.1 Markup

```html
<button class="btn" data-variant="ghost" data-size="sm"
        aria-describedby="tooltip-help">
  <svg class="btn__icon" aria-hidden="true">...</svg>
  <span class="sr-only">Aide</span>
</button>
<div class="tooltip" role="tooltip" id="tooltip-help" hidden>
  Cliquez pour ouvrir le centre d'aide.
</div>
```

### 14.2 CSS

```css
/* Exception CSS Containment : contain: content retiré intentionnellement.
   Le tooltip déborde intentionnellement du composant déclencheur.
   contain: content impliquerait overflow: hidden et silencerait ce débordement.
   Voir 07-regles-code.md §3.9. */
.tooltip {
  position: absolute;
  z-index: var(--z-tooltip);

  padding: var(--padding-xs) var(--padding-sm);
  max-width: 15rem;

  font-size: var(--text-xs);
  line-height: var(--leading-normal);
  color: var(--color-text-inverse);

  background-color: var(--color-bg-inverse);
  border-radius: var(--radius-sm);
  box-shadow: var(--shadow-md);

  pointer-events: none;
  opacity: 0;
  transition: opacity var(--duration-fast) var(--ease-default);
}

.tooltip:not([hidden]) {
  opacity: 1;
}
```

### 14.3 Accessibilité

- Le tooltip porte `role="tooltip"` et un `id`.
- L'élément déclencheur porte `aria-describedby` pointant vers le tooltip.
- Le tooltip est affiché au hover **et** au focus du déclencheur.
- Le tooltip n'est **jamais** le seul moyen d'accéder à une information critique.

---

## 15. Accordion / Section repliable — `<details>`

> Utiliser `<details>` + `<summary>` comme composant natif de section repliable. Le toggle ouvert/fermé est entièrement géré par le navigateur — **zéro JS requis**.

### 15.1 Markup

```html
<!-- Section ouverte par défaut (attribut open) -->
<details class="accordion" open>
  <summary class="accordion__header">
    <svg class="icon accordion__chevron" aria-hidden="true">
      <use href="#icon-chevron-down"></use>
    </svg>
    <span class="accordion__title">Titre de la section</span>
    <span class="accordion__count" aria-label="3 éléments">3</span>
  </summary>
  <div class="accordion__body">
    <!-- Contenu -->
  </div>
</details>

<!-- Section fermée par défaut (sans attribut open) -->
<details class="accordion">
  <summary class="accordion__header">
    <svg class="icon accordion__chevron" aria-hidden="true">
      <use href="#icon-chevron-down"></use>
    </svg>
    <span class="accordion__title">Titre replié</span>
  </summary>
  <div class="accordion__body">
    <!-- Contenu -->
  </div>
</details>
```

### 15.2 CSS

```css
/* Reset du marker natif de <summary> (triangle du navigateur) */
.accordion__header { list-style: none; }
.accordion__header::-webkit-details-marker { display: none; }

.accordion__header {
  display: flex;
  align-items: center;
  gap: var(--gap-sm);
  padding: var(--padding-sm) var(--padding-md);
  background-color: var(--color-bg-subtle);
  border-bottom: var(--border-width-default) solid var(--color-border-subtle);
  border-radius: var(--radius-md) var(--radius-md) 0 0;
  cursor: pointer;
}

/* Quand replié : plus de bordure basse, border-radius complet */
details.accordion:not([open]) > .accordion__header {
  border-bottom: none;
  border-radius: var(--radius-md);
}

.accordion__chevron {
  width: 0.875rem;
  height: 0.875rem;
  flex-shrink: 0;
  color: var(--color-text-muted);
  transition: transform var(--duration-fast) var(--ease-default);
}
.accordion__header:hover .accordion__chevron { color: var(--color-text-default); }

/* Chevron pointant à droite quand la section est repliée */
details.accordion:not([open]) > .accordion__header .accordion__chevron {
  transform: rotate(-90deg);
}

.accordion__title {
  flex: 1;
  font-size: var(--text-sm);
  font-weight: var(--font-weight-semibold);
  color: var(--color-text-default);
}

.accordion__count {
  font-size: var(--text-xs);
  color: var(--color-text-muted);
  font-weight: var(--font-weight-medium);
  background-color: var(--color-bg-muted);
  padding: 0 var(--space-2);
  border-radius: var(--radius-full);
  min-width: 1.25rem;
  text-align: center;
}
```

> **`border-radius` sur les enfants, pas `overflow: hidden` sur le parent.** Si le corps de l'accordion contient des dropdowns, ne pas utiliser `overflow: hidden` sur `.accordion` — cela clipperait les menus. Appliquer les arrondis sur `.accordion__header` et le dernier enfant de `.accordion__body` directement. Voir `07-regles-code.md §3.11`.

### 15.3 Accessibilité

- Le rôle ARIA implicite de `<summary>` est `button` avec `aria-expanded` géré nativement par le navigateur — **ne pas l'ajouter manuellement**.
- Le reset `::-webkit-details-marker` / `list-style: none` supprime le triangle visuel sans nuire à l'accessibilité : le rôle et l'état restent dans l'arbre d'accessibilité.
- Utiliser l'attribut `open` pour les sections ouvertes par défaut (pas de JS).
- Si le `<summary>` contient uniquement une icône chevron sans texte visible, ajouter `aria-label` sur le `<details>` ou un texte `.sr-only` dans le `<summary>`.

---

## 16. Récapitulatif des composants

| Composant          | Fichier CSS           | Fichier JS            | Interactif ? | Section |
| ------------------ | --------------------- | --------------------- | ------------ | ------- |
| Button             | `button.css`          | —                     | Non*         | 2       |
| Input              | `input.css`           | —                     | Non*         | 3       |
| Textarea           | `textarea.css`        | —                     | Non*         | 4       |
| Select             | `select.css`          | —                     | Non*         | 5       |
| Checkbox           | `checkbox.css`        | —                     | Non*         | 6       |
| Radio              | `radio.css`           | —                     | Non*         | 6       |
| Toggle             | `toggle.css`          | —                     | Non*         | 7       |
| Card               | `card.css`            | —                     | Non          | 8       |
| Data Table         | `data-table.css`      | `data-table.js`       | Oui          | 9       |
| Badge              | `badge.css`           | —                     | Non          | 10      |
| Alert              | `alert.css`           | —                     | Non          | 11      |
| Dropdown           | `dropdown.css`        | `dropdown.js`         | Oui (Popover)| 12      |
| Tabs               | `tabs.css`            | `tabs.js`             | Oui          | 13      |
| Tooltip            | `tooltip.css`         | `tooltip.js`          | Oui          | 14      |
| Accordion/Repliable| `accordion.css`       | — (natif)             | Non*         | 15      |

_*Les contrôles de formulaire sont interactifs par nature HTML mais ne nécessitent pas de JS custom pour leur comportement de base. L'Accordion utilise `<details>/<summary>` natif._

---

## 17. Versioning de ce document

| Version | Date       | Changement                                                   |
| ------- | ---------- | ------------------------------------------------------------ |
| 0.1     | 2026-03-25 | Création initiale                                            |
| 0.2     | 2026-04-11 | Ajout convention CSS Containment (item 8)                    |
| 0.3     | 2026-04-17 | §12 Dropdown : migration Popover API + CSS Anchor Positioning |
| 0.3     | 2026-04-17 | §15 Accordion : ajout `<details>/<summary>` comme composant natif |
