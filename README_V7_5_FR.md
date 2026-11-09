# STEM2CORE V7.5 — import direct

## Ouvrir
1. Décompresser l'archive sur l'ordinateur.
2. Ouvrir `STEM2CORE_V7_5_IMPORT_DIRECT.html` avec Chrome, Edge ou Firefox.
3. Cliquer sur « Importer un audio ».
4. Cliquer directement sur le champ natif « Choisir un fichier » et sélectionner un MP3/WAV/M4A.
5. Donner un nom au projet puis cliquer sur « Créer le projet ».

Le champ HTML natif est volontairement visible : le navigateur ouvre le sélecteur de fichiers directement, sans dépendre d'un clic JavaScript sur un champ masqué.

## Important — connexion et IA
Le navigateur vérifie l'état du réseau à l'ouverture. Le moteur IA distant n'est pas encore disponible tant qu'un backend STEM2CORE HTTPS n'a pas été déployé et que son URL et sa clé API n'ont pas été configurées. La présence d'Internet sur l'ordinateur ne suffit pas à elle seule à créer ce serveur. Aucun envoi de fichier audio n'est effectué automatiquement.

## Limites
L'import, la bibliothèque locale, la sauvegarde et l'écoute locale sont disponibles. Les analyses IA et la séparation des instruments dépendent du backend et des moteurs correspondants ; elles ne sont pas présentées comme fonctionnelles sans serveur actif.
