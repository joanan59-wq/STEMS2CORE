# STEM2CORE V7.4 — Android import et réseau

## Client Android
Ouvrir `STEM2CORE_V7_4_ANDROID_IMPORT_RESEAU.html` avec Google Chrome sur Android. Dans l’application, choisir Nouveau projet > Choisir des fichiers. Le sélecteur Android est appelé directement depuis un bouton utilisateur. Les formats audio courants sont acceptés (MP3, WAV, FLAC, M4A, AAC, AIFF, OGG, OPUS, etc.).

## Correctifs
- Bouton d’import natif HTML `button` relié directement au sélecteur, avec `showPicker()` et repli sur `click()`.
- Champ fichier séparé du bouton, acceptant les MIME audio et tous types de fichiers afin que les fournisseurs de fichiers Android puissent proposer leurs contenus; l’application filtre ensuite les extensions audio reconnues.
- Validation tolérante des types MIME Android.
- Repli de stockage temporaire en mémoire si IndexedDB est indisponible dans le contexte local. Dans ce cas, les fichiers ne persistent pas après fermeture de l’application.
- Indicateur réseau au démarrage et à chaque changement online/offline. Si une adresse serveur et une clé sont déjà enregistrées, l’application teste automatiquement `/health` à l’ouverture.

## Connexion Internet et moteur d’analyse
Le client peut utiliser la connexion réseau du téléphone lorsqu’il est ouvert. Le statut `Réseau actif` signifie que le navigateur signale une connexion; il ne prouve pas qu’un serveur STEM2CORE répond. Le moteur backend doit être déployé en HTTPS et configuré dans l’écran d’analyse avec son URL et sa clé API. **Aucun serveur public ni aucune URL de production ne sont fournis dans cette archive** : les analyses en ligne ne peuvent donc pas être déclarées opérationnelles tant que ce déploiement n’est pas effectué.

Aucun envoi audio automatique au démarrage. Les fichiers ne sont envoyés qu’au lancement volontaire d’une analyse et après consentement explicite. Le contrôle `/health` automatique ne transmet aucun audio.

## Backend
Le dossier `backend/` contient le service FastAPI de la base V6.7, ainsi que le Dockerfile et la configuration Render de départ. Déployer ce service sur un hébergement HTTPS, définir `STEM2CORE_API_KEY` et configurer les origines CORS autorisées. Pour une ouverture HTML `file://`, l’origine `null` est déjà autorisée dans cette base.

## Vérifications effectuées
- Syntaxe JavaScript : `node --check` OK.
- Un champ de sélection audio, un bouton de sélection et un indicateur réseau présents dans le HTML.
- Test sur appareil Android réel non effectué; merci de confirmer le résultat du sélecteur sur ton téléphone.
