# TONTI — Architecture du prototype (v1.4)

Version de **démonstration** (Phase 1). Aucun argent réel ne circule, aucun SMS n’est envoyé, aucune vérification officielle n’est faite. Le code est organisé pour que chaque simulation puisse être remplacée par un vrai prestataire sans reconstruire l’application.

## 1. Les trois phases

| Phase | Contenu | État |
|---|---|---|
| 1. Prototype de présentation | Tous les parcours navigables en simulation, données d’exemple, mode démonstration, documents provisoires, multilingue | **Livré (v1.4)** |
| 2. Architecture réelle | Séparer simulations et métier, modèles de données, rôles, contrats d’interface, paramètres par pays | **Préparé** (voir §3 et §4) |
| 3. Réalité et lancement | Backend, authentification, paiements agréés, revue juridique, tests de sécurité | À faire |

## 2. Organisation du code (`index.html`)

1. **Ressources** : logo (lettres vectorielles + cauri d’origine).
2. **Configuration internationale** : `LANGS`, `SPOKEN`, `CUR`, `RAILS`, `ACCTS`, `COUNTRIES`.
3. **Internationalisation** : `t(clé, texteFR, variables)`. Aucun texte n’est écrit en dur dans les écrans. Les dictionnaires sont dans `i18n/*.json`, injectés au moment du build.
4. **Services** : les contrats et les implémentations simulées (`SimPayments`, `SimSms`, `SimIdentity`, `SimNotify`, `SimVoice`), réunis dans `Services`.
5. **Domaine** : les règles métier et le registre des transactions (`recordTx`, `applyTx`, `contribute`, `settlePending`, `confirmCash`, `coverMember`, `doPayoutDomain`, `repayDebt`).
6. **Interface** : les écrans et les feuilles d’action. L’interface n’appelle jamais un service directement pour l’argent : elle passe toujours par le domaine.

## 3. Contrats de services (à implémenter en Phase 3)

```text
PaymentService
  request({idem, group, round, payer, payee, amount, currency, rail, account})
     -> {status: 'success'|'pending'|'refused'|'failed'|'insufficient', ref, reason}
  settlePending(tx, approved) -> {status: 'success'|'expired', ref}
  payout({idem, group, round, beneficiary, amount, currency, rail, parts}) -> {status, refs[]}

SmsService
  sendOtp(phone) -> {delivered, code?}      // code renvoyé seulement en démo
  verifyOtp(phone, code) -> {ok, reason?: 'wrong'|'expired'}

IdentityService
  submit({person, country, doc, number, selfie}) -> {status: 'verified'|'pending'|'rejected', reason?}

NotificationService
  send({to, channel: 'sms'|'whatsapp'|'push'|'voice', text}) -> {queued}

VoiceService
  speak({text, lang, pref}) -> {mode: 'device'|'simulated'|'provider'}
  transcribe({audio, lang}) -> {text, confidence}
  translate({text, from, to}) -> {text}
```

`idem` est une clé d’idempotence (`groupe:tour:membre`). Le prestataire réel doit la respecter pour éviter les doubles débits.

## 4. Règles métier garanties par le domaine

- **Une seule cotisation active par membre et par tour** : un nouveau paiement est refusé si la cotisation est déjà payée, couverte, en attente ou déclarée en espèces.
- **Seuls les paiements `success` sont comptés.** Les paiements refusés, en échec, en solde insuffisant ou expirés apparaissent dans l’historique (filtre « Non comptés ») mais n’entrent dans aucun total, aucun fonds et aucune collecte.
- **Paiement en attente** : il est visible partout comme « En attente » et n’est compté qu’après approbation.
- **Espèces** : la cotisation est « À confirmer » jusqu’à la décision du trésorier (reçu ou refus avec motif).
- **Ordre de protection** : 48 h de grâce, nouvelle demande, médiation, garant, caution, fonds de solidarité, puis levée partielle.
- **Levée partielle** : si rien ne suffit, le trésorier verse ce qui a été collecté. Le manque est noté comme dette des impayés et comme somme due au bénéficiaire. Le remboursement de la dette est reversé au bénéficiaire.
- **Plafond par réseau** : PI-SPI 5 000 000 FCFA par paiement. Une levée plus élevée est découpée en plusieurs virements.
- **Une seule levée par tour** : idempotente.

## 5. Paramètres par pays (`COUNTRIES`)

Chaque pays définit : l’indicatif, la longueur des numéros, la devise, le réseau de paiement, la pièce d’identité et les comptes proposés.

| Zone | Pays | Devise | Réseau (simulé) |
|---|---|---|---|
| UEMOA | ML, SN, CI, BF, NE, TG, BJ, GW | XOF | PI-SPI (BCEAO) |
| CEMAC | CM, GA | XAF | virement instantané local (à définir) |
| Zone euro | FR, BE, ES, IT | EUR | SEPA Instant |
| Royaume-Uni | GB | GBP | Faster Payments |
| États-Unis | US | USD | RTP / FedNow |
| Canada | CA | CAD | Interac |
| Haïti | HT | HTG | virement local (à définir) |

Ajouter un pays revient à ajouter une ligne dans `COUNTRIES`. Les noms de pays s’affichent automatiquement dans la langue de l’utilisateur (`Intl.DisplayNames`).

**Non traité dans la démo** : les tontines multi-devises et les membres qui paient depuis un autre pays (change, frais transfrontaliers). Une tontine a une seule devise.

## 6. Langues

| Code | Langue | Interface | Voix |
|---|---|---|---|
| fr | Français | complète (langue de base) | voix de l’appareil |
| en | English | complète | voix de l’appareil |
| es | Español | complète | voix de l’appareil |
| pt | Português | complète | voix de l’appareil |
| ar | العربية | complète, affichage de droite à gauche | voix de l’appareil |
| bm | Bamanankan | **bêta partielle, à valider** | simulée (voir VOICE.md) |
| wo | Wolof | **bêta partielle, à valider** | simulée (voir VOICE.md) |
| ff, snk, ha | Fulfulde, Soninké, Hausa | prévues | prévues |

Pour ajouter une langue : créez `i18n/<code>.json` (les clés manquantes s’affichent en français), ajoutez la langue à `LANGS` et à `SPOKEN`, puis relancez le build.

## 7. Mode démonstration

L’écran Profil > Mode démonstration contient :

- un scénario de présentation en 12 étapes ;
- le choix du résultat du prochain paiement (approuvé, en attente, refusé, solde insuffisant, échec technique) ;
- le choix du scénario SMS (normal, code expiré, SMS non livré) ;
- le choix du résultat de la vérification d’identité (vérifiée, revue manuelle, refusée) ;
- le temps simulé (+1 jour, +7 jours), pour montrer la grâce, puis le retard ;
- les messages envoyés (SMS, WhatsApp) simulés ;
- la réinitialisation.

Pour qu’aucune simulation ne soit prise pour une vraie opération, plusieurs repères restent visibles : le badge « DÉMO » dans les en-têtes, le filigrane sur les reçus et attestations, les mentions « démonstration » dans les parcours sensibles, et les documents juridiques marqués « PROJET v0.1 ».

## 8. Sécurité : limites connues de la démo

- Les données sont stockées dans le navigateur (`localStorage`). Il n’y a ni serveur, ni chiffrement, ni authentification réelle.
- Le code OTP est affiché à l’écran.
- La logique d’argent tourne côté client. **En production, toute règle du §4 doit être appliquée côté serveur**, avec journal d’audit, contrôle des droits (membre, trésorier, administrateur) et idempotence.
- Il n’y a pas de limite de tentatives, d’antifraude ni de gestion de session.
- Le format QR est un exemple, pas le format officiel PI-SPI.

## 9. Pour passer en Phase 3

1. Choisir un établissement partenaire raccordé à PI-SPI (seuls les établissements agréés par la BCEAO peuvent se raccorder), puis implémenter `PaymentService` avec ses API.
2. Mettre en place un backend (base de données, API, authentification par OTP réel) et y déplacer le domaine.
3. Brancher un fournisseur SMS et WhatsApp (`SmsService`, `NotificationService`).
4. Brancher un service de vérification d’identité par pays (`IdentityService`).
5. Choisir les prestataires vocaux après tests avec des locuteurs natifs (`VoiceService`, voir VOICE.md).
6. Faire relire les documents juridiques par un juriste, pays par pays.
7. Réaliser les tests de sécurité, les tests de charge et la recette.
