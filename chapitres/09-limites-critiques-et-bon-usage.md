[← Anti-patrons et alternatives](08-anti-patrons-et-alternatives.md) · [↑ Sommaire](../README.md#table-des-matières)

# 9. Limites, critiques et bon usage

## Pièges classiques et idées reçues

Une lecture rapide de SOLID conduit à des contresens qui font plus de mal que de bien. Les voici, principe par principe.

### SRP : la déformation *« faire une seule chose »*

C'est le contresens le plus répandu. *Faire une seule chose* est une consigne fonctionnelle (souvent dérivée de la *philosophie Unix* appliquée aux fonctions) ; *avoir une seule raison de changer* est une consigne d'organisation. Les deux peuvent coïncider, mais la première découpe par *verbe* (lire, valider, formater) tandis que la seule règle valable découpe par *acteur* (qui demande la modification ?). Conséquence : ne pas hacher une classe en une nuée de micro-méthodes au prétexte du SRP, car ce qui compte est l'axe de changement.

> **Que veut dire « philosophie Unix » ?** Unix est une grande famille de systèmes d'exploitation (Linux en descend). Sa philosophie tient en une phrase : *« chaque programme fait une seule chose, mais la fait bien »*, et on relie ces petits programmes entre eux. C'est une vue côté outils, à ne pas confondre avec l'acteur unique du SRP.

### LSP : réduit à tort à la compatibilité de signatures

Un compilateur vérifie déjà la compatibilité de types. Le LSP ajoute une couche **comportementale** : préconditions, postconditions, invariants, historique. Une sous-classe peut compiler et passer le typage tout en violant frontalement le LSP (le `Carre extends Rectangle` en est l'exemple canonique). Une revue LSP regarde les *garanties* du parent et vérifie que le sous-type ne les affaiblit pas.

### DIP : confondu avec la DI

Voir la section dédiée plus haut. Symptôme : on injecte tout par le constructeur en se croyant en règle, mais les services métier dépendent toujours de classes techniques concrètes. Le test reste impossible sans la vraie base de données. Tant que la flèche de dépendance n'est pas inversée *au niveau des types*, le DIP n'est pas appliqué, peu importe la manière dont on fabrique les objets.

### ISP : segmenter à outrance

Une interface par méthode n'est pas l'objectif. L'objectif est qu'aucun client ne soit *forcé* de dépendre de ce qu'il n'utilise pas. Si tous les clients utilisent les sept méthodes d'une interface, segmenter en sept interfaces n'apporte rien et augmente le bruit. La granularité juste se mesure à *l'usage réel des clients*, pas à une règle de cardinalité.

### OCP : fermé pour de mauvaises raisons

Fermer une classe à toute modification, y compris aux corrections de bugs ou aux évolutions internes qui ne touchent pas son contrat, est une lecture intégriste. Le *fermé* du OCP signifie : *fermé pour ajouter des cas de variation prévus*. Refactoriser le corps d'une méthode sans changer son contrat n'est pas une violation de l'OCP. Idem, anticiper toutes les extensions imaginables produit des hiérarchies abstraites mort-nées : on attend la deuxième occurrence du besoin avant de figer un point d'extension.

[🔝 Retour en haut de page](#table-des-matières)

## La controverse : *« seul le S compte »*

Une critique récurrente, popularisée notamment par David Heinemeier Hansson (DHH, créateur de Ruby on Rails) et reprise par une partie du monde Python/Ruby, dit en substance : *« sur les cinq, seul le **S** survit à l'épreuve du quotidien ; les quatre autres sont soit triviaux, soit nocifs s'ils sont appliqués sans nuance »*. La position est trop tranchée pour être adoptée, mais elle mérite d'être prise au sérieux principe par principe.

### L'argument détaillé

- **SRP** est, en pratique, le seul des cinq dont l'absence se traduit *immédiatement* par une douleur observable : fichiers énormes, conflits de *merge*, peur d'éditer. Sa formulation *« une raison de changer »* survit à tous les paradigmes (procédural, OOP, fonctionnel).
- **OCP** est un idéal sur lequel l'expérience tempère l'enthousiasme. *On ne peut pas être ouvert à toutes les variations* (théorème d'impossibilité informel) ; on doit choisir *à quelle dimension* on veut être ouvert. Mal anticipée, l'abstraction crée plus de dette qu'elle n'en évite.
- **LSP** est un théorème, pas une simple règle de bon sens : il dit ce que doit garantir un sous-typage correct. Mais on ne le *« suit »* pas activement ; on ne décide pas chaque matin de respecter le LSP, on découvre une violation au moment d'un bug.

> **Que veut dire « heuristique » et « théorème » ?** Une heuristique est une règle approximative qui marche souvent sans garantie absolue (« si le ciel est gris, prends un parapluie »). Un théorème est une vérité démontrée, toujours vraie quand ses conditions sont réunies. Le LSP relève du second : ce n'est pas un conseil, c'est une condition formelle de substitution correcte.
- **ISP** se ramène, dans 90 % des projets de gestion modernes, à *« n'écrivez pas d'interface obèse »*. Une fois cette règle de bon sens absorbée, l'ISP n'apporte plus grand-chose au quotidien.
- **DIP** est puissant mais s'incarne déjà dans les pratiques courantes (DI par constructeur, ports/adapters). On peut faire excellent code en pensant *hexagonal* sans avoir jamais récité le DIP.

### Notre position

La caricature *« seul le S compte »* sert d'aiguillon utile : elle force à se demander *« quelle douleur précise est-ce que je soigne ? »* avant d'introduire une abstraction. Pour autant, les quatre autres principes ne sont pas redondants :

- **OCP** reste vital dans les *frameworks* et les bibliothèques destinés à des tiers.
- **LSP** reste indispensable dès qu'une hiérarchie sérieuse apparaît, et le simple fait de connaître la *history rule* évite des bugs subtils.
- **ISP** reste pertinent quand l'on conçoit l'interface d'un service partagé.
- **DIP** reste structurant pour quiconque vise une architecture testable.

Le bon résumé : SRP est le **principe que l'on applique tous les jours sans réfléchir** ; les quatre autres sont des **outils dont on connaît l'existence et qu'on dégaine quand le contexte les justifie**. Ce n'est pas la même chose que de les déclarer obsolètes.

[🔝 Retour en haut de page](#table-des-matières)

## Quand NE PAS appliquer SOLID

SOLID a un coût : des classes supplémentaires, des abstractions qui s'intercalent, des indirections que l'éditeur de code (IDE) doit traverser, des dossiers qui se multiplient. Appliqué hors contexte, il produit justement la complexité qu'il prétendait éviter. Voici quelques cas où il vaut mieux *ne pas* l'appliquer.

> **Que veut dire « IDE » ?** IDE veut dire *Integrated Development Environment*, « environnement de développement intégré » en français : le logiciel dans lequel on écrit le code (par exemple VS Code ou PhpStorm). Il aide à naviguer entre les fichiers, d'où la gêne quand les indirections se multiplient.

### Le code est jetable ou expérimental

Un script ETL ponctuel, un *spike* d'une journée pour explorer une API, un script d'administration lancé deux fois par an : ce code n'a pas vocation à durer ni à évoluer sous plusieurs autorités. Y appliquer SOLID coûte plus cher que le code lui-même.

> **Que veut dire « ETL », « spike » et « API » ?** Un *ETL* (*Extract, Transform, Load*, « extraire, transformer, charger ») est un traitement qui récupère des données quelque part, les nettoie, puis les range ailleurs. Un *spike* est un petit bout de code écrit vite pour explorer une idée, qu'on jette ensuite. Une *API* (*Application Programming Interface*, « interface de programmation ») est la porte d'entrée par laquelle un programme dialogue avec un autre, comme un menu de commande standardisé entre deux logiciels.

### Le domaine est petit, stable et bien compris

Si la classe tient sur un écran et qu'aucune variation n'est anticipée, la fragmenter en cinq classes pour respecter le SRP au pied de la lettre nuit à la lisibilité. La règle de **Sandi Metz** s'applique : *« make the change easy (warning: this may be hard), then make the easy change »* (rendez le changement facile, ce qui peut être difficile, puis faites le changement devenu facile). On n'introduit l'abstraction qu'au moment où le besoin de variation se manifeste, pas avant.

### La règle des trois usages (Fowler)

Tant qu'un cas de variation est unique, on l'écrit en dur. Au deuxième cas, on commence à voir l'axe de variation et on peut le coder un peu mieux. Au troisième, on extrait une abstraction. Refactoriser dès le premier cas, c'est se tromper d'axe de variation neuf fois sur dix.

### YAGNI et la critique de DHH

> **Que veut dire « YAGNI » ?** YAGNI est l'abréviation de *You Aren't Gonna Need It*, littéralement « vous n'en aurez pas besoin ». C'est un rappel à ne pas coder par avance des fonctions dont on n'a pas encore l'usage : on ajoute la complexité quand le besoin réel arrive, jamais « au cas où ».

> **Qui est DHH ?** DHH sont les initiales de David Heinemeier Hansson, programmeur danois, créateur du framework Ruby on Rails. Il est connu pour ses prises de position tranchées contre l'excès d'abstraction.

David Heinemeier Hansson a popularisé une critique pragmatique de SOLID : appliqué sans discernement à des applications de gestion ordinaires, il empile des couches de ports, d'adaptateurs et de services pour rien, là où un simple *Active Record* fait le travail. Sa formule provocatrice, *« I won't be ridiculed by a Java enterprise architecture astronaut »* (je ne me laisserai pas tourner en ridicule par un astronaute de l'architecture d'entreprise à la Java), vise l'usage rituel de SOLID, pas les principes eux-mêmes. La leçon : SOLID est un outil, pas une religion ; on le sort quand le couplage fait *concrètement* mal.

> **Que veut dire « Active Record » ?** *Active Record* est une façon simple de relier un objet à une ligne de base de données : l'objet `Client` sait lui-même se sauvegarder et se charger (`client.save()`). C'est pratique pour les petites applications, mais cela mélange métier et accès aux données, ce que le DIP cherche justement à séparer dans les gros projets.

### Sur-ingénierie : les signaux d'alerte

- Plus d'interfaces que de classes qui les implémentent.
- Une seule implémentation par interface, sans intention de doublure de test ni de variante.
- Des classes minuscules dont les méthodes ne font qu'appeler la suivante, sans logique propre.
- Des hiérarchies abstraites prévues pour des cas qui ne se sont jamais matérialisés.
- Le temps de naviguer du contrôleur à l'effet réel (DB, HTTP, fichier) dépasse la durée de l'opération elle-même.

### Le bon réflexe

Quand on hésite à appliquer un principe, poser deux questions :
1. *Quel coût concret me fait éviter cette violation ?* (test difficile, déploiement bloqué, équipes qui se marchent dessus, fichier qui change quatre fois par sprint)
2. *Quel coût concret introduit l'abstraction que j'ajoute ?* (un fichier de plus, une indirection, un binding DI, une revue qui doit naviguer dans cinq fichiers)

Si la réponse à (1) reste théorique, on n'applique pas. SOLID rapporte exactement à hauteur de la douleur qu'il soigne ; en l'absence de douleur, c'est une charge nette.

[🔝 Retour en haut de page](#table-des-matières)

---

[← Anti-patrons et alternatives](08-anti-patrons-et-alternatives.md) · [↑ Sommaire](../README.md#table-des-matières)
