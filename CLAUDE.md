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
- Local : `localStorage['points-recompenses:v1']` = `{version, kids[], rewards[], history[]}`.
  - kid : `{id, name, points, color '#rrggbb', icon:{type:'emoji'|'image', value}}` (image = dataURL webp 256 px).
  - reward : `{id, name, price, emoji}` ; history : `{id, ts, kidId, type:'add'|'sub'|'buy', delta, label}`.
- Code famille : `localStorage['points-recompenses:famille']`.
- Firestore : doc `families/{code}` = `{kids:{id:{name,points,color,icon,order}}, rewards:{id:{...}}, updatedAt}` + sous-collection `history/{id}`.
  Les points changent via `increment()` (pas de conflit si les 2 parents tapent en même temps).
- **Ne jamais changer les clés localStorage ni l'URL du site** : c'est ce qui garantit que les points survivent aux mises à jour.

## Architecture JS (index.html)
- Fonctions `op*` (opPoints, opUndo, opKid, opAddKid, opDelKid, opSaveReward, opDelReward, opImport) : en local elles modifient `state` puis `save()` + `render()` ; en mode synchro elles écrivent dans Firestore et c'est `onSnapshot` qui met à jour `state` et l'affichage.
- `render()` reconstruit la vue courante (onglets points / rewards / history / settings), et ne s'exécute pas pendant la saisie d'un prénom.
- Les +1 rapprochés (moins de 2 min, même enfant) sont regroupés en une seule ligne d'historique.

## Fonctionnalités en place (v1.1.0)
Compteur +1 / −1 par enfant, échange de récompenses avec confirmation, barre « encore X pour… », CRUD récompenses,
historique avec annulation, couleur (26 préréglées + sélecteur libre), icône emoji ou image importée de la galerie,
ajout/suppression d'enfants, export/import JSON, synchro Firebase avec indicateur d'état.

## Contraintes
- Ne pas intégrer d'images ou de dessins de personnages protégés (Pokémon, Peppa Pig, Grizzy et les Lemmings…) :
  l'utilisateur importe ses propres images depuis sa galerie.
- Incrémenter `APP_VERSION` à chaque changement.

## État et prochaines étapes
- Mode local testé (navigateur headless). **La synchro Firebase n'a pas encore été testée contre un vrai projet.**
- À faire avec l'utilisateur : créer le projet Firebase (voir LISEZMOI, partie B), remplir `firebase-config.js`,
  publier sur GitHub Pages (git push), puis tester la création de l'espace famille et la jonction du 2e téléphone.
