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

Un juge se mesure par **deux** chiffres, et il faut les deux : le taux de percee, la part des attaques qui passent, et le taux de refus du travail legitime, la part du vrai travail qu'il arrete. Un juge qui excelle sur le premier et ignore le second est inutilisable ; nous l'avons appris deux fois, a nos depens, et les deux fois sont dans ce tableau.

| Etape | Percee | Refus du travail reel |
|---|---|---|
| Juge symbolique v0 (regles sur les mots de surface) | 80 % | non mesure |
| Juge conscient de l'etat v1 (intention et zone, pas les mots) | 28 % | non mesure |
| Neuro-symbolique + co-evolution + constitution (3 graines, 2 domaines) | 0 % | non mesure |
| Premiere mise en mode ombre sur du trafic reel | 0 % | **74 %** |
| Apres reintegration du trafic reel a l'entrainement | 0 % | 0 % |
| Artefact unifie (juge + plancher constitutionnel + attestation) | 0 % | 0,4 % |
| 49 attaques independantes, hors generateur, evasions comprises | 2 % | 1,95 % |
| Plancher corrige, mesure sur 5 928 commandes reellement executees | 2 % | **0,26 %** |
| Sous 16 reecritures generiques du shell, une a la fois | 2,2 % en moyenne | 0,26 % |
| Sous 348 compositions de deux a trois reecritures | 2,6 % en moyenne | 0,26 % |
| Meilleure des 4 representations aveugles neuves, sur un controle de 960 commandes | | **45,1 %** du travail legitime fait avec un outil inhabituel |
| **Notre propre plancher**, sur ce meme controle | | **16,1 %**, dont la moitie due a la forme de la commande et non a l'outil |

**Les taux de percee de ce tableau sont fragiles et nous le disons.** Ils reposent sur 49 attaques. Un « 0 % » sur 49 essais a une borne haute de 7,3 % a 95 % de confiance, et le 2 % actuel (48 sur 49) vaut [89,3 ; 99,6] en detection. Aucun de ces chiffres n'autorise la phrase « il bloque tout ». Il faut une batterie d'un autre ordre de grandeur, ecrite par une main adverse.

**Et une seconde reserve, decouverte apres coup, qui porte sur tout ce tableau.** Jusqu'au 31 aout, notre banc apprenait le comportement normal sur un journal d'activite **vivant**, qui grossissait pendant la mesure. Les taux ci-dessus n'etaient donc pas rejouables a l'identique, ni par un tiers ni par nous. Le corpus est desormais gele et empreinte, et deux lancements identiques rendent le meme fichier a l'octet pres ; le detail est au point 7.

## Les corrections valent mieux que les resultats

Un projet de recherche honnete se juge moins a ses reussites qu'a ce qu'il publie quand il se trompe. Voici les dernieres, dans l'ordre ou elles font mal.

### 1. Notre plancher deterministe refusait le vrai travail

Nous avions conclu que la securite du systeme etait portee par le catalogue deterministe et non par la couche apprise. Ce catalogue n'avait jamais ete mesure que sur dix-sept commandes legitimes ecrites a la main. Passe sur 5 928 commandes reellement executees par un operateur, il en **refusait 1,95 %** : un deploiement par cle SSH, l'effacement d'un fichier de verrou temporaire, l'ouverture d'une session distante. Il rendait le meme verdict a l'effacement d'un fichier de verrou et a l'effacement du disque entier.

Trois distinctions manquaient, aucune n'est une heuristique : un secret en position d'option d'authentification n'est pas un secret en position de charge utile ; effacer n'est pas detruire, la gravite tient a la portee et a la zone ; et la destination fait partie de l'intention, un juge ne peut pas savoir qu'une adresse est hostile mais il peut savoir qu'elle est indeclaree. Refus ramene a 0,28 % sans perte de detection.

### 2. Un commentaire de 590 octets rendait le juge aveugle

Nous avons soumis nos six dernieres manches non publiees a six auditeurs adversariaux independants, avec une consigne unique : trouver ce qui rendrait une publication malhonnete. Ils ont rendu 48 defauts, dont 22 bloquants. Le plus grave annule tous les autres.

L'extracteur qui traduit une commande en intentions ne lisait que ses 600 premiers octets. Il suffisait donc de pousser la charge au-dela : un commentaire anodin de 590 octets, puis n'importe quoi, et le vecteur d'intentions devient **entierement vide**, le verdict PERMIS. Cela valait pour tout. La couche a qui nous venions d'attribuer toute la securite se contournait avec un commentaire, et **aucune de nos batteries ne pouvait le voir, puisque aucune n'ecrit une commande longue**.

Ferme par analyse en fenetres glissantes, verifie sur 30 cas. La lecon depasse ce projet : un banc qui ne fait varier qu'une dimension ne mesure que ce qu'on a pense a mesurer. C'est un auditeur adverse qui l'a trouve, pas nos 49 attaques.

Le meme audit a tue notre resultat le plus prometteur, jamais publie : un juge qui detectait 89,8 % des attaques sans en avoir jamais vu une. Rejoue sur cinq graines, il ne battait son propre controle que sur la seule graine figee dans le script ; son taux de fausses alertes etait une tautologie de seuil ; et trois autres mesures tombaient parce que l'etiquette apprise etait un OU logique de deux de ses dix entrees.

### 3. Sans la connaissance des attaques, le juge tombe de 86 % a 37 %

Le point precedent posait une question qu'on ne pouvait plus eviter : notre representation d'entree avait ete **ecrite en regardant les attaques**. Plusieurs de ses primitives etaient, mot pour mot, des fragments de nos propres exemples malveillants. « Il n'a jamais vu une attaque » etait vrai des poids et faux du systeme.

Nous avons donc rebati la representation sur trois sources anterieures et etrangeres au jeu d'attaques : **le manuel du systeme** (un lexique de capacites produit par une commande a partir des pages de manuel des binaires installes, via un vocabulaire de 141 mots ordinaires), **la norme de hierarchie des fichiers** (quelle zone du disque est touchee) et **la grammaire du shell**. Le lexique se regenere a l'empreinte identique depuis un dossier ou aucun fichier d'attaque n'est accessible. La verification d'aveuglement est automatique : les parties ecrites a la main partagent six mots avec les attaques, tous du vocabulaire ordinaire du manuel et du shell ; l'ancienne representation en partageait quarante-quatre, dont des noms de fichiers precis qui n'existent que parce que quelqu'un avait regarde les attaques avant d'ecrire.

Au meme point de fonctionnement, un pour cent de fausses alertes sur du trafic reel jamais vu :

| Representation | Detection | Fausses alertes |
|---|---|---|
| Ecrite en regardant les attaques | 85,7 % | 1,1 % |
| Aveugle aux attaques | **~37 %** (ecart-type 6) | 1,2 % |
| Comptage sans parametre appris, meme representation aveugle | 16,3 % | |

**Pres de cinquante points d'ecart.** Pres des deux tiers de ce que nous appelions detection venait d'avoir lu les reponses. (Le chiffre de la representation aveugle a d'abord ete publie a 32,0 %, puis corrige deux fois : voir les points 6 et 7, ou nous avons decouvert que notre banc ne mesurait pas ce que nous croyions.)

Deux controles restent bons : zero fausse alerte sur dix-huit commandes legitimes employant des outils que l'operateur n'a jamais utilises, donc ce n'est pas un detecteur d'habitudes ; et le contournement par prefixe long est impossible par construction, puisque plus rien n'est tronque.

### 4. Le contexte recent ne tient pas lieu de declaration d'intention

La conclusion precedente disait : l'agent doit declarer ce qu'il voulait accomplir. Avant de batir
dessus, nous avons teste le substitut le moins cher, celui qu'un juge en production a toujours sous
la main : ce que l'agent faisait juste avant. 95 jours de trafic reel, mille sessions, decoupage
par session et non par commande.

Le contexte porte bien de l'information : il predit l'action suivante 20,3 % mieux qu'un contexte
tire au hasard. Mais il ne detecte rien. **Zero pour cent** de detection si l'agent choisit son
contexte parmi trois cents, **deux pour cent** s'il lui est impose, contre **trente-quatre** pour
l'action seule.

Le controle qui rend ce resultat lisible : en donnant le **meme avantage aux commandes legitimes**,
elles tombent aussi a zero pour cent de signalement. Ce n'est donc pas le contexte qui est inutile,
c'est **le libre choix de sa propre declaration qui detruit le detecteur, pour tout le monde a la
fois**. Une declaration que celui qui agit peut choisir apres coup n'est pas une information, c'est
une surface d'attaque.

Ce qui est falsifie est precis : que l'historique recent tienne lieu de declaration. Ce qui ne
l'est pas : la these, car une vraie declaration formulee dans les mots de la tache n'existe pas
dans ces donnees. **Acquis : une declaration devra etre engagee AVANT que l'action soit choisie, et
liee a elle par autre chose qu'une promesse.** C'est la couche de contrat de la these, et nous
avons maintenant un chiffre qui dit pourquoi elle doit etre contraignante plutot que declarative.

### 5. Un point n'est pas une courbe : le banc de reecriture

Tous nos chiffres reposaient sur 49 attaques figees. Mesurer un juge sur un jeu fige mesure sa
memoire, pas sa comprehension, et un attaquant reel relance la meme attaque **reecrite** jusqu'a ce
qu'une forme passe. Nous avons donc defini seize transformations **a priori depuis la semantique du
shell**, ecrites avant toute mesure et jamais choisies en regardant nos echecs, puis compose ces
transformations entre elles : 348 formes au total.

Premiere passe : **les quarante-neuf attaques repassent toutes**, et une seule forme, l'encodage
octal, ramene la detection a zero sur la totalite.

Deux causes, de nature opposee, et c'est ce qui fait l'interet de la manche.

**Une faute de notre propre instrument.** Notre transformation d'encodage ne codait que les cent
vingt premiers octets : composee avec un prefixe de bourrage, elle n'encodait plus que le
commentaire, donc la forme produite ne contenait plus l'attaque. La compter comme une percee aurait
ete se mentir avec ses propres outils. Trouvee en verifiant, retiree, racontee.

**Un vrai defaut, et c'est la troisieme troncature de la meme journee.** Nous avions ferme la
troncature d'analyse sur le chemin principal par des fenetres glissantes ; les correctifs suivants
passaient les charges **derivees** entieres a la meme fonction qui les retronque. La porte etait
bouchee et la fenetre ouverte. **Deux des trois defauts de cette famille ont ete introduits par les
correctifs des precedents.** Une etape de preparation qui coupe le texte est la classe de defaut la
plus recurrente de ce projet : elle se traite avec une fonction unique appliquee a tout texte
analyse, jamais au cas par cas.

Une troisieme correction de meme nature : **l'ordre des etapes de preparation est une propriete de
securite.** Il faut normaliser avant de resoudre les variables, parce que le shell developpe le
separateur avant de decouper les mots.

| mesure | avant | apres |
|---|---|---|
| evadable sous une reecriture | 100 % | **4,1 %** |
| evadable sous composition de 2 a 3 | 100 % | **18,4 %** |
| pire des 348 compositions | 0 % de detection | **72,7 %** |
| detection moyenne, 16 formes | 90,0 % | **97,8 %** |
| refus du travail reel | 0,28 % | **0,26 %** |

**Et une lecon qui vaut au-dela du projet.** Une derniere correction, reelle et gratuite (elle
ferme un chemin ou une affectation precedee d'un guillemet devenait invisible), a fait monter la
detection moyenne a trois couches de 96,4 a 97,3 % **sans faire bouger d'un point le nombre
d'attaques evadables**. Parce que ce nombre est un **maximum sur 348 tentatives** : fermer un
chemin n'y change rien tant que les autres existent. Seule l'elimination d'une CLASSE entiere le
deplace, comme l'a fait le fenetrage unifie. Viser une moyenne ou viser un maximum ne sont pas la
meme decision d'ingenierie, et il faut savoir laquelle on vise avant d'ecrire le correctif.

### 6. Nous avons publie un point la ou notre banc rendait un intervalle de vingt

Le point precedent posait « 32 % » comme le chiffre a battre. Avant de chercher a le battre, nous avons mesure une chose que nous n'avions jamais mesuree : **le bruit du banc lui-meme**. Meme representation, meme protocole, meme code, dix tirages differents de l'initialisation et du decoupage.

```
24,5   28,6   34,7   36,7   36,7   38,8   38,8   40,8   40,8   44,9
```

**Ecart-type six points, etendue vingt.** Le 32 % publie etait la moyenne de trois graines qui se trouvaient etre basses.

La consequence compte plus que la correction : **sur ce banc, un ecart de moins de douze points entre deux methodes n'est pas une amelioration, c'est du bruit de tirage.** Toute conclusion tiree d'un gain de trois ou cinq points aurait ete du bruit lu comme un progres. Pourquoi c'est si bruyant, et c'etait previsible : quarante-neuf attaques font qu'une seule attaque vaut deux points, et l'initialisation deplace le seuil de decision.

Ce qui tient malgre tout : l'ecart entre la representation ecrite en regardant les attaques et la representation aveugle fait pres de cinquante points. A six points d'ecart-type, cinquante points restent cinquante points. **La conclusion du point 3 est intacte, c'est sa precision qui etait fausse.**

Nous avions passe la journee a reprocher a nos manches anterieures de publier des points sans intervalle de confiance, et nous avions fait exactement pareil deux heures plus tot.

### 7. Cinq fois la meme commande, cinq resultats : le banc apprenait sur le journal de nos propres mesures

Avant de se servir des dix graines du point precedent, verification qu'elles suffisaient : **cinq lancements de la meme commande, memes graines, meme code.** Resultats `38,2 / 35,9 / 36,7 / 34,7 / 39,2`. **Les graines ne fixaient rien**, et nous ne l'avions jamais vu parce que nous n'avions jamais lance deux fois la meme chose.

**Premiere hypothese, fausse, et c'est la moitie du travail.** Le non-determinisme des noyaux de calcul sur carte graphique. Correctif pose dans les regles de l'art, y compris le piege classique : la variable d'environnement qui impose le mode reproductible doit etre posee **avant** le chargement de la bibliotheque, sinon le contexte est deja cree et elle est ignoree sans le moindre message. Plus le mode qui **leve une erreur** quand un noyau non reproductible est appele, au lieu de le laisser tirer au sort en silence. Trois nouveaux lancements : `36,5 / 35,3 / 36,5`. Toujours pas fixe. Le correctif etait bon, la cause etait ailleurs, et c'est pour cela qu'on verifie une hypothese au lieu de se contenter de la corriger.

**La vraie cause.** Le banc apprend le comportement normal sur du trafic reel, lu dans un journal d'activite. Ce journal est **vivant** : six lignes de plus toutes les vingt secondes, et ces lignes sont **nos propres commandes de mesure**. Le corpus d'entrainement grossissait sous le banc, alimente par l'acte meme de mesurer. Nous avions cherche le bruit dans le modele, dans les graines, dans le materiel ; **il etait dans les donnees, et il venait de nous**.

Parade : le corpus est **gele**, et son empreinte est inscrite dans le code du banc et dans chaque resultat. Preuve : **trois lancements de la meme commande rendent des fichiers de resultats identiques a l'empreinte, au dernier octet.** Plancher rejouable : 36,7 %.

Deux defauts d'instrument de la meme famille sont tombes dans la foulee. **Notre controle d'aveuglement ne suivait pas les imports** : un module qui se contente d'assembler plusieurs representations affichait le meilleur score du tableau, zero mot suspect, parce que son fichier ne contient que de la tuyauterie et pas une seule regle. Un controle qui ne suit pas les imports declare propre exactement ce qu'il n'a pas regarde. Et en mesurant quatre representations neuves, nous nous sommes apercus que **leurs fichiers changeaient entre deux lancements** parce que leurs auteurs les corrigeaient encore : une meme representation est passee de 96 a 129 puis a 65 composantes pendant nos mesures.

**La regle qui sort de la journee : tout ce qui entre dans une mesure doit etre fige et empreinte, les donnees, le code mesure et le code qui mesure. Ce qui bouge pendant qu'on l'observe n'est pas mesure, il est apercu.**

### 8. Une representation gagnait vingt-quatre points. La porte qui la validait tenait en dix-huit commandes

Nous avons fait concevoir **quatre representations d'entree par quatre auteurs independants**, tous aveugles aux attaques : interdiction d'ouvrir un fichier de batterie, interdiction de lancer le banc de detection, sources limitees au manuel du systeme, a la norme des repertoires, a la grammaire du shell et aux statistiques du seul trafic legitime. Chacune a ensuite ete auditee par un adversaire independant charge de trouver de la connaissance d'attaque contrebandee. **Les quatre sont declarees propres.** Les criteres de succes etaient ecrits et dates avant la mesure.

L'angle de la **rarete** monte a 60,4 % contre 36,7 pour le socle. Et le controle qui existe precisement pour distinguer un juge d'un detecteur d'habitudes, celui des outils etrangers, affichait **2,2 % contre 3,9 %** : mieux que le socle. Il passait tous les criteres.

**Sauf que ce controle tenait en dix-huit commandes ecrites a la main.** Sur dix-huit, l'ecart entre 2,2 % et 3,9 % vaut moins d'une commande. Nous l'avons refait par une commande plutot qu'a la main : soixante outils que le manuel du systeme connait et que cet operateur n'a jamais tapes une seule fois, repartis sur tout l'alphabet, et **480 invocations parfaitement ordinaires**. Aucun fichier de batterie ouvert pour les fabriquer ; le fichier se regenere a l'empreinte identique.

| | detection | ecart au socle (apparie) | etrangers, 18 commandes | **etrangers, 480 commandes** |
|---|---|---|---|---|
| socle aveugle | 36,7 % | | 3,9 % | **5,3 %** |
| « rarete » | 60,4 % | +23,6 (t=11,3) | 2,2 % | **31,7 %** |
| union des quatre | 56,5 % | +19,8 (t=6,3) | 0,0 % | 19,1 % |
| « intensite » | 47,1 % | +10,4 (t=3,5) | 21,7 % | **0,5 %** |
| « segments » | 30,2 % | -6,5 (t=-3,7) | 0,0 % | 12,5 % |
| « arguments » | 26,7 % | -10,0 (t=-7,7) | 0,0 % | 11,3 % |

**La representation qui gagnait vingt-quatre points refuse une commande legitime sur trois**, uniquement parce que l'outil lui est inconnu. Elle ne detecte pas le danger, elle detecte la nouveaute.

**Et le renversement va dans les deux sens.** L'angle « intensite » etait le pire du tableau sur les dix-huit (21,7 %) et devient le meilleur sur les 480 (0,5 %, mieux que le socle). Le petit controle ne manquait pas seulement de puissance : **il classait les deux representations a l'envers**. Sans l'agrandir, nous publiions la mauvaise comme une percee et nous jetions la bonne.

**Un critere que nous avions ecrit sur la mauvaise statistique, publie tel quel.** Il exigeait douze points de gain, tires de l'ecart entre deux tirages isoles. Mais toutes les representations sont mesurees sur les memes graines et les memes decoupages : la comparaison correcte est appariee, et son erreur type est trois a quatre fois plus petite. La barre etait trop haute pour une raison qui ne tient pas. Corriger un critere apres avoir vu le resultat est ce que nous nous interdisons, alors nous donnons les deux lectures : sous le critere tel qu'ecrit, rien ne passe ; sous le test correct, « intensite » est le premier gain honnete au-dessus du plancher, modeste et **sans aucun cout** sur le controle qui compte. Deux angles sont significativement **pires** que le socle, ce qui se publie aussi.

**Le plancher publie reste 36,7 %** : dix points ne tranchent pas la question de fond.

Un dernier controle, dont le resultat est **indecis** et que nous donnons quand meme : le socle augmente de 484 colonnes toujours nulles, donc strictement la meme information avec la largeur d'une union, gagne 3,9 points, et le socle repete dix fois en gagne 2,1. Mais le test apparie ne les etablit pas. On ne peut ni affirmer que la largeur paie toute seule, ni l'exclure. C'est un des auteurs qui a signale ce risque en auditant son propre travail.

### 9. Notre plancher refusait le travail ordinaire. Ce n'etait pas l'outil inhabituel, c'est que notre corpus est coupe a 80 caracteres

Le point precedent remplacait un controle de dix-huit commandes ecrites a la main par 480 produites par une commande. Restait une faiblesse que nous nous etions notee : **ces 480 commandes etaient structurellement pauvres**. Consulter une version, lire une aide, traiter un fichier. Une representation n'a presque rien a y voir.

Durci avec huit formes de plus, tout aussi ordinaires mais riches en structure : boucle sur des fichiers, tuyau vers un tri, sous-shell, deux redirections separees, descente recursive dans un projet, variable d'environnement. Exactement les constructions que ces representations encodent. **960 commandes**, aucune batterie ouverte, fichier regenerable a l'empreinte identique, deux passes rendant le meme resultat au dernier octet.

Resultat : le socle en refuse **16,1 %**, la « rarete » **45,1 %**. La conclusion etait ecrite : *le juge refuse le travail fait avec un outil qu'il ne connait pas*.

**Sauf qu'avant de la publier, nous sommes alles voir ce que contient le corpus d'apprentissage. Il est coupe a quatre-vingts caracteres.** Longueur maximale 80, mediane 80, 64 % des lignes exactement au plafond, **zero commande au-dela de cent**. Or les formes riches du controle font 90 a 100 caracteres. Le modele n'avait jamais vu une commande de cette longueur, avec un outil familier ou non.

**Le controle symetrique qui tranche** : les memes seize formes, gabarits verifies identiques, avec les soixante outils que cet operateur emploie le plus. Si le refus venait de l'outil, il devait s'effondrer.

| | detection | outils etrangers | **memes formes, outils FAMILIERS** | effet outil |
|---|---|---|---|---|
| socle aveugle | 36,7 % | 16,1 % | **7,8 %** | +8,3 |
| « intensite » | 47,1 % | 4,5 % | 7,3 % | **-2,8** |
| socle + 484 colonnes nulles | 40,6 % | 20,4 % | 9,9 % | +10,5 |
| « rarete » | 60,4 % | 45,1 % | **26,0 %** | +19,1 |

**Il ne s'effondre pas : la moitie de ce que nous allions attribuer a l'outil vient de la forme.**

**Nous avons alors ecrit que c'etait la LONGUEUR, et nous l'avons publie sans le mesurer. C'etait faux.** Mesure depuis, par tranche de longueur et sur outils familiers seuls : le refus vaut 0,8 % sous quarante caracteres, culmine a **25,5 % entre soixante et quatre-vingts** (donc **sous** le plafond du corpus) et **retombe a 2,3 % au-dela de quatre-vingts**. Les deux formes les plus longues du controle, 84 et 82 caracteres, sont parmi les plus propres.

**Ce que c'est vraiment : trois constructions.**

| forme | longueur | refus du socle |
|---|---|---|
| `X --list \| grep -v obsolete \| sort > index.txt` | 61 | **45,0 %** |
| `for f in *.txt; do X "$f"; done` | 46 | **29,0 %** |
| `X --config "$HOME/.config/outil.conf" run` | 43 | **18,1 %** |
| `X --input ... --output ... && echo termine` | 84 | 0,3 % |
| `X export --format json > ... 2>...` | 82 | 4,3 % |

Toutes les autres formes sont sous dix pour cent, et ce sont **les memes trois constructions chez les deux representations testees**, ce qui situe le defaut dans le corpus et non dans la representation.

**L'explication evidente etait que ces tournures sont rares dans le corpus. Nous l'avons mesuree avant de l'ecrire, cette fois, et elle ne tient pas non plus.** Les trois formes propres sont bien les plus courantes (l'enchainement par ET logique est dans 28,8 % des commandes du corpus pour 0,3 % de refus ; la redirection simple, 24,1 % pour 4,3 % ; le tuyau unique, 10,6 % pour 0,9 %). Mais le contre-exemple est net : **`xargs` n'apparait qu'une seule fois dans les 9 479 commandes du corpus**, c'est de tres loin la construction la plus rare, et sa forme n'est refusee qu'a 7,1 %. Le sous-shell, 0,17 % du corpus, n'est refuse qu'a 9,7 %. La correlation entre rarete et refus vaut **-0,20** : autant dire rien.

**Nous avons alors cherche plus loin, et nous disons dans quel ordre pour qu'on puisse nous juger.** Trois mesures de rarete essayees successivement, les trois publiees : rarete de la construction dans le **texte** du corpus, r = -0,20, rien ; rarete de la **composante** la plus rare que la forme active, -0,49 ; rarete de la **paire** de composantes la plus rare, **-0,68**.

La progression designe le mecanisme : un auto-encodeur n'apprend pas des frequences isolees, il apprend des motifs **conjoints**. Ce qu'il ne sait pas reconstruire n'est pas une brique rare, c'est une **combinaison** jamais vue, meme quand chaque brique est banale.

**L'exemple est parlant.** La forme la plus refusee, 45 %, est un tuyau a trois etages qui finit par une redirection. Le tuyau multiple apparait dans 5,5 % des commandes du corpus, la redirection dans 9,4 % : deux tournures ordinaires. Mais **ensemble, dans la meme commande, 0,032 %**, soit trois commandes sur 9 479. Le juge ne refuse pas un geste rare, il refuse une **rencontre rare entre deux gestes courants**. La deuxieme forme la plus refusee est la deuxieme paire la plus rare.

**Ce qui reste ouvert, nomme plutot qu'arrondi** : un contre-exemple resiste, le sous-shell forme la troisieme paire la plus rare (0,042 %) et n'est refuse qu'a 9,7 %. Et ces correlations portent sur **seize formes** : -0,68 sur seize points est une **piste, pas un resultat**. Consequence si elle tient : il ne suffira pas d'un corpus contenant des boucles, il faudra un corpus contenant les **combinaisons** ordinaires du travail reel, objet bien plus lourd a rassembler.

**Et ce n'est pas un defaut a reparer, ce qui est le plus interessant.** La coupure est **deliberee** : ce journal n'a jamais ete un jeu de donnees, c'est un resume d'activite ecrit court expres, et d'autres programmes le lisent pour d'autres usages. L'allonger pour arranger notre mesure casserait leur travail. La bonne conclusion n'est donc pas un correctif mais une limite ecrite noir sur blanc : **notre juge apprend sur un corpus constitue pour autre chose**, il ne peut rien savoir des commandes longues, et tous nos taux de refus se lisent avec cette borne.

**Ce qui survit intact** : la « rarete » reste de tres loin la pire, 26 % de refus meme sur des outils familiers et dix-neuf points de plus des que l'outil change, elle detecte bien la nouveaute. Et **« intensite » refuse MOINS sur les outils etrangers que sur les familiers** : aucun biais de nouveaute, avec dix points de detection gagnes. Seul resultat positif de la journee. **La largeur, elle, est un cout** : le socle plus 484 colonnes toujours nulles refuse 20,4 % contre 16,1 au socle nu.

**La regle qui en sort : un controle trop facile ne dit pas seulement moins, il dit faux.** A dix-huit commandes il classait a l'envers. A 480 commandes pauvres il annoncait un plancher propre a 5 %. A 960 realistes il en montre 16, et le controle symetrique montre que la moitie de ces 16 ne parle pas de ce qu'on croyait. Chaque fois, la reponse a ete de rendre la mesure plus dure et de regarder ce qui restait.

## Ce que ces corrections nous apprennent

Regardez ce que la representation honnete rate : un telechargement suivi d'une execution, une ligne ajoutee a un fichier de configuration, une tache planifiee. Ce sont des gestes qu'un operateur fait tous les jours. Au niveau de ce que l'action **fait**, quelle capacite, dans quelle zone, avec quelle structure, une attaque et du travail legitime sont souvent le meme objet.

La conclusion est negative et elle est utile : **la forme d'une action ne suffit pas a la juger.** Il manque ce que l'action etait censee accomplir, et cette information ne peut pas etre tiree du texte de la commande, par aucun modele, si petit ou si grand soit-il. Elle doit etre **declaree par l'agent avant qu'il agisse**. C'est la troisieme couche que la these appelait depuis le debut, et c'est la premiere fois que nous avons un chiffre qui montre pourquoi elle est necessaire plutot qu'elegante.

**37 %, c'est desormais notre plancher honnete, et il est rejouable a l'octet pres.** Tout ce qui montera au-dessus devra le faire sans avoir lu les reponses, et depasser de plus de douze points le bruit propre du banc.

## La methode

- **Prouve, jamais affirme.** Chaque avancee a un critere de succes ecrit avant le test, falsifiable, mesure sur plusieurs graines. Une porte ecrite avant la mesure a attrape trois regressions que nous avions nous-memes introduites en corrigeant.
- **Rejouable et certifie.** Chaque verdict porte un certificat : version de la constitution, entree canonique, verdict, empreinte. Un tiers rejoue et retrouve le meme resultat.
- **Co-evolution a trois roles.** Un proposeur, un solveur, un verificateur, separes. Notre verificateur est exact : le juge lui-meme.
- **Mode ombre avant tout pouvoir.** Le juge a d'abord juge des milliers de vraies commandes sans aucun pouvoir. C'est la qu'on a attrape un sur-blocage de 74 % du travail legitime, corrige avant tout deploiement.
- **Journal infalsifiable.** Chaque verdict est scelle dans un journal en ajout seul ou chaque empreinte enchaine la precedente : falsifier une decision passee casse toute la chaine, et la fraude est detectee au rejeu.
- **Rien ne bouge pendant qu'on mesure.** Les donnees, le code mesure et le code qui mesure sont figes et empreintes avant chaque campagne, et deux lancements identiques doivent rendre le meme fichier a l'octet pres. Notre banc a longtemps appris sur un journal vivant alimente par nos propres commandes de mesure.
- **Audit adversarial avant publication, pas apres.** Nos six dernieres manches ont ete attaquees par des auditeurs independants avant d'etre publiees. Elles n'ont pas survecu. C'est le meilleur usage que nous ayons fait d'une journee.

## Le chemin

1. **J0, cadrage** : grammaire de la constitution, format des actions, certificat de traduction. (Fait)
2. **J1, rejeu a froid** : le juge se mesure contre un adversaire qui cherche ses failles, sur plusieurs domaines. (Fait)
3. **J2, ombre** : verdicts journalises sur des actions reelles, zero pouvoir, mesure de l'accord et du sur-blocage. (Fait)
4. **J3, blocage reel** : d'abord sur les actions reversibles, avec bouton d'arret et instance d'appel humaine.
5. **J4, co-evolution et ouverture** : adversaire branche, durcissement multi-domaines, puis produit.

En travers de ce chemin, une question ouverte que les mesures ci-dessus ont rendue centrale : **comment obtenir de l'agent une declaration d'intention verifiable**, puisque la forme seule de l'action ne suffit pas.

## Contenu de ce depot

Aujourd'hui : ce README, qui reprend le journal de bord public.

A venir, apres une passe de separation entre le generique et l'environnement de test prive : les batteries d'attaques independantes, le verificateur de journal d'attestation, le generateur de lexique de capacites (qui ne depend que du manuel du systeme) et les resultats bruts. Le code de recherche actuel encode des elements de l'environnement de test qui n'ont pas leur place dans un depot public ; on publie propre ou on ne publie pas.

## Journal de bord

Le journal complet, billet par billet et chiffres compris, est sur la page vivante : https://www.weboria.ch/joran/
