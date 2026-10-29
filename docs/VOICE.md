# TONTI — Voix et langues africaines

La voix est une **fonction d’accessibilité centrale**, pas une simple option de traduction. Une personne à l’aise en bambara ou en wolof doit pouvoir écouter ses rappels de cotisation et les instructions de paiement, naviguer dans l’application et, à terme, parler à un assistant vocal.

Trois capacités distinctes sont nécessaires, à évaluer séparément pour chaque langue :

- **TTS** : synthèse vocale (lire à voix haute) ;
- **ASR** : reconnaissance vocale (comprendre la parole) ;
- **Traduction**.

## 1. Ce qui est en place dans la démo (v1.4)

| Fonction | Où | État |
|---|---|---|
| Réglages séparés : langue de l’interface, langue parlée, voix préférée (féminine, masculine, sans préférence), lecture automatique | Profil > Voix ; écran de choix de la langue au premier lancement | Fonctionnel |
| Bouton « Écouter » à côté des instructions et des montants | Accueil, tontine, paiement, résultat, versement de la levée, protection, accueil des nouveaux utilisateurs | Fonctionnel |
| Lecture : voix de l’appareil (fr, en, es, pt, ar) | Lecteur en bas d’écran | Réelle (synthèse du navigateur) |
| Lecture : bambara, wolof | Lecteur | **Simulée et annoncée comme telle** : le texte s’affiche dans la langue, sans aucun son généré, avec le nom du prestataire à connecter et un bouton pour écouter en français |
| Assistant vocal | Bouton micro (Accueil, Profil) | Simulé : consentement, bouton micro, questions d’exemple, texte modifiable, réponse visuelle et lue |
| Consentement au traitement de la voix | Avant le premier usage de l’assistant ; document « Traitement de la voix » | Fonctionnel (projet v0.1) |

### Règles respectées

- Le micro n’est **jamais** activé dans la démo, et aucune transcription n’est présentée comme reconnue depuis le micro. Les questions d’exemple sont annoncées comme telles, et le texte reste modifiable.
- Aucun son pré-enregistré n’est utilisé (il n’existe pas d’enregistrement autorisé pour le moment).
- **Aucune action d’argent n’est exécutée par la voix.** L’assistant ouvre l’écran concerné, et l’utilisateur confirme toujours à l’écran.
- Les montants, noms, dates et statuts lus à voix haute viennent des mêmes données que l’affichage. Ils sont donc identiques dans toutes les langues.
- Si une phrase n’est pas encore traduite, le lecteur l’indique (« affichée en français »).

## 2. Contrat d’interface

```text
VoiceService
  speak({text, lang, pref})      -> {mode: 'device'|'simulated'|'provider'}
  transcribe({audio, lang})      -> {text, confidence}     // ASR
  translate({text, from, to})    -> {text}
```

Chaque prestataire est branché derrière ce contrat. On peut aussi en combiner plusieurs, par exemple un prestataire A pour la voix bambara et un prestataire B pour la reconnaissance du wolof.

## 3. Prestataires à évaluer

Ces pistes et leurs descriptions viennent de vos recherches et des présentations des prestataires. Elles n’ont pas été vérifiées ici.

| Prestataire | Intérêt | Langues visées | À vérifier |
|---|---|---|---|
| **Kooma** | Plateforme axée bambara : TTS, transcription, traduction, via API | Bambara | Accès à l’API, prix, conditions commerciales, latence, qualité sur le vocabulaire financier |
| **Djelia** | Modèles distincts pour la transcription, la synthèse et la traduction en bambara ; API documentée | Bambara | Tarifs à l’usage, conditions actuelles |
| **Khaya AI** | Couverture plus large : TTS et ASR annoncés pour le wolof et d’autres langues africaines | Wolof et autres | Langue, voix, dialecte et disponibilité de l’API pour chaque service |
| **WAXAL** (recherche, données ouvertes) | Données vocales pour 27 langues d’Afrique subsaharienne, utiles pour entraîner ou héberger ses propres modèles | Plusieurs | Jeu de données par langue et licence avant tout usage commercial |

**Recommandation** : comparer Kooma et Djelia pour le bambara, et Khaya AI pour le wolof, avec **le même jeu de phrases réelles**. Ce jeu doit couvrir des montants en FCFA, des dates, des retards, des négations, des noms et les instructions de paiement. Le meilleur prestataire est celui que les locuteurs natifs comprennent le mieux, pas celui qui annonce le plus de langues.

**Précaution** : la reconnaissance vocale des langues peu dotées peut encore mal gérer les nombres, les noms, les dates et le vocabulaire financier. Une démonstration convaincante ne suffit pas à prouver la fiabilité en production.

## 4. Feuille de route des langues

| Langue | Priorité | À démontrer |
|---|---|---|
| Français | P0 | Interface complète, instructions et rappels lus |
| Bambara | P0 | Instructions et rappels lus, saisie vocale, traduction |
| Wolof | P1 | Instructions et rappels lus, saisie vocale, traduction |
| English | P1 | Interface complète, voix |
| Fulfulde / Peul | P2 | Évaluer les variantes régionales et les modèles disponibles |
| Soninké, Hausa, autres | Plus tard | Selon la demande et la qualité vérifiée des modèles |

Les traductions bambara et wolof de la démo sont **partielles et à valider par des locuteurs natifs** avant toute présentation publique.

## 5. Phase 3 : à faire

1. Préparer un corpus de test de 50 à 100 phrases (montants, dates, noms, retards, négations, instructions).
2. Tester chaque prestataire sur ce corpus avec des locuteurs natifs de plusieurs régions.
3. Brancher le prestataire retenu derrière `VoiceService`.
4. Définir les règles de conservation et de suppression des enregistrements. Mettre à jour le document « Traitement de la voix ».
5. Ajouter de vraies demandes d’autorisation du micro, puis afficher la transcription reconnue avec sa correction possible.
6. Prévoir des consignes audio pré-enregistrées pour les actions essentielles quand la connexion est faible, avec l’accord écrit des voix enregistrées.
7. Garder la règle : jamais d’opération d’argent sur la seule base d’une transcription.
