[← Responsabilité unique (SRP)](02-responsabilite-unique-srp.md) · [↑ Sommaire](../README.md#table-des-matières) · [Substitution de Liskov (LSP) →](04-substitution-de-liskov-lsp.md)

# 3. Ouvert/fermé (OCP)

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

---

[← Responsabilité unique (SRP)](02-responsabilite-unique-srp.md) · [↑ Sommaire](../README.md#table-des-matières) · [Substitution de Liskov (LSP) →](04-substitution-de-liskov-lsp.md)
