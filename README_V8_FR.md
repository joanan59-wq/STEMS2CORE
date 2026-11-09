# STEM2CORE V8 — préparation du service en ligne

Le parcours produit cible s'inspire des flux de travail documentés de Klangio (import > choix du mode/instruments > transcription > édition > export) et Moises (import > séparation des stems > écoute/édition). STEM2CORE garde son identité et son objectif de partitions par instrument.

Cette version ajoute les fichiers Docker et dépendances absents du backend V7.x, plus une procédure de déploiement. Voir `backend/README_DEPLOIEMENT_V8_FR.md`.

**Important :** le serveur n'est pas hébergé par la création de cette archive. Il faut encore le déployer sur un compte d'hébergement et relier le frontend à son URL HTTPS. Le mode séparation par défaut est explicitement `mock`; ne pas le confondre avec une IA réelle.
