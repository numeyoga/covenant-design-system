# Brief — Todos (Liste de tâches)

## Besoin fonctionnel

Écran principal unique d'une application de gestion de tâches personnelles. L'utilisateur gère ses tâches organisées par projets, avec catégories, tags, priorités et dates d'échéance. La vue est contextuelle : elle s'adapte au projet ou à la vue sélectionnée dans la sidebar. L'accent est mis sur la productivité : filtrage, tri, recherche et réorganisation sont des actions de premier rang.

## Données affichées

| Donnée | Type | Volume estimé | Obligatoire |
| ------ | ---- | ------------- | ----------- |
| Titre de la tâche | Texte court (≤ 120 car.) | 1–200 par vue | Oui |
| Statut | Enum : à faire / en cours / terminé | — | Oui |
| Priorité | Enum : haute / normale / basse | — | Non |
| Date d'échéance | Date + heure optionnelle | — | Non |
| Projet | Référence (texte + couleur) | 1–20 projets | Non |
| Catégorie | Texte + couleur | 1–10 catégories | Non |
| Tags | Liste de labels courts | 0–5 par tâche | Non |
| Sous-tâches | Liste de tâches enfants (titre + statut) | 0–10 par tâche | Non |
| Description | Texte long (notes libres) | — | Non |

## Actions utilisateur

| Action | Type | Déclencheur | Destination |
| ------ | ---- | ----------- | ----------- |
| Créer une tâche | Création | Bouton "Nouvelle tâche" (toolbar) ou raccourci `N` | Modal de création |
| Marquer terminée | Toggle | Clic sur checkbox de la tâche | Mise à jour inline |
| Éditer une tâche | Édition | Clic sur le titre / bouton Éditer | Drawer latéral (détail) |
| Supprimer une tâche | Suppression | Menu contextuel → Supprimer | Confirmation inline |
| Réorganiser | Tri manuel | Drag & drop (`data-js-draggable`) | Réordonnancement inline |
| Créer un projet | Création | Bouton "+" dans la sidebar | Modal ou inline sidebar |
| Filtrer | Filtrage | Toolbar filtres (priorité, statut, tag, échéance) | Vue filtrée sans navigation |
| Rechercher | Recherche | Champ de recherche (toolbar) | Vue filtrée en temps réel |
| Trier | Tri | Sélecteur de tri (toolbar) | Réordonnancement de la liste |
| Développer sous-tâches | Affichage | Clic sur indicateur sous-tâches | Expansion inline |

## Contraintes

- Application personnelle : pas de gestion multi-utilisateurs, pas de permissions.
- Vue unique : tous les projets passent par le même écran principal, filtrés par la sidebar.
- Densité : l'utilisateur peut avoir de nombreuses tâches visibles simultanément — la densité doit favoriser la lecture rapide.
- Drag & drop marqué par `data-js-draggable` et `data-js-drop-zone` (non implémenté en maquette).
- Le drawer de détail s'ouvre sans quitter la liste (pas de navigation).

## App Shell

Déclinaison choisie : **B — Sidebar**
Raison : la sidebar porte la navigation par projets/vues (niveau macro) ; la zone principale porte la liste avec filtres et tri (niveau micro). La dualité macro/micro nécessite une sidebar persistante.

## Version DS ciblée

DS 0.4

## Données exemples

| Champ | Exemple réaliste |
| ----- | ---------------- |
| Titre tâche | Préparer la présentation budget Q2 |
| Titre tâche 2 | Relire le contrat fournisseur Azimuth |
| Titre tâche 3 | Mettre à jour les dépendances du projet Atlas |
| Date d'échéance | 18 avr. 2026, 17:00 |
| Projet | Marketing Produit |
| Catégorie | Travail |
| Tags | urgent, client, présentation |
| Priorité | Haute / Normale / Basse |
| Statut | En cours / À faire / Terminé |
| Sous-tâche | Collecter les données de ventes Q1 |
| Sous-tâche 2 | Créer les slides de synthèse |
| Description | Voir les notes du dernier comité de direction. Inclure les projections H2. |

## Design Thinking

**Purpose** — Permettre à un utilisateur de garder une vue claire sur ses tâches en cours, triées par projet et priorité, sans friction.

**Ton** — Dense, sobre, orienté productivité. Chaque pixel compte, pas de décoration superflue.

**Interaction principale** — Cocher une tâche comme terminée. C'est l'action la plus fréquente ; elle doit être instantanée et visuellement satisfaisante.

**Différenciateur** — Navigation à deux niveaux : la sidebar (projet / vue système) + la toolbar (filtres / tri) permettent à l'utilisateur d'affiner sa vue sans jamais changer d'écran ni perdre son contexte.
