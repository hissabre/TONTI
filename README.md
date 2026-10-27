# TONTI — la tontine protégée

Application web mobile (PWA) pour gérer des tontines au Mali, en français et en FCFA.

## Mettre en ligne avec GitHub Pages
1. Créez un dépôt GitHub (par ex. `tonti`) et déposez-y tout le contenu de ce dossier.
2. Dans le dépôt : **Settings → Pages → Source : Deploy from a branch → main / (root)**.
3. Après une minute, l’application est en ligne sur `https://<votre-compte>.github.io/tonti/`.
4. Sur Android (Chrome) : menu ⋮ → **Ajouter à l’écran d’accueil**. Sur iPhone (Safari) : Partager → **Sur l’écran d’accueil**.

## Contenu
- `index.html` : toute l’application (aucune installation, aucun build).
- `manifest.webmanifest`, `icons/` : installation sur téléphone.
- `sw.js` : fonctionnement hors ligne après la première visite.
- `identite.html` : charte graphique (logo, couleurs, typographie, règles).

## Ce qui est simulé dans cette version
Les données sont enregistrées sur le téléphone (localStorage). Les paiements Orange Money / Moov Money, les SMS de rappel et la vérification NINA sont simulés.
Pour un lancement réel avec plusieurs utilisateurs : ajouter un serveur (par ex. Supabase ou Firebase) pour partager les groupes, une API de paiement mobile (agrégateur type CinetPay, PayDunya ou les API Orange Money), et un fournisseur SMS.
