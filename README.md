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

## Paiements : PI-SPI (BCEAO)
L’application utilise un seul canal de paiement, la PI-SPI de la BCEAO :
- chaque membre a un **alias** (son numéro +223) lié à son compte (Orange Money, Moov, Wave, banque…) ;
- à chaque tour, TONTI envoie une **demande de paiement** ; le membre l’approuve dans l’application de son compte ;
- le paiement est confirmé instantanément, avec une référence ; le trésorier n’a plus rien à valider (sauf les espèces) ;
- la levée est versée par virement PI-SPI vers l’alias du bénéficiaire ;
- paiement par **code QR** possible.

Pour le lancement réel : seuls les établissements agréés par la BCEAO se raccordent à la PI-SPI. TONTI doit passer par un établissement partenaire raccordé (banque ou émetteur de monnaie électronique) et utiliser ses API homologuées. Dans cette version, la PI-SPI est simulée et le QR est un exemple.

## Ce qui est simulé dans cette version
Les données sont enregistrées sur le téléphone (localStorage). Les paiements PI-SPI, les SMS et la vérification NINA sont simulés. Le code de démonstration est 1234 ; le code d’invitation d’exemple est GM-2026.
Pour un lancement réel avec plusieurs utilisateurs : ajouter un serveur (par ex. Supabase ou Firebase) pour partager les groupes, une API de paiement mobile (agrégateur type CinetPay, PayDunya ou les API Orange Money), et un fournisseur SMS.
