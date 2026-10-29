# TONTI — version de démonstration 1.4

Application web (PWA) de tontines protégées : multilingue, multidevise, avec voix.
**Démonstration : aucun argent réel, aucun SMS envoyé, aucune vérification officielle.**

## Mettre en ligne avec GitHub Pages
1. Créez un dépôt GitHub et déposez tout le contenu de ce dossier.
2. Allez dans Settings → Pages → Deploy from a branch → `main` / (root).
3. L’application est en ligne à l’adresse `https://<compte>.github.io/<depot>/`.

## Contenu
- `index.html` : l’application complète (les traductions sont déjà incluses).
- `i18n/*.json` : les dictionnaires de langues (fr est la base, intégrée au code ; en, es, pt et ar sont complets ; bm et wo sont en bêta partielle).
- `docs/ARCHITECTURE.md` : les phases, les contrats de services, les règles métier, les pays et les limites de sécurité.
- `docs/VOICE.md` : la voix et les langues africaines, les prestataires à évaluer et la feuille de route.
- `identite.html` : la charte graphique.
- `manifest.webmanifest`, `sw.js`, `icons/` : installation sur téléphone et mode hors ligne.

## Démonstration
- Code SMS : **1234** (sauf scénario « code expiré », où le code est affiché à l’écran).
- Code d’invitation d’exemple : **GM-2026**.
- Profil > Mode démonstration : scénario de présentation, résultats des paiements, SMS, identité, temps simulé et réinitialisation.
