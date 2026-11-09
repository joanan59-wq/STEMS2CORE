# STEM2CORE V8.1 — publication Android

## Contenu
- `frontend/` : interface PWA avec manifeste, icônes Android et cache hors ligne de l'interface.
- `backend/` : API FastAPI et Dockerfile.
- `render.yaml` : définition de service Render.

## Publication
1. Publier le dépôt sur GitHub.
2. Dans Render, créer un Blueprint depuis ce dépôt et appliquer `render.yaml`.
3. Déployer le frontend `frontend/` sur un hébergeur de sites statiques HTTPS.
4. Renseigner le domaine du frontend dans `STEM2CORE_CORS_ORIGINS` du backend.
5. Configurer l'URL HTTPS de l'API dans le frontend (même origine recommandée via proxy).
6. Vérifier `/health`, puis tester import, transcription et export avec un petit WAV.
7. Ouvrir l'adresse HTTPS dans Chrome Android > menu ⋮ > Installer l'application.

## Sécurité et état réel
Ne jamais intégrer une clé privée dans `app.js` ou dans une page publique. Une API publique sans authentification est exposée aux abus : activer une authentification et des limites d'usage avant une diffusion publique. Le mode `mock` est conservé volontairement : il ne réalise pas de séparation. L'IA avancée n'est pas activée par cette archive. Le déploiement et l'installation nécessitent encore les comptes et validations du propriétaire.
