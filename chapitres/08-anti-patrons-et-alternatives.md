[← Principes et techniques connexes](07-principes-et-techniques-connexes.md) · [↑ Sommaire](../README.md#table-des-matières) · [Limites, critiques et bon usage →](09-limites-critiques-et-bon-usage.md)

# 8. Anti-patrons et alternatives

## Catalogue des anti-patrons OOP

SOLID se comprend par contraste. Voici les anti-patrons les plus souvent repérés en revue de code (le moment où des collègues relisent le code avant de l'intégrer).

> **Que veut dire « refactoring » ?** Le *refactoring* (« réusinage » en français) consiste à réorganiser du code pour le rendre plus clair sans changer ce qu'il fait, comme ranger un atelier sans modifier les outils. Le mot revient dans plusieurs descriptions ci-dessous : il désigne la correction à apporter.

### God Class (classe-Dieu)

Une classe qui sait tout, fait tout, voit tout. Symptômes : 800+ lignes, 30+ méthodes publiques, dépendances vers la moitié du système. La *God Class* est l'incarnation de la violation simultanée de SRP, ISP et OCP. Refactoring : identifier les *acteurs* qui poussent au changement et extraire un module par acteur.

### Anaemic Domain Model (objet anémique)

Le contraire exact de la classe-Dieu (l'expression anglaise signifie « modèle de domaine anémique ») : des objets qui ne sont que des sacs à *getters/setters*, toute la logique métier se trouvant dans des *services* qui les manipulent de l'extérieur. Critique de Martin Fowler (*Anemic Domain Model*, 2003) : c'est *« procedural code masquerading as objects »* (du code procédural déguisé en objets). Symptôme typique : un domaine de 200 *entités* et 200 *services*, avec zéro méthode métier dans les entités. Violation flagrante du Tell-Don't-Ask et, par ricochet, de l'encapsulation.

> **Que veut dire « entité » et « service » ?** Une *entité* est un objet du métier qui a une identité durable (un `Client`, une `Commande`). Un *service* est un objet sans état propre qui rend un service technique ou orchestre plusieurs entités (envoyer un e-mail, calculer un prix global). L'anémie, c'est quand toute la matière grise est dans les services et que les entités sont vides.

### Service Locator (localisateur de services)

Un objet global (souvent statique, c'est-à-dire accessible de partout sans être instancié) à qui on demande ses dépendances : `ServiceLocator::get(MailerInterface::class)`. Symptômes : on ne peut plus lire la signature d'un constructeur pour savoir de quoi la classe dépend, les tests deviennent fragiles (à cause de l'état partagé globalement), et le DIP est camouflé (on dépend de `ServiceLocator`, pas d'une abstraction propre). Préférer toujours l'injection par constructeur.

### Singleton (mal utilisé)

> **Que veut dire « singleton » ?** Un *singleton* est un objet dont il n'existe qu'un seul exemplaire dans tout le programme, accessible de n'importe où. Comme un registre national unique : pratique, mais dangereux si tout le monde peut y écrire n'importe quand.

Le singleton n'est pas mauvais en soi (configuration immuable, registre de journalisation). Il devient toxique dès qu'il porte un **état modifiable** : on a recréé une variable globale déguisée. Le singleton modifiable casse les tests (impossible d'isoler un cas), masque les dépendances (on ne voit pas qui s'en sert) et bloque l'exécution en parallèle. Règle : si vous écrivez `Foo::getInstance()`, demandez-vous pourquoi vous ne passez pas plutôt `Foo` en argument.

### Smart UI / Presentation Heavy (interface trop savante)

> **Que veut dire « contrôleur » et « vue » ?** Dans une application, le *contrôleur* est l'objet qui reçoit la demande de l'utilisateur et coordonne la réponse ; la *vue* est ce qui affiche le résultat à l'écran. L'anti-patron consiste à entasser les règles métier dans ces deux endroits au lieu de les confier aux objets du domaine.

Toute la logique métier vit dans le contrôleur ou la vue. *« On parcourt les commandes, on filtre celles qui datent de plus de 30 jours, on calcule le total, on affiche. »* Au moindre changement de règle, on doit fouiller dans des gabarits d'affichage (*templates*). Antidote : remettre la logique sur les objets du domaine et réduire la vue à un simple afficheur (*renderer*).

### Feature Envy (envie de fonctionnalité)

Une méthode de la classe A passe son temps à appeler les *getters* de la classe B pour décider quelque chose. C'est qu'en réalité elle appartient à B (Tell-Don't-Ask). Correction : déplacer la méthode vers B (le *Move Method* de Fowler).

### Shotgun Surgery (chirurgie au fusil à pompe)

Le moindre changement métier oblige à toucher dix fichiers. C'est le symptôme inverse de la classe-Dieu : la responsabilité est trop éparpillée. Le nom imagé évoque un tir qui disperse ses plombs partout. Diagnostic fréquent : un découpage horizontal (par couche technique) là où il devrait être vertical (par cas d'usage).

### Refused Bequest (héritage refusé)

Un sous-type *refuse* l'héritage qu'il reçoit : il redéfinit la moitié des méthodes du parent par `throw new UnsupportedOperationException` (lever une erreur « opération non supportée »). Signe qu'on a confondu *est-un* et *partage de code*. La composition, ou une autre interface, aurait été plus juste.

[🔝 Retour en haut de page](#table-des-matières)

## CUPID : l'alternative de Dan North

En 2022, Dan North (l'inventeur du *Behaviour-Driven Development*) propose **CUPID** comme alternative, ou plutôt comme complément, à SOLID, avec un parti pris : remplacer des règles de structure par des *propriétés observables* d'un code agréable à utiliser.

> **Que veut dire « Behaviour-Driven Development » (BDD) ?** En français : « développement piloté par le comportement ». C'est une façon de concevoir un logiciel en décrivant d'abord, en langage courant, comment il doit se comporter (*« étant donné un panier vide, quand j'ajoute un article, alors le total augmente »*), avant d'écrire le code. CUPID, comme SOLID, est un acronyme : ses cinq lettres résument cinq qualités d'un bon code.

| Lettre | Propriété             | Idée                                                                                |
| ------ | --------------------- | ----------------------------------------------------------------------------------- |
| **C**  | **Composable**        | Le module se branche facilement à d'autres ; petite surface, peu de prérequis.      |
| **U**  | **Unix-philosophy**   | Faire une chose, la faire bien ; produire des sorties consommables par d'autres.    |
| **P**  | **Predictable**       | Comportement déterministe, prévisible, observable ; pas d'effets de bord cachés.    |
| **I**  | **Idiomatic**         | Écrit dans le style attendu par le langage et l'écosystème (PSR en PHP, *go fmt*).  |
| **D**  | **Domain-based**      | La structure du code suit la structure du domaine, pas une architecture générique.  |

### Pourquoi CUPID intéresse même un partisan de SOLID

- **Propriétés vs règles** : SOLID dit *quoi faire* ; CUPID dit *à quoi ressemble* un bon code, en laissant le moyen ouvert.
- **Composable > Open/Closed** : on s'intéresse à la facilité d'assemblage plutôt qu'à un point d'extension formel, ce qui coûte souvent moins cher.
- **Predictable** intègre l'observabilité, les journaux d'exécution (*logs*) et les mesures (*métriques*), tous absents de SOLID.
- **Idiomatic** rappelle qu'un code SOLID *non-idiomatique* dans un écosystème lui fait plus de mal qu'un code moins SOLID mais lisible par tous.

> **Que veut dire « idiomatique » et « PSR » ?** Un code *idiomatique* est écrit dans le style habituel d'un langage, comme on parle une langue sans accent étranger. Les *PSR* (*PHP Standard Recommendations*, « recommandations standard PHP ») sont les conventions communes de la communauté PHP ; `go fmt` est l'outil qui impose un style uniforme au langage Go. Respecter ces usages rend le code immédiatement familier aux autres développeurs.

> **Que veut dire « observabilité » et « effet de bord » ?** L'*observabilité* est la capacité à voir ce que fait un programme pendant qu'il tourne (grâce aux journaux et aux mesures), comme les cadrans d'un tableau de bord. Un *effet de bord* est une action cachée d'une fonction en plus de son résultat annoncé (par exemple, écrire un fichier alors qu'on demandait juste un calcul) : ce sont ces surprises que le code prévisible cherche à éviter.
- **Domain-based** rejoint le *Domain-Driven Design* (DDD, Eric Evans, 2003) : organiser le code par cas d'usage métier, pas par couche technique.

> **Que veut dire « Domain-Driven Design » (DDD) ?** En français : « conception pilotée par le domaine ». C'est une approche qui consiste à modeler le code sur le vrai métier de l'entreprise et à parler le même langage que les experts métier. Deux notions y reviennent souvent. Un **agrégat** (*aggregate*) est un petit groupe d'objets qu'on traite comme un tout cohérent avec une porte d'entrée unique : par exemple une `Commande` et ses lignes forment un agrégat, et on passe toujours par la commande pour les modifier (comme un dossier agrafé qu'on ne dépareille pas). Un **contexte borné** (*bounded context*) est une frontière à l'intérieur de laquelle un mot a un sens précis et unique : le mot « client » ne veut pas dire la même chose au service facturation et au service support, donc on délimite deux contextes pour éviter la confusion.

### Verdict honnête

CUPID ne remplace pas SOLID ; il l'enrobe et déplace l'attention. Un module peut respecter SOLID *sur le papier* et ne pas être *composable* (interfaces trop génériques, configuration intrusive). À l'inverse, un module *composable, prévisible, idiomatique* respecte de fait la majorité de SOLID sans qu'on ait besoin de réciter le mantra. À garder dans la boîte à outils intellectuelle, surtout en revue de bibliothèque ou de framework.

[🔝 Retour en haut de page](#table-des-matières)

---

[← Principes et techniques connexes](07-principes-et-techniques-connexes.md) · [↑ Sommaire](../README.md#table-des-matières) · [Limites, critiques et bon usage →](09-limites-critiques-et-bon-usage.md)
