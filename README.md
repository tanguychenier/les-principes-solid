# [Tansoftware](https://www.tansoftware.com) - Les principes SOLID [![fr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/France.png)](README.md)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE) [![Lang](https://img.shields.io/badge/Lang-Français-005EB8.svg)](#) [![Topic](https://img.shields.io/badge/Topic-SOLID-brightgreen.svg)](#) [![Examples](https://img.shields.io/badge/Examples-PHP-777BB4.svg)](#)

## Table des matières

- [Introduction](#introduction)
- [Glossaire : vocabulaire indispensable](#glossaire--vocabulaire-indispensable)
- [Single Responsibility Principle (SRP)](#single-responsibility-principle-srp)
- [Open/Closed Principle (OCP)](#openclosed-principle-ocp)
- [Liskov Substitution Principle (LSP)](#liskov-substitution-principle-lsp)
- [Interface Segregation Principle (ISP)](#interface-segregation-principle-isp)
- [Dependency Inversion Principle (DIP)](#dependency-inversion-principle-dip)
- [Couplage et cohésion : la métrique sous SOLID](#couplage-et-cohésion--la-métrique-sous-solid)
- [Composition plutôt qu'héritage](#composition-plutôt-quhéritage)
- [Tell-Don't-Ask : la conséquence pratique](#tell-dont-ask--la-conséquence-pratique)
- [Catalogue des anti-patrons OOP](#catalogue-des-anti-patrons-oop)
- [CUPID : l'alternative de Dan North](#cupid--lalternative-de-dan-north)
- [Pièges classiques et idées reçues](#pièges-classiques-et-idées-reçues)
- [La controverse : *« seul le S compte »*](#la-controverse--seul-le-s-compte)
- [Quand NE PAS appliquer SOLID](#quand-ne-pas-appliquer-solid)
- [Pour aller plus loin](#pour-aller-plus-loin)

## Introduction

SOLID est un acronyme : chacune de ses cinq lettres est l'initiale d'un principe de conception logicielle. Il a été forgé par Michael Feathers à partir de cinq principes rassemblés par [Robert C. Martin](http://cleancoder.com/products), alias *Uncle Bob*, dans son livre *[Agile Software Development: Principles, Patterns, and Practices](https://www.amazon.fr/Software-Development-Principles-Patterns-Practices/dp/0135974445)* (2002).

> **Que veut dire « acronyme » ?** Un acronyme est un mot fabriqué avec la première lettre de plusieurs autres mots, pour qu'on le retienne plus facilement. Comme « OVNI » (Objet Volant Non Identifié). Ici, S-O-L-I-D résume cinq règles dont chaque lettre est le rappel d'une.

> **Que veut dire « conception orientée objet » ?** C'est une façon d'écrire un programme où l'on regroupe ensemble des données (par exemple le solde d'un compte) et les actions qui les manipulent (déposer, retirer) à l'intérieur d'un même « objet ». On parle aussi de POO (Programmation Orientée Objet), ou OOP en anglais (Object-Oriented Programming). C'est comme un appareil ménager : le four cache sa mécanique interne et n'expose que des boutons. SOLID donne des règles pour fabriquer de bons « objets » de ce genre.

Ces cinq principes répondent chacun à un symptôme précis d'un code orienté objet devenu difficile à maintenir : trop de raisons de changer (SRP), modifications qui rejaillissent partout (OCP), héritage qui casse les programmes qui s'en servent (LSP), interfaces surchargées (ISP), code dépendant de détails techniques (DIP). L'ordre des lettres n'est pas un hasard : SRP pose la notion de responsabilité que les quatre suivants supposent déjà connue.

SOLID n'est pas une liste de cases à cocher : c'est une grille de lecture. Aucun de ces principes n'est une loi absolue. Chacun a un coût (des classes supplémentaires, des abstractions, des indirections) et un bénéfice (de la stabilité, de la facilité à tester, une liberté d'évolution). On commence donc par le vocabulaire qui rend les définitions compréhensibles, puis on traite chaque principe avec sa formulation exacte, ses exemples en PHP, ses pièges et le moment où il vaut mieux ne pas l'appliquer.

> **Que veut dire « abstraction » et « indirection » ?** Une abstraction est une description simplifiée qui cache les détails (le mot « véhicule » au lieu de « moteur thermique à quatre cylindres »). Une indirection, c'est passer par un intermédiaire au lieu d'agir directement (téléphoner à un standard plutôt qu'à la personne). Les deux notions reviennent sans cesse dans SOLID.

[🔝 Retour en haut de page](#table-des-matières)

## Glossaire : vocabulaire indispensable

Les principes mobilisent un vocabulaire précis. Chaque définition ci-dessous reste courte et orientée pratique, avec une comparaison du quotidien.

### Abstraction

Représentation simplifiée d'un concept qui ne retient que ce qui intéresse celui qui s'en sert et masque le reste. En POO (Programmation Orientée Objet), une abstraction prend la forme d'une **interface** ou d'une **classe abstraite** (deux notions définies juste après). *« Envoyer un courriel »* est une abstraction ; *« ouvrir une connexion réseau sur le port 587, négocier le chiffrement, etc. »* est un détail d'implémentation.

> **Analogie.** Le mot « transport » est une abstraction : il vous dit qu'on va vous déplacer, sans préciser train, bus ou taxi. Vous raisonnez avec le mot « transport » sans avoir à connaître la mécanique de chaque véhicule.

> **Que veut dire « appelant » ?** L'appelant est le morceau de code qui se sert d'un autre morceau de code. Si la fonction A utilise la fonction B, alors A est l'appelant de B. Comme un client (appelant) qui passe commande à un serveur de restaurant (le code appelé).

### Interface

Contrat purement déclaratif : une liste de signatures de méthodes (le nom de chaque opération, ses paramètres, son type de retour, les erreurs possibles) sans le code qui les exécute. Une classe qui *implémente* l'interface s'engage à fournir un comportement conforme à ce contrat. En PHP comme en Java, c'est le mot-clé `interface` ; en C#, idem (avec un `I` en préfixe par convention).

> **Analogie.** Une interface est comme une fiche de poste : elle dit *« cette personne doit savoir cuisiner, servir, encaisser »* sans préciser *comment*. N'importe qui qui sait faire ces trois choses peut occuper le poste.

> **Que veut dire « méthode » et « signature » ?** Une méthode est une action qu'un objet sait faire (un verbe : `deposer`, `imprimer`). Sa signature est sa carte d'identité : son nom, ce qu'on lui donne en entrée et ce qu'elle renvoie. Comme la fiche d'un appareil qui indique les prises et les boutons sans montrer les circuits.

### Classe abstraite

Classe à moitié écrite : elle peut contenir du code utile mais comporte au moins une action laissée vide (méthode abstraite) que les classes filles devront compléter. On l'utilise pour mettre en commun un comportement partagé (le patron *template method*, vu plus loin). Différence clé avec une interface : une classe ne peut hériter que d'**une seule** classe abstraite, alors qu'elle peut implémenter **plusieurs** interfaces.

> **Analogie.** Une classe abstraite est une recette à trous : *« faire revenir les oignons, ajouter VOTRE viande, laisser mijoter »*. Le squelette est fourni, mais chaque cuisinier remplit le trou (la viande) à sa façon.

### Héritage

Relation *est-un* : `Chien` hérite d'`Animal` (un chien *est un* animal). La classe fille récupère la structure et le comportement de la classe mère et peut les redéfinir (en anglais `override`, « redéfinir par-dessus »). L'héritage lie fortement les deux classes : tout changement dans la mère se répercute sur les filles. À utiliser avec modération ; voir LSP pour les pièges.

> **Analogie.** L'héritage, c'est recevoir les traits de ses parents : vous tenez d'eux les yeux et la taille (la structure) et certaines habitudes (le comportement), que vous pouvez ensuite modifier un peu chez vous.

### Composition

Relation *a-un* : `Voiture` a un `Moteur` (une voiture *contient* un moteur, elle n'*est pas* un moteur). La voiture garde le moteur comme pièce interne, plutôt que d'en hériter. La composition remplace souvent avantageusement l'héritage : elle évite le lien hiérarchique rigide et permet d'échanger la pièce pendant que le programme tourne. Adage : *« favor composition over inheritance »* (préférez la composition à l'héritage), formule du collectif Gang of Four en 1994.

> **Analogie.** Une lampe *a* une ampoule : elle ne descend pas de l'ampoule, elle l'abrite. Vous changez l'ampoule sans changer la lampe. C'est tout l'intérêt de la composition.

> **Que veut dire « Gang of Four » ?** C'est le surnom de quatre auteurs (Gamma, Helm, Johnson, Vlissides) d'un livre fondateur de 1994 sur les « patrons de conception », des solutions toutes faites à des problèmes récurrents de code. « Gang des quatre » en français.

### Polymorphisme

Capacité d'une même demande à déclencher plusieurs comportements selon le type réel de l'objet. Quand on appelle `forme.aire()` sur une variable annoncée comme une `Forme`, le code réellement exécuté dépend du type concret derrière (`Cercle`, `Rectangle`...). Le polymorphisme est le mécanisme qui rend OCP, LSP et DIP utilisables en pratique.

> **Analogie.** Vous dites *« fais du bruit »* à un animal : le chien aboie, le chat miaule, le canard cancane. Une seule consigne, plusieurs réponses selon l'animal réel. C'est le polymorphisme (du grec *poly*, plusieurs, et *morphê*, forme).

### Sous-typage

Relation formelle qui dit que tout objet du sous-type est *acceptable* partout où le supertype est attendu. C'est le sens technique de *« hérite de »* ou *« implémente »*. Le LSP ajoute une condition : le sous-typage doit aussi être *comportemental* (le sous-type ne doit pas mentir sur le contrat).

> **Que veut dire « supertype » et « sous-type » ?** Le supertype est la catégorie large (`Animal`) ; le sous-type est la catégorie précise qui en relève (`Chien`). Un chien étant un animal, partout où l'on attend un animal, un chien fait l'affaire.

### Contrat

Ensemble des engagements qu'une opération prend envers ceux qui l'appellent. Il se compose de :
- **Préconditions** : ce que l'appelant doit garantir avant l'appel (par exemple : l'argument ne doit pas être vide).
- **Postconditions** : ce que l'opération garantit après coup (par exemple : la liste renvoyée est triée).
- **Invariants** : propriétés toujours vraies, avant et après chaque opération publique (par exemple : le solde ne devient jamais négatif).

Le terme vient de *Design by Contract* (« conception par contrat »), proposé par Bertrand Meyer avec le langage Eiffel.

> **Analogie.** Un contrat de livraison : vous devez fournir une adresse valide (précondition), le livreur garantit le colis intact avant 18 h (postcondition), et l'entrepôt promet de ne jamais perdre votre commande tout au long du processus (invariant).

### Couplage

Degré de dépendance entre deux modules. Couplage *fort* : un changement dans A oblige à modifier B. Couplage *faible* : A et B communiquent par un contrat stable, et on peut changer l'un sans toucher l'autre. SOLID cherche à réduire le couplage là où il fait mal.

> **Que veut dire « module » ?** Un module est un bloc de code regroupé en unité cohérente : une classe, un fichier, un paquet. Comme une pièce détachée d'une machine, qu'on peut considérer séparément.

> **Analogie.** Deux personnes fortement couplées finissent les phrases l'une de l'autre : si l'une change d'avis, l'autre est perdue. Faiblement couplées, elles s'échangent juste des messages clairs et restent autonomes.

### Cohésion

Degré de parenté entre les éléments d'un même module. Cohésion *forte* : les méthodes d'une classe travaillent toutes sur les mêmes données pour un même but. Cohésion *faible* : la classe est un fourre-tout. Le SRP est, en pratique, un guide vers une forte cohésion.

> **Analogie.** Une boîte à outils bien rangée où tout sert à réparer un vélo (cohésion forte) contre un tiroir fourre-tout où traînent un tournevis, un trombone et une pile usée (cohésion faible).

### Covariance / contravariance

Comportement des types quand on affine une signature dans une classe fille.
- **Covariance** : le type renvoyé par une méthode redéfinie peut être *plus précis* que dans la classe mère (un sous-type). Exemple : si `Animal::nourrir()` renvoie un `Animal`, `Vache::nourrir()` peut renvoyer une `Vache`.
- **Contravariance** : le type d'un paramètre peut être *plus général* (un supertype). C'est rare en PHP et Java, mais autorisé pour rester compatible avec le LSP.

Règle pour s'en souvenir : *« on accepte plus, on rend moins »*. Les paramètres se relâchent (contravariance), les retours se resserrent (covariance).

> **Analogie.** Un restaurant qui accepte plus de clients à l'entrée (contravariance) mais sert une assiette mieux définie à la sortie (covariance) reste compatible avec son ancienne carte : personne n'est pris au dépourvu.

### Inversion de contrôle (IoC) et injection de dépendances (DI)

> **Que veut dire « dépendance » ?** Une dépendance est un outil dont un morceau de code a besoin pour fonctionner (une connexion à la base de données, un service d'envoi d'e-mails). Comme un cuisinier qui dépend de son four : sans lui, pas de plat.

- **Inversion de contrôle** (IoC, de l'anglais *Inversion of Control*) : le module ne fabrique plus lui-même ses dépendances, il les *reçoit* de l'extérieur. C'est un principe.
- **Injection de dépendances** (DI, de l'anglais *Dependency Injection*) : la *technique* qui réalise l'IoC, le plus souvent en passant les dépendances dans le constructeur (injection par constructeur), via une méthode dédiée (*setter*), ou via un conteneur DI (un magasin central qui distribue les outils).

> **Analogie.** Au lieu d'aller fabriquer vous-même votre four (le module crée sa dépendance), on vous livre un four prêt à l'emploi dans votre cuisine (on vous l'injecte). Vous pouvez ainsi recevoir un vrai four, ou un faux four pour faire des essais sans cuisson.

À ne pas confondre avec le **DIP** : le DIP dit *qui dépend de quoi* (haut niveau, bas niveau, abstractions) ; la DI dit *comment* on fournit concrètement la dépendance.

### Les cinq sigles

> **Que veut dire « sigle » ?** Un sigle est une suite d'initiales qu'on lit lettre par lettre, comme SNCF. Les cinq sigles de SOLID résument chacun un principe :

- **SRP**, *Single Responsibility Principle* (principe de responsabilité unique) : une seule raison de changer.
- **OCP**, *Open/Closed Principle* (principe ouvert/fermé) : ouvert à l'extension, fermé à la modification.
- **LSP**, *Liskov Substitution Principle* (principe de substitution de Liskov) : un sous-type doit honorer le contrat du parent.
- **ISP**, *Interface Segregation Principle* (principe de ségrégation des interfaces) : aucune dépendance imposée à des méthodes inutilisées.
- **DIP**, *Dependency Inversion Principle* (principe d'inversion des dépendances) : dépendre d'abstractions, pas de détails.

```mermaid
flowchart TD
    SOLID["SOLID : 5 principes de conception orientée objet"]
    SOLID --> S["S : SRP, une seule raison de changer"]
    SOLID --> O["O : OCP, ouvert a l'extension, ferme a la modification"]
    SOLID --> L["L : LSP, un sous-type honore le contrat du parent"]
    SOLID --> I["I : ISP, pas de methodes imposees inutiles"]
    SOLID --> D["D : DIP, dependre d'abstractions, pas de details"]
```

[🔝 Retour en haut de page](#table-des-matières)

## Single Responsibility Principle (SRP)

> *« Une classe ne doit avoir qu'une seule raison de changer. »* (Robert C. Martin)

> **Que veut dire « classe » ?** Une classe est le moule à partir duquel on fabrique des objets. La classe `Voiture` décrit ce qu'est une voiture (ses données, ses actions) ; chaque voiture concrète sur la route est un objet issu de ce moule. C'est comme un plan d'architecte : un seul plan, plusieurs maisons construites dessus.

### La définition exacte (et pourquoi elle est mal lue)

La lecture populaire est *« une classe ne doit faire qu'une seule chose »*. Cette formulation est fausse, ou au mieux trompeuse : la plupart des classes utiles font *plusieurs choses* (un *repository* ouvre une transaction, prépare ses paramètres, exécute une requête, range le résultat). Ce que dit Martin est plus précis : **une classe ne doit avoir qu'une seule raison de changer**, autrement dit elle ne doit répondre qu'à un seul *acteur*, un seul groupe de personnes susceptibles d'en demander la modification.

> **Que veut dire « repository » ?** Un *repository* (« dépôt » en français) est l'objet chargé de ranger et de retrouver des données, en général dans une base. Comme un magasinier : on lui demande *« range ce dossier »* ou *« retrouve-moi le client n°42 »* sans savoir comment ses étagères sont organisées.

> **Que veut dire « acteur » ici ?** L'acteur est la personne ou le groupe qui pourrait réclamer un changement : une équipe, un service de l'entreprise, un rôle métier (le marketing, le juridique, la production). Demandez-vous toujours *« qui viendrait frapper à la porte pour faire modifier cette classe ? »*.

Reformulation plus tardive de Martin lui-même (dans *Clean Architecture*) : *« A module should be responsible to one, and only one, actor »* (un module ne doit rendre de comptes qu'à un seul acteur). C'est l'**axe de changement** : si deux personnes très différentes peuvent vouloir modifier la même classe pour des raisons sans rapport entre elles, la classe porte deux responsabilités.

### SRP n'est pas la cohésion

La cohésion mesure si les éléments d'une classe travaillent ensemble ; le SRP mesure s'ils changent ensemble. Une classe peut être très cohésive (toutes ses méthodes manipulent un `Client`) tout en violant le SRP (parce qu'elle mêle persistance, validation et formatage d'export, trois préoccupations qui évoluent à des rythmes différents et sous des autorités différentes). Le SRP est donc plus strict : il regarde le *moteur* de l'évolution, pas seulement la *parenté thématique* des méthodes.

### Trouver l'axe de changement : méthode pratique

Devant une classe suspecte, posez les questions :
1. *Qui* demanderait à modifier cette méthode ? (un nom de personne, d'équipe ou de département)
2. Si je liste ces *qui* pour chaque méthode, en obtient-on un seul ou plusieurs ?
3. Pour chaque méthode, quelle est la fréquence de changement et son origine (réglementaire, métier, technique, esthétique) ?

Si la réponse à (2) est *plusieurs*, séparer. Les axes typiques : *infrastructure* (DBA, ops), *communication* (marketing, mailing), *règles métier* (produit), *présentation* (UX), *conformité* (juridique, sécurité).

### À éviter

```php
class Utilisateur {
    public function __construct(public string $email, public string $motDePasse) {}

    public function enregistrer(): void {
        // accès à la base : concerne l'équipe infrastructure
        $pdo = new PDO('mysql:host=...;dbname=...');
        $pdo->prepare('INSERT INTO users ...')->execute([...]);
    }

    public function envoyerCourrielBienvenue(): void {
        // accès au SMTP : concerne l'équipe communication
        mail($this->email, 'Bienvenue', '...');
    }

    public function validerEmail(): bool {
        // règle métier : concerne l'équipe produit
        return filter_var($this->email, FILTER_VALIDATE_EMAIL) !== false;
    }
}
```

Trois acteurs (infrastructure, communication, produit) modifient la même classe. Toute évolution du SMTP impose de retester la persistance, et la moindre règle de validation oblige à redéployer un objet qui touche la base.

### À préférer

```php
final class Utilisateur {
    public function __construct(public readonly string $email, public readonly string $motDePasse) {}
    public function emailValide(): bool {
        return filter_var($this->email, FILTER_VALIDATE_EMAIL) !== false;
    }
}

final class UtilisateurRepository {
    public function __construct(private PDO $pdo) {}
    public function enregistrer(Utilisateur $u): void { /* ... */ }
}

final class CourrielBienvenue {
    public function __construct(private Mailer $mailer) {}
    public function envoyer(Utilisateur $u): void { /* ... */ }
}
```

Chaque classe a un seul propriétaire et peut être modifiée sans craindre de casser les autres. Le test devient linéaire : on teste la persistance contre une base, la validation comme une fonction pure, l'envoi avec un *Mailer* en doublure.

### Quand assouplir

Pour un script utilitaire de quelques dizaines de lignes, multiplier les classes ajoute du bruit sans bénéfice. Le SRP devient rentable dès qu'une classe est touchée par plusieurs développeurs ou plusieurs équipes, ou que sa taille dépasse l'écran. Avant ce seuil, on accepte une classe *un peu trop large* pour rester lisible.

```mermaid
classDiagram
    class Utilisateur {
        +email: string
        +motDePasse: string
        +emailValide(): bool
    }
    class UtilisateurRepository {
        -pdo: PDO
        +enregistrer(u: Utilisateur): void
    }
    class CourrielBienvenue {
        -mailer: Mailer
        +envoyer(u: Utilisateur): void
    }
    UtilisateurRepository --> Utilisateur : persiste
    CourrielBienvenue --> Utilisateur : notifie
```

[🔝 Retour en haut de page](#table-des-matières)

## Open/Closed Principle (OCP)

> *« Une entité logicielle doit être ouverte à l'extension, fermée à la modification. »* (Bertrand Meyer, 1988, repris par R. C. Martin.)

### Lire la définition

*Ouverte à l'extension* : on doit pouvoir ajouter un comportement. *Fermée à la modification* : sans avoir à toucher au code existant pour le faire. La promesse n'est pas que le fichier ne change *jamais* (corriger un bug reste légitime) mais qu'**ajouter un cas n'oblige pas à modifier ce qui marche déjà**.

> **Analogie.** Une multiprise est ouverte à l'extension (vous branchez un nouvel appareil) et fermée à la modification (vous n'avez pas besoin de rouvrir la multiprise ni de refaire le câblage du mur). Bien conçu, le code se rallonge sans devoir être rouvert.

Le mécanisme qui rend cela possible est le **polymorphisme** : on définit un point d'extension (une interface ou une classe abstraite) et chaque nouveau cas devient une *nouvelle classe* qui se branche dessus. Deux patrons en sont l'incarnation directe :
- **Strategy** (« stratégie ») : on remplace tout l'algorithme (`Forme::aire`).
- **Template Method** (« patron de méthode ») : on hérite d'un squelette et on ne redéfinit que les étapes qui varient.

> **Que veut dire « patron de conception » (design pattern) ?** C'est une solution éprouvée à un problème de code qui revient souvent, comme une recette de cuisine connue. *Strategy* et *Template Method* sont deux de ces recettes.

### Pourquoi

Modifier une classe stable, c'est risquer de casser tout le code qui en dépendait. L'extension par polymorphisme isole le nouveau cas dans une nouvelle classe, ce qui rend le risque de régression strictement local au nouveau code.

### À éviter

```php
final class CalculAire {
    public function calculer(array $formes): float {
        $total = 0.0;
        foreach ($formes as $f) {
            if ($f instanceof Rectangle) {
                $total += $f->largeur * $f->hauteur;
            } elseif ($f instanceof Cercle) {
                $total += M_PI * $f->rayon ** 2;
            }
            // chaque nouvelle forme = nouveau elseif ici
        }
        return $total;
    }
}
```

Ajouter un triangle exige de modifier `CalculAire`, donc de retester ce qui marchait déjà. La cascade de `instanceof` est presque toujours le signe d'un OCP non appliqué.

### À préférer (interface, patron strategy)

```php
interface Forme {
    public function aire(): float;
}

final class Rectangle implements Forme {
    public function __construct(public float $largeur, public float $hauteur) {}
    public function aire(): float { return $this->largeur * $this->hauteur; }
}

final class Cercle implements Forme {
    public function __construct(public float $rayon) {}
    public function aire(): float { return M_PI * $this->rayon ** 2; }
}

final class Triangle implements Forme {
    public function __construct(public float $base, public float $hauteur) {}
    public function aire(): float { return $this->base * $this->hauteur / 2; }
}

final class CalculAire {
    /** @param Forme[] $formes */
    public function calculer(array $formes): float {
        return array_sum(array_map(fn(Forme $f) => $f->aire(), $formes));
    }
}
```

Ajouter une nouvelle forme se fait par une nouvelle classe ; `CalculAire` reste fermé.

### À préférer (classe abstraite, patron template method)

Lorsque les sous-classes partagent un *squelette* (ouverture transaction, logique commune, fermeture) mais varient sur une *étape*, une classe abstraite est plus économe :

```php
abstract class RapportPdf {
    final public function generer(string $cible): void {
        $this->ouvrir($cible);
        $this->ecrireEntete();
        $this->ecrireCorps();   // étape variable
        $this->ecrirePiedDePage();
        $this->fermer();
    }
    abstract protected function ecrireCorps(): void;
    protected function ecrireEntete(): void { /* défaut */ }
    protected function ecrirePiedDePage(): void { /* défaut */ }
    private function ouvrir(string $cible): void { /* ... */ }
    private function fermer(): void { /* ... */ }
}

final class RapportVentes extends RapportPdf {
    protected function ecrireCorps(): void { /* tableau des ventes */ }
}
```

Pour ajouter `RapportStock`, on hérite ; `RapportPdf` reste fermé.

### Quand assouplir

Anticiper toutes les extensions possibles produit des hiérarchies abstraites surdimensionnées. Le bon moment pour appliquer l'OCP est lors de la **deuxième** apparition d'un cas de variation, jamais du premier (cf. règle des trois usages de Martin Fowler). Dit autrement : on attend de voir l'axe de variation *réel* avant de figer un point d'extension. Un OCP appliqué à l'aveugle produit autant de dette qu'un OCP absent.

[🔝 Retour en haut de page](#table-des-matières)

## Liskov Substitution Principle (LSP)

> *« Si S est un sous-type de T, alors les objets de type T peuvent être remplacés par des objets de type S sans altérer les propriétés du programme. »* (Barbara Liskov, 1987.)

> **Qui est Barbara Liskov ?** Une informaticienne américaine, lauréate du prix Turing (l'équivalent du prix Nobel en informatique). Le principe porte son nom car elle l'a formulé. L'idée : remplacer un objet par un autre de la même famille ne doit rien casser.

> **Analogie.** Si une recette dit *« un fruit »*, remplacer la pomme par une poire ne doit pas faire échouer la recette. Si soudain on glisse un citron qui rend le gâteau immangeable, c'est que le « citron » ne respecte pas le contrat attendu d'un fruit dessert : voilà une violation à la Liskov.

### Le contrat : formulation rigoureuse

Le LSP n'est pas une simple règle de compatibilité de types (le compilateur s'en charge déjà). C'est une règle de compatibilité **comportementale**. Pour que `S` puisse réellement se substituer à `T`, quatre conditions doivent tenir simultanément :

1. **Les préconditions ne peuvent pas être renforcées** dans le sous-type. Si `T::operation` accepte tout entier, `S::operation` ne peut pas exiger un entier strictement positif : un appelant qui passait `-3` à `T` casserait avec `S`.
2. **Les postconditions ne peuvent pas être affaiblies** dans le sous-type. Si `T::charger` garantit *« retourne une liste triée »*, `S::charger` ne peut pas retourner une liste non triée : un appelant qui comptait sur le tri serait pris en défaut.
3. **Les invariants de la classe mère doivent être préservés**. Si `CompteBancaire` invariant *« solde >= -plafondAutorisé »*, aucun sous-type ne peut autoriser un état plus permissif.
4. **La règle d'historique** (*history rule*, formulée par Liskov & Wing en 1994) : un sous-type ne doit pas autoriser des transitions d'état que le supertype interdisait. Exemple : si `PointImmuable::deplacer` n'existe pas, hériter pour ajouter une méthode mutante viole le LSP même si la signature reste compatible.

Côté typage statique, ces règles se traduisent par : paramètres **contravariants**, retours **covariants**, exceptions levées au plus aussi nombreuses que celles du parent.

```mermaid
flowchart TD
    Q["Un sous-type S remplace son parent T"]
    Q --> C1["1. Preconditions : ne pas exiger PLUS que le parent"]
    Q --> C2["2. Postconditions : ne pas garantir MOINS que le parent"]
    Q --> C3["3. Invariants : preserver les regles toujours vraies du parent"]
    Q --> C4["4. Regle d'historique : pas de transition d'etat interdite par le parent"]
    C1 --> OK["Si les 4 tiennent : substitution sure"]
    C2 --> OK
    C3 --> OK
    C4 --> OK
```

### Pourquoi

Un appelant qui croit manipuler une `T` et reçoit une `S` doit pouvoir continuer son travail. Sinon, le polymorphisme devient un piège : chaque appelant doit tester *quel sous-type* il a vraiment, ce qui ramène le code à la cascade d'`instanceof` que l'OCP cherchait précisément à éviter.

### À éviter : l'archétype Carré/Rectangle

```java
class Rectangle {
    protected int largeur, hauteur;
    public void setLargeur(int l) { this.largeur = l; }
    public void setHauteur(int h) { this.hauteur = h; }
    public int aire() { return largeur * hauteur; }
}

class Carre extends Rectangle {
    @Override public void setLargeur(int l) { this.largeur = l; this.hauteur = l; }
    @Override public void setHauteur(int h) { this.largeur = h; this.hauteur = h; }
}
```

Test révélateur :

```java
void testRectangle(Rectangle r) {
    r.setLargeur(5);
    r.setHauteur(4);
    assertEquals(20, r.aire());
}

testRectangle(new Rectangle()); // OK
testRectangle(new Carre());     // Échoue : aire() = 16, pas 20
```

Analyse détaillée des violations :
- **Postcondition affaiblie** : après `setLargeur(5)`, `Rectangle` garantit *« largeur == 5 et hauteur inchangée »*. `Carre` casse la seconde moitié.
- **Invariant ajouté** : `Carre` impose `largeur == hauteur`, invariant absent du parent. Tout code qui comptait sur l'indépendance des deux dimensions est cassé.
- **Règle d'historique** : `Rectangle` autorisait la suite d'états *(5, 4)*. `Carre` rend cette suite inatteignable depuis `setLargeur` puis `setHauteur`. Une transition légitime du parent disparaît.

En théorie des ensembles, un carré est un rectangle ; en programmation, l'héritage d'implémentation introduit un couplage que la mathématique n'a pas. La leçon est plus large : *« est-un » dans le langage métier n'implique pas « est sous-type de »* au sens du LSP.

### À préférer

Soit on n'hérite pas (le carré n'est *pas* un rectangle modifiable), soit on rend les formes immuables. Dans ce dernier cas, il n'y a plus de méthode pour changer les dimensions, donc plus de postcondition à casser :

> **Que veut dire « immuable » ?** Un objet immuable est un objet qu'on ne peut plus changer une fois créé, comme un ticket de caisse déjà imprimé. Si rien ne peut être modifié, aucun comportement modificateur ne peut trahir le contrat.

```java
interface Forme { double aire(); }

record Rectangle(double largeur, double hauteur) implements Forme {
    public double aire() { return largeur * hauteur; }
}

record Carre(double cote) implements Forme {
    public double aire() { return cote * cote; }
}
```

L'immuabilité (records Java, classes `readonly` en PHP 8.2+) supprime de fait les violations historiques du LSP. C'est l'une des raisons pour lesquelles la programmation par objets-valeurs immuables est devenue dominante.

### Au-delà du carré/rectangle : exemples plus parlants

Le couple *Carré/Rectangle* est devenu si récurrent qu'il occulte d'autres violations plus instructives, qu'on rencontre tous les jours sans les voir.

#### L'autruche est-elle un oiseau ?

```php
abstract class Oiseau {
    abstract public function voler(): void;
}

final class Hirondelle extends Oiseau {
    public function voler(): void { /* ok */ }
}

final class Autruche extends Oiseau {
    public function voler(): void {
        throw new LogicException('Une autruche ne vole pas.');
    }
}
```

Une fonction `migrer(Oiseau $o) { $o->voler(); ... }` casse dès qu'on lui passe une `Autruche`. Le LSP est violé : la postcondition implicite *« après `voler()`, l'oiseau est en l'air »* ne tient plus. La correction n'est pas de retravailler la hiérarchie mais de **ségréguer par capacité**, c'est-à-dire de séparer les rôles selon ce que chaque oiseau sait vraiment faire :

> **Que veut dire « ségréguer » ?** Ségréguer, c'est séparer en groupes distincts. Ici, on range d'un côté les oiseaux qui volent et de l'autre ceux qui courent, au lieu de tout mélanger. C'est exactement l'idée du principe ISP, vu plus loin.

```php
interface OiseauVolant { public function voler(): void; }

final class Hirondelle implements OiseauVolant { public function voler(): void { /* ok */ } }
final class Autruche {
    public function courir(): void { /* ok */ }
}
```

L'autruche reste un oiseau au sens du domaine ; elle n'est plus *substituable* à un `OiseauVolant`. C'est le mariage typique LSP + ISP : on ne déclare la capacité que là où elle existe vraiment.

#### `ImmutableSet` étend-il `Set` ?

Plus subtil et omniprésent : on hérite d'une interface mutable pour fournir une version immuable.

```php
interface Ensemble {
    public function ajouter(mixed $element): void;
    public function contient(mixed $element): bool;
}

final class EnsembleImmuable implements Ensemble {
    public function __construct(private readonly array $elements) {}
    public function ajouter(mixed $element): void {
        throw new BadMethodCallException('Immuable');
    }
    public function contient(mixed $element): bool { return in_array($element, $this->elements, true); }
}
```

Tout client qui reçoit un `Ensemble` croit pouvoir lui ajouter un élément ; la version immuable lance une exception. Violation directe du LSP, et symptôme typique des bibliothèques de collections de la première génération (Java avant les `List.of(...)`). La solution propre : ne pas faire hériter `Immuable` de la version mutable, mais définir `EnsembleLecture` comme racine et `EnsembleMutable` comme sur-ensemble :

```php
interface EnsembleLecture {
    public function contient(mixed $element): bool;
    public function taille(): int;
}

interface EnsembleMutable extends EnsembleLecture {
    public function ajouter(mixed $element): void;
}
```

L'immuable implémente `EnsembleLecture` et rien d'autre ; il est *vraiment* substituable partout où la lecture seule suffit. C'est la trace la plus pure du LSP : la hiérarchie **suit les capacités**, pas la familiarité du nom.

#### Le compte bancaire et la *history rule*

```php
class CompteCourant {
    public function __construct(protected float $solde) {}
    public function deposer(float $m): void { $this->solde += $m; }
    public function retirer(float $m): void {
        if ($m > $this->solde) throw new DomainException('Solde insuffisant');
        $this->solde -= $m;
    }
}

class CompteEpargneBloque extends CompteCourant {
    public function retirer(float $m): void {
        throw new DomainException('Retraits bloqués jusqu\'à échéance.');
    }
}
```

Aucune signature ne change ; aucune précondition n'est *renforcée* explicitement. Pourtant, la **règle d'historique** est violée : le supertype autorisait la séquence d'états *(dépôt, retrait, dépôt, retrait)* ; le sous-type ne l'autorise plus. Tout code qui orchestrait des comptes en s'appuyant sur cette séquence est cassé. Le LSP n'est pas dans la signature, il est dans **les transitions d'état que les clients tenaient pour acquises**.

### Détecter une violation en revue

Indices fréquents :
- Un sous-type lance `UnsupportedOperationException` ou `null` là où le parent retournait une valeur.
- Un sous-type ajoute une vérification *« if (param == ...) throw »* en début de méthode redéfinie.
- Le code appelant fait `instanceof` pour distinguer les sous-types.
- Les tests d'intégration passent pour le parent mais échouent pour les enfants avec le *même scénario*.

### Quand assouplir

Le LSP ne s'applique pas si vous n'utilisez jamais le sous-type *via* le type parent (héritage purement structurant, sans polymorphisme à l'usage). C'est une situation rare et souvent un signal qu'une autre relation (composition, interface) serait plus juste. En pratique, dès qu'une variable typée du parent peut recevoir un enfant, le LSP s'applique sans exception.

[🔝 Retour en haut de page](#table-des-matières)

## Interface Segregation Principle (ISP)

> *« Aucun client ne devrait être forcé de dépendre de méthodes qu'il n'utilise pas. »* (R. C. Martin)

> **Que veut dire « client » ici ?** Le client est le code qui se sert d'une interface (un synonyme d'« appelant »). Comme un usager qui n'utilise qu'un guichet d'une administration : pourquoi devrait-il connaître les vingt autres guichets qui ne le concernent pas ?

### Lire la définition

L'ISP regarde l'interface du *côté du client*. La question n'est pas *« cette interface est-elle grosse ? »* mais *« chaque client utilise-t-il la totalité de ce qu'elle déclare ? »*. Une interface de 30 méthodes est acceptable si tous ses clients en consomment 30 ; une interface de 4 méthodes est surchargée si un client n'en utilise qu'une.

Mieux vaut donc plusieurs interfaces *cohérentes par rôle* qu'une grosse interface fourre-tout (*fat interface*, « interface obèse »). Une classe peut implémenter autant d'interfaces que nécessaire (PHP, Java et C# autorisent l'héritage multiple d'interfaces) : multiplier les interfaces raisonnablement ne coûte rien à la structure du code.

> **Analogie.** Une télécommande universelle bourrée de 60 boutons (interface obèse) où vous n'en touchez que 3 vous oblige quand même à composer avec les 57 autres. Trois petites télécommandes claires, une par appareil, sont plus simples : chacun ne prend que celle dont il a besoin.

### Pourquoi

Une classe qui implémente une interface obèse hérite de méthodes qu'elle n'a aucun moyen d'honorer. Les options sont toutes mauvaises :
- `UnsupportedOperationException` : violation directe du LSP.
- `null` ou valeur sentinelle : décale le bug à l'appelant.
- Implémentation vide silencieuse : faux positif au runtime.

Côté appelants, dépendre d'une interface trop large augmente le couplage à des évolutions qui ne les concernent pas : ajouter une méthode à l'interface oblige à recompiler/redéployer même les modules qui ne s'en servent pas.

### À éviter : interface obèse

```java
interface AppareilBureau {
    void imprimer(Document d);
    void scanner(Document d);
    void faxer(Document d);
}

class ImprimanteSimple implements AppareilBureau {
    public void imprimer(Document d) { /* ok */ }
    public void scanner(Document d)  { throw new UnsupportedOperationException(); }
    public void faxer(Document d)    { throw new UnsupportedOperationException(); }
}
```

`ImprimanteSimple` ment sur ses capacités ; tout code qui reçoit un `AppareilBureau` doit gérer l'éventualité d'une exception. C'est aussi une violation du LSP par contagion : `ImprimanteSimple` n'est pas un sous-type comportemental d'`AppareilBureau`.

### À préférer : interfaces ségrégées par *rôle*

```java
interface Imprimante { void imprimer(Document d); }
interface Scanner    { void scanner(Document d); }
interface Fax        { void faxer(Document d); }

class ImprimanteSimple implements Imprimante {
    public void imprimer(Document d) { /* ok */ }
}

class Multifonction implements Imprimante, Scanner, Fax {
    public void imprimer(Document d) { /* ok */ }
    public void scanner(Document d)  { /* ok */ }
    public void faxer(Document d)    { /* ok */ }
}
```

Chaque appelant ne dépend que de la capacité dont il a besoin : un service d'archivage prend `Scanner` en paramètre, un module d'impression prend `Imprimante`. Le multifonction sert tout le monde sans rien forcer.

### Variante PHP

```php
interface Imprimante { public function imprimer(Document $d): void; }
interface Scanner    { public function scanner(Document $d): Image; }
interface Fax        { public function faxer(Document $d, string $numero): void; }

final class ImprimanteSimple implements Imprimante {
    public function imprimer(Document $d): void { /* ... */ }
}

final class Multifonction implements Imprimante, Scanner, Fax {
    public function imprimer(Document $d): void { /* ... */ }
    public function scanner(Document $d): Image { /* ... */ }
    public function faxer(Document $d, string $numero): void { /* ... */ }
}
```

### Le piège inverse : l'explosion d'interfaces

Appliqué mécaniquement, l'ISP conduit à une interface par méthode (*« role interface taken to absurdity »*). Symptômes :
- Les implémentations cumulent dix interfaces alors qu'elles forment manifestement un même rôle métier.
- Les noms d'interfaces deviennent verbaux (`OrderSaver`, `OrderReader`, `OrderDeleter`...) là où `OrderRepository` couvrait le besoin.
- Les conteneurs DI explosent en bindings redondants.

La règle pratique : segmenter quand un *client réel* sous-utilise l'interface, ou quand deux groupes de méthodes évoluent à des rythmes différents. Ne pas segmenter par esthétique.

### Le bon mètre : les *role interfaces* (Martin Fowler)

> **Qui est Martin Fowler ?** Un auteur britannique très influent sur la qualité du code, notamment sur le *refactoring* (l'art de réorganiser du code sans changer son comportement). Plusieurs notions de ce document viennent de lui.

Fowler distingue deux familles d'interfaces :
- **Header interfaces** (« interfaces en-tête ») : on prend une classe existante et on en *recopie* toutes les méthodes publiques pour en faire une interface. Le nom évoque le fichier *header* du langage C, un simple double structurel. Ces interfaces ont autant de méthodes que la classe et glissent vite vers l'interface obèse.
- **Role interfaces** (« interfaces de rôle ») : on déclare l'interface du *point de vue d'un appelant précis*, avec seulement les méthodes que ce rôle utilise. Une classe peut implémenter plusieurs interfaces de rôle, chacune correspondant à un contrat avec un type d'appelant.

Le bon mètre n'est donc pas *« combien de méthodes ? »* mais *« combien de rôles distincts cette interface mélange-t-elle ? »*. Une interface de douze méthodes peut être saine si elle correspond à un seul rôle ; une de trois méthodes est obèse si elle en mélange deux. C'est cette grille qui rend l'ISP utilisable sans tomber dans l'explosion d'interfaces : on dérive le découpage de la *forme du dialogue avec les clients*, pas d'une cardinalité.

### Quand assouplir

Sur de petits domaines stables, segmenter à l'extrême crée plus d'interfaces qu'il n'y a de classes ; on garde alors une seule interface tant que toutes les implémentations honorent réellement toutes les méthodes. Tant qu'aucun client n'est forcé de dépendre d'une méthode qu'il n'utilise pas, l'ISP est respecté, même avec une interface unique.

```mermaid
classDiagram
    class Imprimante {
        <<interface>>
        +imprimer(d: Document)
    }
    class Scanner {
        <<interface>>
        +scanner(d: Document)
    }
    class Fax {
        <<interface>>
        +faxer(d: Document)
    }
    class ImprimanteSimple
    class Multifonction
    Imprimante <|.. ImprimanteSimple
    Imprimante <|.. Multifonction
    Scanner    <|.. Multifonction
    Fax        <|.. Multifonction
```

[🔝 Retour en haut de page](#table-des-matières)

## Dependency Inversion Principle (DIP)

> *« Les modules de haut niveau ne doivent pas dépendre des modules de bas niveau ; les deux doivent dépendre d'abstractions. »*
>
> *« Les abstractions ne doivent pas dépendre des détails ; les détails doivent dépendre des abstractions. »*

### Lire les deux règles

> **Que veut dire « haut niveau » et « bas niveau » ?** Le haut niveau, c'est le code des règles métier (que veut l'entreprise : passer une commande, calculer un prix). Le bas niveau, c'est la plomberie technique (parler à la base de données, envoyer un e-mail). Le haut niveau décide, le bas niveau exécute les détails.

Le DIP énonce deux règles, pas une. La première inverse la flèche entre les couches. Sans DIP, le code métier *importe* les classes techniques (la connexion à la base via PDO, le client HTTP, la bibliothèque d'envoi d'e-mails). Avec le DIP, le code métier définit lui-même l'abstraction dont il a besoin (une *interface de port*) et la technique vient se brancher dessus en l'implémentant. Les deux dépendent désormais de l'abstraction, mais c'est l'**infrastructure** qui dépend du métier, et non l'inverse.

> **Que veut dire « port » et « adaptateur » ?** Le port est la prise standardisée définie par le métier (par exemple : *« je veux pouvoir enregistrer une commande »*). L'adaptateur est la fiche concrète qui se branche sur ce port (par exemple : *« voici comment je l'enregistre vraiment dans MySQL »*). Comme une prise électrique murale (le port) et l'adaptateur de voyage que vous y enfichez (l'adaptateur) : changez de pays, vous changez d'adaptateur, jamais la prise du mur.

> **Que veut dire « infrastructure » et « domaine » ?** Le *domaine* est le cœur métier (les règles propres à votre activité). L'*infrastructure* regroupe les outils techniques autour (base de données, réseau, fichiers). On veut que l'infrastructure serve le domaine, pas que le domaine soit prisonnier d'un outil technique.

La seconde règle interdit qu'une abstraction soit polluée par des détails : une interface `CommandeRepository` ne doit pas faire apparaître un `PDOStatement` (un objet propre à la base de données) dans sa signature, sinon on a juste déplacé le couplage au lieu de le supprimer. Une bonne abstraction n'exige pas plus que ce que tous ceux qui l'implémentent peuvent tenir ; elle parle le langage du métier (commandes, clients, articles), pas celui de l'outillage (lignes, requêtes, connexions).

### DIP n'est pas DI

Confusion la plus fréquente :
- **DIP** est un *principe d'architecture* : où placer les flèches de dépendance, qui définit les abstractions, comment isoler le métier.
- **DI** (injection de dépendances) est une *technique* : passer une dépendance par le constructeur plutôt que de l'instancier soi-même.

On peut faire de la DI sans respecter le DIP (par exemple en injectant une classe technique concrète dans un service métier : la dépendance va dans le mauvais sens, même si elle est injectée). On peut respecter le DIP sans cadriciel de DI (en branchant les implémentations à la main dans la racine de composition). Les deux sont complémentaires mais répondent à des questions différentes.

> **Que veut dire « framework » (cadriciel) ?** Un *framework* (« cadriciel » en français) est une boîte à outils complète qui impose une organisation de départ et appelle votre code aux bons moments, comme un chef de chantier qui vous dit quand poser chaque brique. Symfony en PHP ou Ruby on Rails sont des frameworks.

> **Que veut dire « racine de composition » ?** C'est l'unique endroit du programme où l'on assemble les vraies pièces concrètes entre elles (au démarrage). Comme la table de montage d'une usine : tout le reste du code ne voit que des contrats, seule cette table connaît les vrais modèles.

### Pourquoi

Sans DIP, changer de base de données, de système de messagerie ou de fournisseur de SMS oblige à modifier le code métier. Avec le DIP, le métier reste stable ; on ne change qu'un *adaptateur* technique. Le test devient possible sans vraie base de données ni vrai serveur d'e-mail : on injecte des *doublures* qui implémentent les mêmes interfaces. C'est la base de l'architecture *hexagonale*, aussi appelée *ports & adapters* (« ports et adaptateurs », Alistair Cockburn), et de la *clean architecture* (« architecture propre », Martin).

> **Que veut dire « doublure » ?** Une doublure est un faux objet qui imite un vrai le temps d'un test, comme une doublure au cinéma remplace l'acteur pour une cascade. On l'utilise pour tester son code sans dépendre d'une vraie base de données.

> **Que veut dire « architecture hexagonale » ?** C'est une manière d'organiser un logiciel en mettant le métier au centre, entouré de « ports » par lesquels le monde extérieur communique avec lui. Le dessin se fait souvent sous forme d'hexagone, d'où le nom. L'idée : le cœur ne dépend de rien d'extérieur, c'est l'extérieur qui se branche au cœur.

```mermaid
flowchart LR
    subgraph Sans_DIP["Sans DIP : le metier depend de la technique"]
        M1["Service metier"] --> T1["Base de donnees concrete"]
    end
    subgraph Avec_DIP["Avec DIP : la technique depend du metier"]
        M2["Service metier"] --> P["Interface CommandeRepository (le port, definie par le metier)"]
        A["Adaptateur PDO (la technique)"] -.implemente.-> P
    end
```

### À éviter

```php
final class ServiceCommande {
    public function passer(Commande $c): void {
        // dépendance directe à une implémentation concrète
        $pdo = new PDO('mysql:host=...;dbname=...');
        $pdo->prepare('INSERT INTO commandes ...')->execute([...]);

        $smtp = new \PHPMailer\PHPMailer\PHPMailer();
        $smtp->send();
    }
}
```

`ServiceCommande` est inutilisable sans MySQL ni PHPMailer, donc intestable sans eux. La flèche de dépendance va du domaine vers l'infrastructure : c'est l'inverse de ce que l'on veut.

### À préférer : abstraction définie par le domaine, injection par constructeur

```php
// Abstractions définies par le domaine
interface CommandeRepository {
    public function enregistrer(Commande $c): void;
}

interface NotificateurClient {
    public function confirmer(Commande $c): void;
}

// Module de haut niveau : ne connaît que les abstractions
final class ServiceCommande {
    public function __construct(
        private CommandeRepository $repository,
        private NotificateurClient $notificateur,
    ) {}

    public function passer(Commande $c): void {
        $this->repository->enregistrer($c);
        $this->notificateur->confirmer($c);
    }
}

// Détails d'infrastructure : implémentent les abstractions
final class CommandeRepositoryPdo implements CommandeRepository {
    public function __construct(private PDO $pdo) {}
    public function enregistrer(Commande $c): void { /* ... */ }
}

final class NotificateurClientSmtp implements NotificateurClient {
    public function __construct(private Mailer $mailer) {}
    public function confirmer(Commande $c): void { /* ... */ }
}
```

En tests, on injecte des doublures en mémoire ; en production, les implémentations concrètes. Le sens du couplage est inversé : c'est `CommandeRepositoryPdo` qui dépend de `CommandeRepository`, pas l'inverse.

### Câblage à la racine de composition

```php
// composition root (point d'entrée : controleur, container, bin/console...)
$pdo           = new PDO($dsn, $user, $pass);
$mailer        = new Mailer($smtpConfig);

$service = new ServiceCommande(
    new CommandeRepositoryPdo($pdo),
    new NotificateurClientSmtp($mailer),
);

$service->passer($commande);
```

La règle : **un seul endroit du code connaît les classes concrètes**, la racine de composition. Tout le reste manipule des interfaces.

### Quand assouplir

Pour du code utilitaire sans variation prévisible (parser de fichier, formatage de date, calcul mathématique pur), introduire une interface ne fait qu'ajouter une couche. Le DIP brille là où l'implémentation peut raisonnablement changer ou être remplacée par une doublure de test : persistance, communication réseau, horloge, génération aléatoire, accès au système de fichiers. Pour le reste, une dépendance directe à du code stable (la *standard library*, un calcul pur) ne fait de mal à personne.

[🔝 Retour en haut de page](#table-des-matières)

## Couplage et cohésion : la métrique sous SOLID

SOLID est un *moyen*, pas une *fin*. Le but que les cinq principes cherchent à atteindre tient en deux mots venus de Larry Constantine et Edward Yourdon dans les années 1970 : **couplage faible**, **cohésion forte**. Comprendre cette boussole, c'est cesser de réciter les principes pour commencer à les juger.

### Couplage : neuf nuances de dépendance

Le couplage n'est pas tout ou rien ; il existe une échelle classique, du plus toxique au plus sain :

1. **Couplage de contenu** : un module modifie directement les variables internes d'un autre (l'équivalent de fouiller dans les tiroirs du voisin). Toxique.
2. **Couplage commun** : deux modules partagent des données globales modifiables. Toxique, car les changements deviennent imprévisibles.
3. **Couplage de contrôle** : A passe à B un *drapeau* (un indicateur) qui change le chemin d'exécution interne de B. Médiocre : B doit deviner les intentions de A.
4. **Couplage de timbre** *(stamp)* : A passe une grosse structure à B alors que B n'en utilise qu'un champ. Symptôme d'un ISP non appliqué.
5. **Couplage de données** : A passe à B exactement les paramètres dont B a besoin. **Acceptable, voire idéal.**
6. **Couplage de message** : A et B ne dialoguent que par envoi de messages bien définis (objets-messages, événements). **Idéal.**

SOLID pousse globalement le code vers les niveaux (5) et (6). Le DIP vise à éliminer les niveaux (1) et (2) entre couches ; l'ISP cible le (4) ; le SRP réduit les couplages internes cachés (deux raisons de changer dans la même classe).

### Cohésion : sept nuances aussi

Symétriquement, sept niveaux de cohésion, du plus mauvais au meilleur :

1. **Cohésion fortuite** : les méthodes se retrouvent dans la même classe par hasard de l'histoire.
2. **Cohésion logique** : *« toutes les opérations de validation »*, mais portant sur des objets différents.
3. **Cohésion temporelle** : *« tout ce qui se fait au démarrage »*.
4. **Cohésion procédurale** : étapes successives d'un même enchaînement.
5. **Cohésion de communication** : les méthodes opèrent sur les mêmes données.
6. **Cohésion séquentielle** : la sortie d'une méthode sert d'entrée à la suivante.
7. **Cohésion fonctionnelle** : toutes les méthodes concourent à *une seule tâche bien définie*. **Idéal.**

Le SRP, lu correctement, vise la cohésion fonctionnelle alignée sur un *acteur* unique. Une classe qui n'a qu'une raison de changer mais quatre tâches indépendantes (cohésion logique) viole encore l'esprit du principe.

### La loi de Constantine

> *« Bon code = couplage faible entre modules, cohésion forte à l'intérieur. »*

C'est cette maxime que SOLID met en pratique. Si l'*application* d'un principe SOLID **augmente** le couplage ou **réduit** la cohésion, c'est qu'on l'applique mal, peu importe la rigueur apparente de la lecture. Le principe est au service de la métrique, jamais l'inverse.

[🔝 Retour en haut de page](#table-des-matières)

## Composition plutôt qu'héritage

L'OOP des années 1990 (Java 1.0, premiers manuels Eiffel et Smalltalk) survalorisait l'héritage : *est-un* partout, hiérarchies à six niveaux, *frameworks* qui obligeaient à `extends` une classe de base pour tout. Trente ans plus tard, le verdict est tombé : **l'héritage profond est presque toujours une erreur**. Les bibliothèques modernes (Go, Rust, Kotlin, Swift, PHP moderne) misent sur les interfaces, traits, composition et délégation.

### Pourquoi l'héritage déçoit

- **Couplage maximal** : la classe fille dépend du fonctionnement interne du parent, pas seulement de son contrat. Un changement dans le parent peut casser silencieusement les enfants (problème dit *fragile base class*, « classe de base fragile » : toucher au socle fissure tout ce qui s'appuie dessus).
- **Combinatoire impossible** : si on a `Oiseau`/`Mammifère` et `Volant`/`Nageur`/`Coureur`, l'héritage simple impose de choisir une seule dimension. Avec quatre dimensions à deux valeurs, on a 16 sous-classes potentielles.
- **Substitution risquée** : voir LSP. La majorité des violations LSP viennent d'un héritage forcé alors qu'une autre relation conviendrait.
- **Évolution unilatérale** : en composition, on peut changer un composant à l'exécution ; en héritage, jamais.

### Le test de l'« est-un » contre le test de l'« joue le rôle de »

Avant d'écrire `class B extends A`, posez-vous : *« B est-il intrinsèquement un A, ou B joue-t-il temporairement le rôle de A pour un certain client ? »*. Le second cas appelle une interface, pas un héritage. Une `EntréeJournal` peut *jouer le rôle* de `MessageHorodaté` sans en *être un*.

### Strategy par composition : l'exemple canonique

```php
// Anti-pattern : héritage pour varier l'algorithme
abstract class Tri {
    abstract public function trier(array $data): array;
}
final class TriRapide extends Tri { /* ... */ }
final class TriFusion extends Tri { /* ... */ }

class GestionnaireDocuments extends TriRapide { /* couplage figé à un tri précis */ }
```

```php
// Composition : on injecte la stratégie
interface Tri {
    public function trier(array $data): array;
}

final class TriRapide implements Tri { /* ... */ }
final class TriFusion implements Tri { /* ... */ }

final class GestionnaireDocuments {
    public function __construct(private Tri $tri) {}
    public function indexer(array $documents): array {
        return $this->tri->trier($documents);
    }
}
```

On peut maintenant changer la stratégie de tri pendant que le programme tourne, la tester avec une stratégie factice, et `GestionnaireDocuments` ne dépend plus que d'un *contrat*, pas d'un parent.

### Délégation explicite : le *vrai* « bonjour le monde » de l'objet moderne

> **Que veut dire « déléguer » ?** Déléguer, c'est confier une tâche à quelqu'un d'autre dont c'est le métier, au lieu de la faire soi-même. Un objet qui *a* une horloge lui demande l'heure plutôt que d'essayer de la calculer lui-même.

```php
final class JournalEvenements {
    public function __construct(private readonly Horloge $horloge) {}
    public function consigner(string $message): void {
        // on délègue au composant Horloge, on n'en hérite pas
        echo '[' . $this->horloge->maintenant()->format('c') . "] {$message}\n";
    }
}
```

`JournalEvenements` *a une* `Horloge` ; il n'en *est pas une*. On peut injecter une `HorlogeFigée` en test sans toucher au journal. Pas une seule ligne d'héritage.

```mermaid
flowchart TB
    subgraph Heritage["Heritage (est-un) : lien rigide"]
        J1["JournalEvenements"] -->|"extends (herite de)"| H1["Horloge"]
    end
    subgraph Composition["Composition (a-un) : piece interchangeable"]
        J2["JournalEvenements"] -->|"possede une"| H2["Horloge (interface)"]
        HV["HorlogeReelle"] -.implemente.-> H2
        HF["HorlogeFigee (pour les tests)"] -.implemente.-> H2
    end
```

### Quand l'héritage reste légitime

- **Template Method** très restreint : le squelette est *vraiment* invariant et seules une ou deux étapes varient. Et encore, la plupart de ces cas se ramènent à une fonction d'ordre supérieur.
- **Sous-typage strict d'un type valeur immuable** où le LSP est garanti par construction (records, enums avec méthodes).
- **Contraintes de framework** : certains frameworks imposent `extends` (rare, mais ça arrive).

Adage à retenir : *« Préférer la composition à l'héritage »* (Gang of Four, 1994). C'est le plus court résumé de SOLID lu honnêtement.

[🔝 Retour en haut de page](#table-des-matières)

## Tell-Don't-Ask : la conséquence pratique

> *« Tell, don't ask. »* (Alec Sharp, popularisé par Martin Fowler.)

> **Que veut dire « Tell-Don't-Ask » ?** Mot à mot : *« ordonne, ne demande pas »*. Plutôt que de demander à un objet ses données pour décider à sa place, on lui *dit* quoi faire et on le laisse se débrouiller. Comme au restaurant : vous commandez un plat (vous *dites*), vous n'allez pas en cuisine sortir les ingrédients pour cuisiner vous-même (vous ne *demandez* pas les détails).

### Le principe

Au lieu de demander à un objet son état pour décider à sa place, on lui **dit ce qu'il faut faire** et on le laisse décider en interne. *Demander* (ask) casse l'encapsulation : on sort les données, on raisonne ailleurs, puis on remet les données en place. *Dire* (tell) respecte l'encapsulation : la décision reste collée aux données qu'elle concerne.

> **Que veut dire « encapsulation » ?** L'encapsulation, c'est garder les données d'un objet à l'intérieur de cet objet, derrière des actions bien définies, au lieu de les exposer en vrac. Comme un distributeur de billets : vous appuyez sur des boutons (les actions), vous ne plongez pas la main dans le coffre (les données).

### Pourquoi c'est la conséquence directe de SOLID

- **SRP** : si la décision reste à l'extérieur, deux modules se partagent la responsabilité (les données d'un côté, la règle de l'autre). Tell-Don't-Ask la regroupe.
- **OCP** : ajouter une nouvelle règle ne modifie pas l'appelant ; cela enrichit l'objet ou son polymorphisme.
- **LSP** : on parle au contrat, pas aux rouages internes ; un sous-type peut donc redéfinir son comportement sans casser l'appelant.

### *Ask* (demander) : l'anti-patron

> **Que veut dire « anti-patron » ?** Un anti-patron (en anglais *anti-pattern*) est une solution courante mais mauvaise, un piège dans lequel on tombe souvent. L'inverse d'un bon patron de conception.

```php
final class Panier {
    /** @var Article[] */ public array $articles = [];
    public bool $promo = false;
}

// Logique métier dispersée dans l'appelant
function totalPanier(Panier $p): float {
    $total = 0.0;
    foreach ($p->articles as $a) {
        $total += $a->prix;
    }
    if ($p->promo) {
        $total *= 0.9;
    }
    return $total;
}
```

L'appelant interroge le panier (`$p->articles`, `$p->promo`), prend la décision (multiplier par 0.9), recompose. Si la promo passe à 8 %, ou s'ajoute une seconde règle (panier > 100 € = livraison gratuite), c'est l'appelant qui change. Les invariants ne sont protégés par personne.

### *Tell* (dire) : l'expression objet

```php
final class Panier {
    /** @param Article[] $articles */
    public function __construct(
        private array $articles = [],
        private bool $promo = false,
    ) {}

    public function ajouter(Article $a): void { $this->articles[] = $a; }
    public function appliquerPromo(): void { $this->promo = true; }

    public function total(): float {
        $total = array_sum(array_map(fn(Article $a) => $a->prix, $this->articles));
        return $this->promo ? $total * 0.9 : $total;
    }
}

// Côté appelant : sans logique
$p->ajouter($article);
$p->appliquerPromo();
$facture = $p->total();
```

L'appelant *dit* (`appliquerPromo`, `ajouter`) puis *demande le résultat fini* (`total`). Toute évolution des règles reste dans `Panier`. Les `instanceof`, les `if` portant sur l'état, les *getters* dépouillés s'évanouissent.

### Le pendant : les *getters* sont parfois nécessaires

> **Que veut dire « getter » ?** Un *getter* (« accesseur » en français) est une petite méthode dont le seul rôle est de renvoyer une donnée de l'objet, par exemple `getNom()`. Son contraire, qui modifie une donnée, est un *setter* (« mutateur »).

Tell-Don't-Ask n'interdit pas les *getters* : un objet doit pouvoir exposer ce qui sert à l'affichage, à la sérialisation (transformer un objet en texte ou en fichier pour le transmettre ou le stocker) ou à la persistance (l'enregistrer durablement). La règle est : **ne pas fonder une décision métier sur le *getter* d'un autre objet**. On lit pour communiquer, on *dit* pour décider.

[🔝 Retour en haut de page](#table-des-matières)

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

## Pour aller plus loin

- *Agile Software Development: Principles, Patterns, and Practices*, Robert C. Martin (livre fondateur)
- *Clean Architecture*, Robert C. Martin
- *Adaptive Code: Agile coding with design patterns and SOLID principles*, Gary McLean Hall
- *Practical Object-Oriented Design in Ruby* (POODR), Sandi Metz (la référence sur le *« make the easy change easy »*)
- *Object-Oriented Software Construction*, Bertrand Meyer (origine du *Design by Contract* et de l'OCP)
- *Data Abstraction and Hierarchy*, Barbara Liskov, 1987 (papier d'origine du LSP)
- *A Behavioral Notion of Subtyping*, Liskov & Wing, 1994 (formalisation du LSP, règle d'historique)
- [SOLID Principles (Wikipedia)](https://en.wikipedia.org/wiki/SOLID)
- [The Principles of OOD (Uncle Bob)](http://butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod)
- [Refactoring Guru, Design Patterns](https://refactoring.guru/design-patterns)
- [Hexagonal Architecture, Alistair Cockburn](https://alistair.cockburn.us/hexagonal-architecture/)

## Licence

Distribué sous licence [MIT](LICENSE).

## Auteur

**Tansoftware - Tanguy Chénier** · [LinkedIn](https://www.linkedin.com/in/tanguy-chenier) · [Tan-Software](https://github.com/Tan-Software) · [Compte personnel (derniers outils)](https://github.com/tanguychenier) · [tansoftware.com](https://www.tansoftware.com)
