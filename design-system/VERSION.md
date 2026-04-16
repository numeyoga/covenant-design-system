# Design System — Version

**Version courante : DS 0.4**

Cette version est la valeur à utiliser dans les champs `brief.md` et `session.md` :

```markdown
## Version DS ciblée
DS 0.4
```

---

## Correspondance des documents

| Document              | Version doc | Dernière mise à jour |
| --------------------- | ----------- | -------------------- |
| `00-fondations.md`    | 0.4         | 2026-04-15           |
| `01-design-tokens.md` | 0.1         | 2026-03-25           |
| `02-layout.md`        | 0.2         | 2026-04-11           |
| `03-composants.md`    | 0.1         | 2026-03-25           |
| `04-patterns.md`      | 0.1         | 2026-03-25           |
| `05-etats-feedback.md`| 0.1         | 2026-03-25           |
| `06-iconographie.md`  | 0.1         | 2026-03-25           |
| `07-regles-code.md`   | 0.4         | 2026-04-16           |
| `08-checklist.md`     | 0.3         | 2026-04-11           |

---

## Changelog DS

| Version DS | Date       | Changement principal                                               | Impact maquettes           |
| ---------- | ---------- | ------------------------------------------------------------------ | -------------------------- |
| DS 0.1     | 2026-03-25 | Création initiale — tous les documents de base                     | Aucune maquette existante  |
| DS 0.2     | 2026-03-25 | Viewport adapté à l'écran 2560×1440, outillage CLI Baseline        | Aucune maquette existante  |
| DS 0.3     | 2026-04-11 | Status bar standard, scroll containment, CSS Containment, @supports| Regénérer l'App Shell      |
| DS 0.4     | 2026-04-15 | Principe Intentionnalité (00§6.7), focus double anneau (07§3.5)    | Mettre à jour les :focus   |

---

## Politique de version

La version DS est incrémentée lors de tout changement qui affecte le markup ou le CSS produit par un agent. Les changements purement documentaires (clarifications, exemples) n'incrémentent pas la version.
