# CLAUDE.md — Covenant Design System

## Skill actif

Le skill `covenant-design-system` doit être invoqué pour **toute tâche UI** dans ce dépôt : création ou modification de pages HTML, composants, layouts, styles, tokens, icônes, accessibilité, ou revue de conformité.

## Démarrage d'une session de maquettage

Lire `START.md` en **premier**, avant toute autre action. Ce fichier définit l'ordre exact de chargement des artefacts et du Design System.

## Démarrage d'une session d'implémentation

Lire `START-IMPLEMENTATION.md` en premier. Ce fichier définit le protocole pour transformer une maquette en code de production conforme au Design System.

## Stack technique — contraintes non négociables

- HTML5 sémantique
- CSS natif avec custom properties (tokens DS uniquement — aucune valeur brute)
- JavaScript vanilla ES2024+, modules ES (`type="module"`)
- **Aucun framework, pré-processeur ou transpileur en production**
- Compatibilité : Web Platform Baseline "Newly Available"
- Navigateurs cibles : Chrome, Opera (Chromium)
- Accessibilité : WCAG 2.2 niveau AA

## Version DS courante

Voir `design-system/VERSION.md` — actuellement **DS 0.4**.

## Structure du dépôt

```
.
├── CLAUDE.md                        ← CE FICHIER
├── START.md                         ← Protocole de démarrage (design)
├── START-IMPLEMENTATION.md          ← Protocole de démarrage (implémentation)
├── WORKFLOW-DESIGN-MAQUETTE.md      ← Workflow complet de conception
├── manifest.md                      ← État global (écrans × versions DS)
├── app-shells.md                    ← Déclinaisons App Shell disponibles
├── catalog.md                       ← Registre des composants
├── catalog/[composant]/index.html   ← Catalogues vivants (créés en Phase 5)
├── screens/[nom-ecran]/             ← Artefacts par écran (créés en Phase 1)
└── design-system/
    ├── VERSION.md                   ← Version DS courante et changelog
    ├── SKILL.md                     ← Point d'entrée du skill Claude
    └── references/                  ← Documents de référence (00 à 08)
```

## Règles critiques à appliquer sans exception

1. Tokens DS uniquement — aucune valeur brute (`#fff`, `16px`, `200ms`)
2. BEM pour la structure (`block__element`), `data-*` pour les variantes/états
3. Jamais de classes modifier pour les états (`btn--primary` est interdit — utiliser `data-variant="primary"`)
4. `data-js-*` comme hooks JavaScript, jamais les classes CSS
5. Native `<dialog>` pour toutes les modales et drawers
6. Un seul bouton `primary` par section visible
7. Focus visible obligatoire via technique double anneau (voir `07-regles-code.md §3.5`)
8. `contain: content` sur les composants autonomes (avec exceptions documentées)

## Répertoires créés à la demande

`catalog/` et `screens/` sont créés progressivement au fil du workflow. Leur absence dans le dépôt est l'état initial attendu — ne pas les créer vides.
