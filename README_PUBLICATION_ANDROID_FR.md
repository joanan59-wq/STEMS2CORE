# STEM2CORE — dossier de publication Android

## Ce dossier est prêt à être envoyé dans un dépôt GitHub
Le dépôt contient l'interface web, la PWA Android et le backend Docker. Le fichier `render.yaml` définit deux services Render : API et site statique.

## Étapes de publication
1. Créer un dépôt GitHub privé nommé `stem2core`.
2. Ajouter le contenu de ce dossier à la racine du dépôt (pas le dossier parent).
3. Dans Render, choisir **New > Blueprint**, connecter le dépôt GitHub et laisser Render lire `render.yaml`.
4. Une fois les services créés, relever l'URL `https://stem2core-api.onrender.com` et l'URL du site.
5. Mettre à jour `STEM2CORE_CORS_ORIGINS` avec l'URL exacte du site, puis redéployer l'API.
6. Vérifier `https://stem2core-api.onrender.com/health`.
7. Ouvrir le site HTTPS dans Chrome Android, menu ⋮, puis **Installer l'application** ou **Ajouter à l'écran d'accueil**.

## Pré-requis de compte
GitHub et Render nécessitent que le propriétaire se connecte et autorise la liaison. Aucun identifiant ni mot de passe ne doit être envoyé dans le chat. L'assistant ne peut pas créer le dépôt ou déployer dans ces comptes sans cette autorisation.

## Vérifications honnêtes avant publication
- Le fichier CSS manquant dans l'archive précédente a été ajouté.
- PWA : manifeste, icônes 192/512, service worker.
- API : Dockerfile et dépendances inclus.
- Le moteur de séparation reste en mode `mock` : aucune séparation IA réelle n'est activée.
- L'analyse polyphonique actuelle est expérimentale, non fiable sur un mix complet.
- Aucun test de déploiement Render, test réel sur téléphone Android, ni APK/AAB signé n'a encore été effectué.
- Le frontend fourni est le shell de travail local ; son import et ses exports ne prouvent pas que le pipeline complet de transcription est connecté de bout en bout.
- Avant toute mise en ligne publique, prévoir authentification, quotas, limites de taille et politique de conservation/suppression des fichiers audio. Le backend ne doit pas être présenté comme service public de production dans son état actuel.

## Politique audio
Les fichiers ne doivent être envoyés au serveur qu'après une action explicite de l'utilisateur. Prévoir une confirmation claire avant analyse distante et une politique de suppression des fichiers temporaires côté serveur.
