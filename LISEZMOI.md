# Points Récompenses : installation et synchro

## A. Mettre l'appli en ligne (déjà fait si tu as installé la v1)
1. Compte sur github.com, nouveau dépôt **Public** nommé `points`.
2. « Add file » puis « Upload files » : dépose tous les fichiers du zip (pas le zip lui-même), puis « Commit changes ».
3. « Settings » puis « Pages » : « Deploy from a branch », branche `main`, dossier `/ (root)`, « Save ».
4. L'appli est à l'adresse `https://TON-PSEUDO.github.io/points/`. Sur chaque téléphone : Chrome, menu ⋮, « Installer l'application ».

## B. Créer la base Firebase (une seule fois, gratuit, sans carte bancaire)
1. Va sur console.firebase.google.com avec ton compte Google, « Créer un projet », nom `points-famille`. Google Analytics : désactivé.
2. Sur la page du projet, clique sur l'icône Web `</>` pour ajouter une appli. Surnom : `Points`. Ne coche pas Hosting.
   Firebase affiche un bloc `const firebaseConfig = { apiKey: "...", ... }`. Garde cette page ouverte.
3. Menu de gauche : « Firestore Database », « Créer une base de données ». Emplacement : un emplacement en Europe (par exemple `europe-west9` Paris). Mode : **production**.
4. Dans Firestore, onglet « Règles » : efface tout, colle le contenu du fichier `firestore.rules`, puis « Publier ».

## C. Brancher l'appli sur Firebase
1. Sur GitHub, dépose les nouveaux fichiers du zip (ils remplacent les anciens).
2. Ouvre `firebase-config.js` dans le dépôt, clique sur le crayon ✏️, remplace les valeurs vides par celles de l'étape B.2, puis « Commit changes ».
3. Attends une minute que GitHub publie.

## D. Relier les deux téléphones
1. **Ton téléphone** (celui qui a les bons points) : ouvre l'appli avec internet, ferme-la et rouvre-la pour charger la nouvelle version (« Version 1.1.0 » en bas des Réglages).
   Réglages, « Créer l'espace famille ». Un code de 12 caractères s'affiche.
2. **Téléphone de la maman** : installe l'appli (étape A.4), Réglages, « J'ai déjà un code famille », saisis le code (bouton « Partager le code » pour l'envoyer par SMS).
3. C'est fini : un point ajouté sur un téléphone apparaît sur l'autre en une seconde. Sans internet, l'appli continue de marcher et envoie les changements au retour du réseau.

## Mises à jour
Remplace les fichiers sur GitHub, sauf `firebase-config.js` (pour ne pas effacer ta configuration). Ne change jamais l'adresse ni le nom du dépôt.

## Bon à savoir
- Le code famille fonctionne comme un mot de passe : toute personne qui le connaît peut modifier les points.
- Le plan gratuit de Firebase permet des dizaines de milliers de modifications par jour, bien au-delà de l'usage d'une famille.
- La sauvegarde (Réglages, « Exporter ») reste utile en cas de souci.
