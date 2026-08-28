# The Tribunal : le juge des agents IA

> Les IA n'ont plus besoin qu'on les croie sur parole. Elles ont besoin d'un juge.

Un agent IA agit desormais sur l'argent, les fichiers, les systemes. The Tribunal est une couche de controle locale, deterministe et rejouable qui, avant chaque action, rend un verdict : autoriser, refuser, ou escalader. Les modeles proposent. Le juge dispose.

Page vivante du projet (journal de bord mis a jour a chaque avancee) : https://www.weboria.ch/joran/

## La these

La bonne architecture pour un agent qui agit n'est pas un modele plus intelligent. C'est un modele qui propose, et a cote, un juge independant qui certifie. Ce juge ne peut pas etre un grand modele de langage : il doit etre

- **deterministe** : meme entree, meme verdict, a l'octet ;
- **non contournable par reformulation** ;
- **auditable** : trace et certificat pour chaque verdict ;
- **minuscule** : assis sur chaque action, cout marginal quasi nul ;
- **local** : les donnees ne sortent pas.

Le marche 2026 s'est structure en deux camps qui laissent le milieu vide : les regles symboliques (rapides mais aveugles a l'intention reformulee) et les juges par grand modele (expressifs mais lents, non rejouables, eux-memes vulnerables a l'injection). Le point du milieu, un juge appris, compact, deterministe, rejouable et certifiable, qui juge l'action et non le texte : personne ne le vend. C'est notre place.

## Resultats mesures, publies tels quels

On mesure le juge par le taux de percee : la part des attaques qui passent. Chiffres reels, verdicts rejouables a l'octet, sur des jeux de test GELES de mecanismes jamais vus a l'entrainement.

| Etape | Taux de percee |
|---|---|
| Juge symbolique v0 (regles sur les mots de surface) | 80 % |
| Juge conscient de l'etat v1 (intention et zone du monde, pas les mots) | 28 % |
| Neuro-symbolique + co-evolution + constitution (3 graines, 2 domaines) | 0 % |
| Valide sur trafic REEL en mode ombre (0 % de faux blocage) | 0 % |
| Artefact unifie (juge + plancher constitutionnel + attestation) | 0 % |
| 49 attaques INDEPENDANTES, hors generateur, evasions comprises | 0 % |

Et le chiffre qu'on publie parce qu'il derange : la couche apprise, SEULE, sans le catalogue de menaces deterministe, rate encore 61 % des evasions ecrites expres pour battre les regles. Aujourd'hui c'est le catalogue qui porte la securite ; apprendre au reseau a voir l'intention derriere n'importe quelle ecriture est le vrai front de recherche, et il se travaille contre un adversaire qui pense.

## La methode

- **Prouve, jamais affirme.** Chaque avancee a un critere de succes ecrit avant le test, falsifiable, mesure sur plusieurs graines.
- **Rejouable et certifie.** Chaque verdict porte un certificat : version de la constitution, entree canonique, verdict, empreinte. Un tiers rejoue et retrouve le meme resultat.
- **Co-evolution a trois roles.** Un proposeur, un solveur, un verificateur, separes. Notre verificateur est exact : le juge lui-meme.
- **Mode ombre avant tout pouvoir.** Le juge a d'abord juge des milliers de vraies commandes sans aucun pouvoir. C'est la qu'on a attrape un sur-blocage de 74 % du travail legitime, corrige avant tout deploiement.
- **Journal infalsifiable.** Chaque verdict est scelle dans un journal en ajout seul ou chaque empreinte enchaine la precedente : falsifier une decision passee casse toute la chaine, et la fraude est detectee au rejeu.

## Le chemin

1. **J0, cadrage** : grammaire de la constitution, format des actions, certificat de traduction. (Fait)
2. **J1, rejeu a froid** : le juge se mesure contre un adversaire qui cherche ses failles, sur plusieurs domaines. (Fait)
3. **J2, ombre** : verdicts journalises sur des actions reelles, zero pouvoir, mesure de l'accord et du sur-blocage. (Fait)
4. **J3, blocage reel** : d'abord sur les actions reversibles, avec bouton d'arret et instance d'appel humaine.
5. **J4, co-evolution et ouverture** : adversaire branche, durcissement multi-domaines, puis produit.

## Contenu de ce depot

Aujourd'hui : ce README, qui reprend le journal de bord public.

A venir, apres une passe de separation entre le generique et l'environnement de test prive : le catalogue de menaces deterministe, les batteries d'attaques independantes, le verificateur de journal d'attestation, et les resultats bruts. Le code de recherche actuel encode des elements de l'environnement de test (chemins, services) qui n'ont pas leur place dans un depot public ; on publie propre ou on ne publie pas.

## Journal de bord

Le journal complet, billet par billet et chiffres compris, est sur la page vivante : https://www.weboria.ch/joran/

- **2026-08-28, billet 012.** Batterie d'attaques independantes ecrite a la main, hors generateur : le juge en laissait passer 74 %. Reconstruction a deux etages (catalogue de menaces deterministe + couche apprise elargie). Resultat final : 0 % de percee sur 49 attaques en 14 familles, evasions comprises, 0 % de sur-blocage. Reseau seul : encore 61 % de percee sur les evasions, publie tel quel.
- **2026-08-28, billet 011.** La preuve passe de une a trois graines. Zero percee, y compris sur les 382 attaques hors du filet du plancher, arretees par le reseau seul.
- **2026-08-28, billet 010.** Les trois preuves separees deviennent un seul artefact : juge + plancher + attestation, tous les criteres verts en une passe, deterministe a l'octet.
- **2026-08-28, billet 009.** Le plancher constitutionnel ferme la faille du billet 008 par construction : fuites d'actions sensibles de 16 % a 0 %.
- **2026-08-28, billet 008.** Troisieme verdict, escalader, pour les actions sensibles ; journal d'attestation chaine. Publie tel quel : 1 action sensible sur 12 passait encore, a durcir.
- **2026-08-27, billet 007.** Le mode ombre attrape un sur-blocage de 74 % du vrai travail, corrige a 0 % sans perdre les attaques.
- **2026-08-27, billet 006.** Le juge tourne sur notre propre cerveau de raisonnement, a parite : ~0 % de percee, 0 % de sur-blocage.
- **2026-08-27, billet 005.** Audit rejouable bit-identique sur deux executions independantes.
- **2026-08-27, billet 004.** Tenue contre un adversaire qui cherche, sur trois graines et un second domaine (appels d'outil et d'API).
- **2026-08-27, billet 003.** La co-evolution converge : percee de 80 % a 1.6 % sur jeu gele.
- **2026-08-27, billet 002.** Premier juge neuronal, anti-mirage propre (famille retiree, redecouverte, reinjectee).
- **2026-08-27, billet 001.** Le juge symbolique v0 est une passoire (80 % de percee), et c'est la bonne nouvelle fondatrice.

## Pourquoi maintenant

Le reglement europeen sur l'IA impose journalisation des actions, surveillance humaine et reconstruction d'incident pour les systemes a haut risque. Les assureurs exigent des preuves de tests adversariaux pour couvrir les agents. Un juge local, deterministe, qui journalise, produit ce dossier tout seul. Construit en Suisse.

---

Projet de recherche independant sur la surete des agents IA. Les resultats sont publies bruts, verifiables et rejouables, y compris les chiffres qui derangent.
