# Session — Todos

## Métadonnées

- Version maquette : 0.4
- Version DS ciblée : DS 0.4
- Phase courante : 5 — Catalogue (cycle complété)
- Date dernière session : 2026-04-17

## Décisions prises

- 2026-04-16 — App Shell B (Sidebar) retenu
- 2026-04-16 — Sidebar : 3 sections (Vues système · Projets · Catégories) — option C
- 2026-04-16 — Groupement des tâches par priorité (défaut)
- 2026-04-16 — Filtre actif affiché en `data-variant="primary"` avec icône ×
- 2026-04-16 — Bouton d'action (···) visible uniquement au hover de la ligne
- 2026-04-16 — Poignée drag & drop visible uniquement au hover
- 2026-04-16 — Sous-tâches en indent avec bordure gauche sur `.task-row__subtasks`
- 2026-04-16 — Tâche en retard : date en `--color-danger-text`
- 2026-04-16 — Tâche en cours : titre en `--color-primary`
- 2026-04-16 — Tâche terminée : opacité réduite + barré sur le titre
- 2026-04-16 — Sidenav : couleur projet/catégorie via `--dot-color` sur `.sidenav__color-dot`

## Composants identifiés

| Composant | Statut | Provenance |
| --------- | ------ | ---------- |
| `btn` | existant | catalog (ready-for-dev) |
| `badge` | existant DS | prototype dans cet écran |
| `input` | existant DS | prototype dans cet écran |
| `checkbox` | existant DS | prototype dans cet écran |
| `dropdown` | existant DS | prototype dans cet écran |
| `task-row` | nouveau | screens/todos v0.3 |
| `task-group` | nouveau | screens/todos v0.3 |
| `task-subtask` | nouveau | screens/todos v0.3 |
| `sidenav` (étendu) | extension DS | color-dot, group-header |

## Points ouverts

- [ ] Décider du comportement drawer de détail (Phase 4 ou 5)
- [ ] États hover complets à vérifier dans le navigateur
- [ ] Couleurs dot projets/catégories : `--palette-*` en inline style = exception prototype documentée (couleurs user-defined, pas de token sémantique applicable)

## Décisions Phase 4

- 2026-04-16 — `contain: layout style` sur `.task-row` (contain:paint omis — dropdown déborde volontairement)
- 2026-04-16 — `.avatar` corrigé : `--palette-*` → `--color-primary` / `--color-text-on-primary`
- 2026-04-16 — `.topbar__toggle:hover` corrigé : `rgb()` brut → `--color-topbar-hover`
- 2026-04-16 — Tokens manquants ajoutés : `--radius-full`, `--shadow-md`, `--z-dropdown`
- 2026-04-16 — Dates enveloppées dans `<time datetime="…">`
- 2026-04-16 — Bouton "Exporter" disabled ajouté (démontre l'état désactivé)
- 2026-04-17 — `contain` retiré de `.task-row` (contain:layout brise le positionnement absolu des dropdowns)
- 2026-04-17 — `overflow: hidden` retiré de `.task-group` ; border-radius appliqué sur `__header` et dernier `.task-row`
- 2026-04-17 — `.task-row[data-state="done"] .task-row__project` : pointer-events none + bg transparent
- 2026-04-17 — Dropdowns : `--shadow-dropdown`, `--radius-lg`, icônes en muted, chevron animé au clic

## Fichiers modifiés cette session

- index.html (Phase 4 — corrections finition)
- layout.css (tokens manquants : radius-full, shadow-md, z-dropdown)
- components.css (violations tokens corrigées, contain ajouté)
- session.md (mis à jour Phase 4)
