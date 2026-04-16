# START — Protocole de démarrage de session

> Ce fichier est le point d'entrée de chaque session de travail sur les maquettes.
> Il doit être chargé en premier dans le contexte de l'agent IA avant toute autre action.

---

## 1. Charger les artefacts persistants

Lire les quatre fichiers suivants dans cet ordre :

1. `WORKFLOW-DESIGN-MAQUETTE.md` - Description du workflow et du format des fichiers
2. `manifest.md` — état global du projet (écrans, versions, phases)
3. `app-shells.md` — déclinaisons App Shell disponibles
4. `catalog.md` — registre des composants existants

---

## 2. Identifier l'écran en cours de travail

- Si l'utilisateur a précisé l'écran dans son message → utiliser ce nom.
- Sinon → demander : "Sur quel écran travaille-t-on aujourd'hui ?"

---

## 3. Charger le contexte de l'écran

Si l'écran existe déjà dans `screens/` :

```
screens/[nom-ecran]/session.md       ← lire en premier (phase courante, décisions)
screens/[nom-ecran]/brief.md         ← contexte fonctionnel
screens/[nom-ecran]/index.html       ← prototype courant
screens/[nom-ecran]/layout.css       ← styles de mise en page
screens/[nom-ecran]/components.css   ← styles composants
```

Si c'est un nouvel écran → passer directement à la Phase 1 (Brief).

---

## 4. Charger le Design System en mode design

Lire les fichiers DS dans cet ordre :

| Ordre | Fichier                                         | Sections prioritaires                     |
| ----- | ----------------------------------------------- | ----------------------------------------- |
| 1     | `design-system/references/00-fondations.md`     | §2 Périmètre, §3 Principes, §4 Responsive |
| 2     | `design-system/references/01-design-tokens.md`  | Intégral                                  |
| 3     | `design-system/references/02-layout.md`         | Intégral                                  |
| 4     | `design-system/references/03-composants.md`     | Selon composants identifiés               |
| 5     | `design-system/references/04-patterns.md`       | Selon patterns identifiés                 |
| 6     | `design-system/references/05-etats-feedback.md` | Intégral                                  |
| 7     | `design-system/references/06-iconographie.md`   | Intégral                                  |

**Ne pas charger** : `07-regles-code.md`, `08-checklist.md` — hors scope en phase de design.

---

## 5. Identifier la phase courante

Lire `session.md` → champ **"Phase courante"**.

| Phase | Nom         | Critère de démarrage                                |
| ----- | ----------- | --------------------------------------------------- |
| 1     | Brief       | Nouvel écran — aucun `brief.md` existant            |
| 2     | Squelette   | `brief.md` validé                                   |
| 3     | Composition | Structure de zones validée                          |
| 4     | Finition    | Tous les composants placés, données représentatives |
| 5     | Catalogue   | Prototype finalisé et validé                        |

En cas de doute → demander confirmation à l'utilisateur.

---

## 6. Rappel des contraintes fixes

Ces règles s'appliquent à **tous les fichiers produits** sans exception :

- Aucun framework CSS, JS ou WebComponent (Bootstrap, Tailwind, Vue, Lit, etc.)
- Tokens DS uniquement — aucune valeur brute (`#fff`, `16px`, `200ms`)
- BEM sur tous les composants dès la Phase 3 — classes `block__element` pour la structure, attributs `data-variant` / `data-size` / `data-*` pour les variantes et états. **Jamais de classes `--modifier` pour les états** (ex : utiliser `data-variant="primary"`, pas `.btn--primary`)
- `data-js-*` posés comme marqueurs d'intention, **non implémentés** (hors scope design)
- App Shell : header et footer statusbar **obligatoires** dans toutes les déclinaisons
- Données statiques **réalistes** — pas de Lorem ipsum, pas de "Item 1 / Item 2"
- Consulter `catalog.md` **avant** de créer un nouveau composant

---

## 7. Résumé de démarrage

Avant de commencer à travailler, confirmer à l'utilisateur :

```
Contexte chargé :
- Écran : [nom]
- Phase courante : [x]
- Version DS ciblée : [x.x]
- Composants existants disponibles : [liste courte depuis catalog.md]
- Points ouverts de la session précédente : [depuis session.md]
```
