# STEM2CORE V3–V6 — Integrated engineering package

This package consolidates the V1 responsive PWA frontend and the V2 separation API foundation, then adds V3 transcription foundations, V4 instrument-analysis adapters, V5 MusicXML export primitives, and V6 deployment/security/test documentation.

## Honest implementation status
- V1 client shell: included from the earlier starter.
- V2 upload/separation API: included. Mock provider is default; Demucs provider is optional and must be installed/configured.
- V3 pitch-analysis endpoint: real monophonic pitch estimation using librosa PYIN when optional audio dependencies are installed. Best suited to isolated voice/bass/guitar-like monophonic stems; it is not reliable polyphonic transcription.
- V4 piano/drum/brass: adapter contracts and experimental onset/pitch utilities only. No claim of production-grade piano, drum-kit, choir-part, or brass-section transcription.
- V5 MusicXML: basic monophonic MusicXML generation from detected note events; not a full notation editor or engraving engine.
- V6: Docker/deployment templates, security checklist, tests and acceptance plan. This is not a deployed production service.

## Backend quick start
```bash
cd backend
python -m venv .venv
# Windows: .venv\Scripts\activate
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload
```
API docs: http://127.0.0.1:8000/docs

For pitch analysis install:
```bash
pip install -r requirements-audio.txt
```
For actual stem separation install FFmpeg, then:
```bash
pip install -r requirements-demucs.txt
# Set STEM2CORE_SEPARATION_PROVIDER=demucs
```
The Demucs adapter currently performs a two-stem vocal/accompaniment separation. Fine-grained six-family stem separation is a later model-integration task.

## Frontend
Serve the `frontend` directory over localhost:
```bash
cd frontend
python -m http.server 8080
```
The existing V1 client is local-first. This package documents API integration; the frontend does not yet provide a fully integrated transcription/score editor workflow.

## Important production work still required
Authentication, durable PostgreSQL job/project storage, S3-compatible private object storage, a proper Celery/RQ worker, signed download URLs, rate limiting, observability, GPU worker deployment, privacy/retention controls, cross-device sync, and end-to-end mobile/browser testing.

## V6 release-candidate frontend integration
The project detail page now includes an API-connected analysis panel:
- Backend health status.
- Select a project file and submit it to the V2 separation API.
- Poll separation job status.
- Run V3 monophonic pitch analysis.
- Review detected note events and confidence values.
- Export the detected events as a basic MusicXML file.

The frontend defaults to `http://127.0.0.1:8000`. For hosted use, set
`window.STEM2CORE_API` before loading `app.js` to the HTTPS API URL.
This is still a release candidate: mock separation is the default; install
Demucs/FFmpeg for actual vocal/accompaniment separation and librosa for pitch analysis.

## V6.1 — amélioration de l'export de partition

L'export MusicXML utilise désormais les temps de début (`start`) et les durées
(en secondes) renvoyés par l'analyse PYIN. Il conserve les silences, répartit
les notes sur plusieurs mesures 4/4 et lie les notes qui traversent une barre
de mesure. Le tempo peut être réglé dans l'interface (20 à 300 BPM, défaut
120 BPM) avant l'export. Les événements qui se chevauchent sont refusés : le
moteur reste monophonique et ne prétend pas transcrire les accords ou plusieurs
voix simultanées.

Tests : depuis `backend/`, exécuter `python -m pytest -q`.

## V6.2 — installation mobile et présence sur Android

Le frontend comporte maintenant un manifeste PWA avec icônes PNG 192/512, icône maskable, icône Apple, raccourci « Nouveau projet », prise en charge du mode autonome et un guide d'installation Android/iOS intégré. Le service worker met en cache la coque de l'application et permet de rouvrir l'interface hors connexion après une première visite en ligne. Les fichiers audio et les analyses serveur ne deviennent pas disponibles hors connexion par magie : ils restent soumis au stockage local et à la disponibilité du backend.

Pour que l'application soit proposée à l'installation, hébergez-la en HTTPS (ou utilisez localhost pour le développement). Sur Android/Chrome, l'utilisateur confirme « Installer l'application » ; l'icône STEM2CORE apparaît ensuite dans le lanceur/menu des applications. Une présence distribuable via Google Play nécessite en plus un paquet Android TWA/APK/AAB signé. Le dossier `android-twa/` documente cette étape, mais ne contient pas de paquet signé.


## V6.3 — première brique polyphonique expérimentale

Nouvel endpoint `POST /api/v6/transcriptions/polyphonic` : analyse spectrale
multi-hauteur avec `librosa.piptrack`, conservation d'événements qui peuvent
se chevaucher et regroupement des notes par hauteur MIDI (`pitch_tracks`).
Cette brique est volontairement expérimentale : elle peut confondre les
harmoniques avec des notes réelles et ne sépare pas les instruments. Elle
n'est donc pas présentée comme une transcription piano fiable. Installer
`requirements-audio.txt` pour activer les dépendances audio.

Le moteur renvoie les notes, leur début, leur durée et un indicateur de
confiance spectral. L'export MusicXML V5 reste monophonique et refuse toujours
les chevauchements : un export polyphonique multi-voix correctement gravé
sera une étape distincte. Les chœurs ne sont pas encore séparés en voix
individuelles.

Exemple d'appel :
```bash
curl -X POST http://127.0.0.1:8000/api/v6/transcriptions/polyphonic \
  -F "file=@piano.wav"
```


## V6.4 — interface d'analyse polyphonique expérimentale

Le détail d'un projet propose maintenant un bouton **Analyse polyphonique (test)**.
Il appelle `POST /api/v6/transcriptions/polyphonic`, affiche les hauteurs
détectées sous forme de pistes temporelles (piano-roll simplifié) et permet
de télécharger les événements au format JSON. Cette visualisation regroupe
les notes par hauteur MIDI, et non par instrument : elle ne doit pas être
interprétée comme une séparation automatique des instruments.

Pour un téléphone Android connecté au même réseau local qu'un ordinateur
qui exécute l'API, l'adresse par défaut utilise maintenant le nom d'hôte du
site avec le port 8000. En production, configurer `window.STEM2CORE_API`
avec l'URL HTTPS réelle de l'API (ou un chemin de même origine). Ne pas
exposer le serveur de développement directement sur Internet.


## V6.5 — export MusicXML multi-pistes expérimental

Ajout de `POST /api/v6/export/polyphonic-musicxml`. À partir des événements
polyphoniques détectés, l'API génère un fichier MusicXML avec une partie
monophonique par hauteur MIDI détectée. Les notes simultanées de hauteurs
différentes peuvent ainsi être conservées dans des parties séparées. Le
frontend propose désormais le bouton « Exporter la partition polyphonique ».

Important : les parties sont des **pistes de hauteur**, pas des instruments
identifiés. Cette fonction ne sépare pas piano, guitare, voix ou cuivres et
ne corrige pas les faux positifs dus aux harmoniques. Une validation et une
édition musicale restent nécessaires. Les doublons simultanés de même
hauteur ne sont pas pris en charge.


## V6.6 — édition manuelle des événements détectés

Dans le panneau d'analyse polyphonique, les utilisateurs peuvent désormais
corriger la hauteur MIDI, le temps de début et la durée des notes affichées,
supprimer des notes et en ajouter manuellement. Les corrections sont utilisées
par les exports JSON et MusicXML. Le tableau affiche jusqu'à 500 notes à la
fois ; les événements au-delà restent conservés pour l'export.

Cette édition est une correction d'événements, pas encore un éditeur de
partition graphique complet : pas de saisie sur portée, de quantification
rythmique automatique ni de reconnaissance des instruments. Le fichier audio
source n'est pas modifié.


## V6.7 — aperçu de portée, clé et quantification

L'éditeur propose un aperçu graphique simplifié des événements détectés sur
une portée, avec choix de clé de sol ou de fa. Une quantification temporelle
est disponible sur des grilles de 1/16, 1/8, noire ou blanche selon le tempo
défini. Le bouton « Imprimer / PDF » ouvre la boîte de dialogue d'impression
du navigateur ; choisir « Enregistrer au format PDF » pour produire un PDF.

Limites : cet aperçu est une représentation simplifiée des hauteurs et des
positions temporelles, pas une gravure professionnelle. Les altérations sont
simplifiées en dièses. La quantification modifie les événements avant export
et doit être contrôlée à l'écoute.


## Version 7.0 — Release candidate

### Nouveautés de V6.8 à V7.0
- Aperçu de portée enrichi : notes pleines/vides selon la durée, hampes et drapeaux simplifiés pour les durées courtes.
- Repères de temps, barres de mesure et numérotation des mesures en 4/4 calculés à partir du tempo.
- Export MIDI standard (`.mid`) avec tempo, notes simultanées et durées. Le fichier MIDI peut être ouvert dans un séquenceur ou un logiciel de notation.
- Les corrections manuelles et la quantification existantes sont reprises dans les exports MIDI et MusicXML.

### Limites importantes
- L'aperçu graphique reste une prévisualisation de travail, pas une gravure musicale professionnelle. Les rythmes sont estimés à partir des durées en secondes et du tempo déclaré.
- Les barres de mesure supposent une mesure 4/4. La signature rythmique n'est pas encore détectée automatiquement.
- Le moteur polyphonique reste expérimental : les harmoniques peuvent être prises pour des notes et les hauteurs détectées ne sont pas des pistes d'instruments séparées.
- L'export MIDI conserve les hauteurs et les durées détectées, mais ne crée pas encore de banques de sons ni de parties instrumentales nommées.
- Le PDF est généré par la fonction d'impression du navigateur (« Enregistrer au format PDF »).
- L'application Android reste une PWA installable après hébergement HTTPS ; aucun APK/AAB signé n'est inclus dans cette archive.

### Vérifications
Les contrôles de syntaxe JavaScript/Python, les tests backend existants et l'intégrité de l'archive doivent être exécutés avant toute mise en production.
