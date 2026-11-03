# Points Récompenses : contexte du projet

App familiale pour suivre les points de 2 enfants dans un système de récompenses.
Utilisée sur 2 téléphones Android (papa et maman). Réponds en français.
Principe directeur : **toujours la solution la plus simple et la plus facile à maintenir**.

## Choix techniques (déjà validés avec l'utilisateur)
- **PWA** (web app installable via Chrome « Installer l'application »), pas d'appli native ni de Play Store.
- Hébergement **GitHub Pages** (dépôt public `points`), URL `https://PSEUDO.github.io/points/`.
- Une seule page : `index.html` (HTML + CSS + JS inline, sans build, sans framework).
- Synchro entre téléphones : **Firebase Firestore** (plan gratuit), SDK JS 10.12.2 chargé en `import()` dynamique depuis gstatic.
- Pas d'authentification : un « espace famille » identifié par un code secret de 12 caractères (`families/{code}`), règles dans `firestore.rules` (pas de listing possible).

## Fichiers
- `index.html` : toute l'appli.
- `firebase-config.js` : `window.FIREBASE_CONFIG = {...}` propre à l'utilisateur. **Ne jamais l'écraser lors d'une mise à jour.**
- `sw.js` : service worker, réseau d'abord pour l'appli, cache d'abord pour le SDK Firebase (hors ligne).
- `manifest.json`, `icon-*.png` : installation PWA.
- `firestore.rules` : à coller dans la console Firebase.
- `LISEZMOI.md` : guide pas à pas pour l'utilisateur.

## Données
- Local : `localStorage['points-recompenses:v1']` = `{version, kids[], rewards[], actions[], history[]}`.
  - kid : `{id, name, points, color '#rrggbb', icon:{type:'emoji'|'image', value}}` (image = dataURL webp 256 px).
  - reward : `{id, name, price, emoji}` ; action (barème) : `{id, name, points (−999..999, ≠0), emoji}`.
  - history : `{id, ts, kidId, type:'add'|'sub'|'buy', delta, label}` ; `label` non vide = action du barème ou récompense.
- Code famille : `localStorage['points-recompenses:famille']`.
- Firestore : doc `families/{code}` = `{kids:{id:{name,points,color,icon,order}}, rewards:{id:{...}}, actions:{id:{...}}, updatedAt}` + sous-collection `history/{id}`.
  Un espace créé avant la v1.2.0 n'a pas `actions` : le premier téléphone en v1.2.0 y envoie son barème.
  Les points changent via `increment()` (pas de conflit si les 2 parents tapent en même temps).
- **Ne jamais changer les clés localStorage ni l'URL du site** : c'est ce qui garantit que les points survivent aux mises à jour.

## Architecture JS (index.html)
- Fonctions `op*` (opPoints, opUndo, opKid, opAddKid, opDelKid, opSaveItem/opDelItem pour `rewards` et `actions`, opImport) : en local elles modifient `state` puis `save()` + `render()` ; en mode synchro elles écrivent dans Firestore et c'est `onSnapshot` qui met à jour `state` et l'affichage.
- `render()` reconstruit la vue courante (onglets points / rewards / history / settings), et ne s'exécute pas pendant la saisie d'un prénom.
- Les +1 rapprochés (moins de 2 min, même enfant) sont regroupés en une seule ligne d'historique (pas les lignes du barème).
- `*{margin:0}` annule le centrage natif des `<dialog>` : garder `margin:auto` sur `dialog`.

## Fonctionnalités en place (v1.2.0)
Compteur +1 / −1 par enfant, onglet Barème (actions à points positifs ou négatifs, un clic puis choix de l'enfant,
retrait plafonné au solde), échange de récompenses avec confirmation, barre « encore X pour… », CRUD récompenses et barème,
historique avec annulation, couleur (26 préréglées + sélecteur TSV maison : carré saturation/luminosité + barre de teinte),
icône emoji ou image importée de la galerie, ajout/suppression d'enfants, export/import JSON, synchro Firebase avec indicateur d'état.

## Contraintes
- Ne pas intégrer d'images ou de dessins de personnages protégés (Pokémon, Peppa Pig, Grizzy et les Lemmings…) :
  l'utilisateur importe ses propres images depuis sa galerie.
- Incrémenter `APP_VERSION` à chaque changement.

## Publication
- Dépôt public `mazelx/points` (compte GitHub perso), site `https://mazelx.github.io/points/`, publié par `git push` sur `main`.
- Le remote est `https://mazelx@github.com/mazelx/points.git` : le push passe par le compte `mazelx` même si le compte `gh` actif est le pro.
- Commits signés avec l'e-mail noreply perso (config git locale), jamais l'e-mail Betclic.
- Projet Firebase `family-points-d3b46`, règles publiées. GitHub Pages met jusqu'à 10 min à servir un fichier modifié.
- Test local : `.claude/launch.json` (python http.server, port 8765).

## État et prochaines étapes
- Mode local testé (navigateur intégré). Base Firestore et règles vérifiées en lecture.
- **À tester avec l'utilisateur : création de l'espace famille, jonction du 2e téléphone, synchro du barème.**
