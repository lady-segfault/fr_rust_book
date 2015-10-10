% La pile et le tas

Étant un langage système, Rust opère à un bas niveau. Si vous venez
d'un langage haut niveau, il y a certains aspects de la programmation
système qui ne vous sont pas familiers. Le plus important est la façon
dont la mémoire fonctionne, avec une pile (*stack*) et un tas (*heap*). Si
la façon dont les langages similaires au C utilisent l'allocation sur la pile
vous est familière, ce chapitre vous rafraîchira la mémoire. Sinon,
vous apprendrez de manière plus générale à propos de ce concept, mais avec une
focalisation sur Rust. 

# Gestion de la mémoire

La pile et le tas concernent la gestion de la mémoire. Ce
sont des abstractions qui vous aident à déterminer quand allouer et
libérer de la mémoire.

Voici une comparaison haut niveau :

La pile est très rapide, c'est là où la mémoire est allouée par défaut
dans Rust, mais cette allocation est locale à un appel de fonction, et
est limitée en taille. Le tas, quant à lui, est plus lent, et est
alloué explicitement par votre programme. Mais il est pratiquement
illimité en taille, et est accessible globalement.

# La pile

Parlons de ce programme Rust :

```rust
fn main() {
    let x = 42;
}
```

Ce programme a une variable, `x`. Cette mémoire doit
être allouée depuis quelque part. Rust « alloue sur la pile » par
défaut, ce qui veut dire que les valeurs basiques « vont sur la
pile ». Qu'est-ce que ça veut dire ?

Hé bien, quand une fonction est appelée, de la mémoire est allouée
pour chacune de ses variables locales et pour d'autres
informations. On appelle cela une « structure de pile » (*stack frame*)
et, dans le cadre de ce tutoriel, nous allons ignorer les informations
additionnelles et simplement prendre en compte les variables locales
que nous allouons. Donc, dans ce cas, quand `main()` est exécutée, nous
allons allouer un seul entier de 32 bits pour notre structure de
pile. Ceci est automatiquement géré pour vous, comme vous pouvez le
voir ; nous n'avons pas eu à écrire de code Rust spécial ou à faire quoi
que ce soit. 

Quand la fonction se termine, sa structure de pile est
libérée. Cela se produit également de manière automatique.

C'est tout ce qu'il y a à voir pour ce programme simple. La chose importante
à comprendre ici est que l'allocation sur la pile est très, très
rapide. Comme nous connaissons toutes les variables locales à
l'avance, nous pouvons obtenir toute la mémoire d'un coup. Et comme
nous nous débarrassons également de toutes ces variables en même
temps, nous pouvons aussi le faire très rapidement.

L'inconvénient est qu'on ne peut pas conserver des valeurs si on en a
besoin pour plus longtemps que pour une simple fonction. Nous n'avons
pas non plus encore abordé ce que veut dire ce mot, « pile ». Pour
faire cela, nous allons avoir besoin d'un exemple légèrement plus
compliqué :

```rust
fn foo() {
    let y = 5;
    let z = 100;
}

fn main() {
    let x = 42;

    foo();
}
```

Ce programme a un total de trois variables : deux dans `foo()` et une
dans `main()`. Comme dans le cas précédent, quand `main()` est appelée
un unique entier est allouée pour sa structure de pile. Mais avant que
nous puissions vous montrer ce qui se produit quand `foo()` est
appelée, nous devons visualiser ce qui se passe avec la mémoire. Votre
système d'exploitation présente une mémoire qui est plutôt simple à
votre programme : une énorme liste d'adresses, de 0 à un 
grand nombre, représentant quelle quantité de RAM votre ordinateur
possède. Par exemple, si vous avez un gigaoctet de RAM, vos adresses
vont de `0` à `1,073,741,823`. Ce nombre est 2<sup>30</sup>, le nombre
d'octets dans un gigaoctet.

Cette mémoire est un peu comme comme un tableau géant : les adresses
commencent à zéro et montent jusqu'au nombre final. Voilà donc un
schéma de notre première structure de pile :


| Adresse | Nom  | Valeur |
|---------|------|--------|
| 0       | x    | 42     |

Nous avons `x` situé à l'adresse `0`, avec la valeur `42`.

Quand `foo()` est appelée, une nouvelle structure de pile est allouée :

| Adresse | Nom  | Valeur |
|---------|------|--------|
| 2       | z    | 100    |
| 1       | y    | 5      |
| 0       | x    | 42     |

Comme `0` a été utilisée par la première structure, `1` et `2` sont
utilisées pour la structure de pile de  `foo()`. Elle grandit vers le
haut à mesure qu'on appelle des fonctions.

Nous devons ici noter un certain nombre de choses importantes. Les
nombres 0, 1 et 2 sont tous à but illustratif, et ne correspondent pas
aux nombres que l'ordinateur utilisera réellement. En
particulier, les séries d'adresses vont en réalité être séparées par
un certain nombre d'octets qui séparent chaque adresse, et cette
séparation peut même être supérieure à la taille de la valeur qui est stockée.

Lorsque `foo()` a terminé, sa structure de pile est libérée :

| Adresse | Nom | Valeur |
|---------|-----|--------|
| 0       | x   | 42     |

Ensuite, après `main()`, même cette dernière valeur disparaît. Facile !

On appelle cela une « pile » parce que ça fonctionne comme une pile
d'assiettes : la première assiette que vous déposez est la
dernière que vous reprenez. Les piles sont aussi parfois appelées des
queues « dernier entré, premier sorti » (*last in, first out* ou *LIFO*) pour
cette raison, car la dernière valeur que vous placez sur la pile est
la première que vous reprenez.

Essayons un exemple avec trois niveaux de profondeur :

```rust
fn bar() {
    let i = 6;
}

fn foo() {
    let a = 5;
    let b = 100;
    let c = 1;

    bar();
}

fn main() {
    let x = 42;

    foo();
}
```

D'accord, d'abord nous appelons `main()` :

| Adresse | Nom  | Valeur |
|---------|------|--------|
| 0       | x    | 42     |

Ensuite `main()` appelle `foo()` :

| Adresse | Nom  | Valeur |
|---------|------|--------|
| 3       | c    | 1      |
| 2       | b    | 100    |
| 1       | a    | 5      |
| 0       | x    | 42     |

Et ensuite `foo()` appelle `bar()` :

| Adresse | Nom  | Valeur |
|---------|------|--------|
| 4       | i    | 6      |
| 3       | c    | 1      |
| 2       | b    | 100    |
| 1       | a    | 5      |
| 0       | x    | 42     |

Ouah ! Notre pile grandit.

Lorsque `bar()` a terminé, sa structure est libérée, ne laissant
plus que `foo()` et `main()` :

| Adresse | Nom  | Valeur |
|---------|------|--------|
| 3       | c    | 1      |
| 2       | b    | 100    |
| 1       | a    | 5      |
| 0       | x    | 42     |

Ensuite `foo()` se termine, ne laissant que `main()` :

| Adresse | Nom  | Valeur |
|---------|------|--------|
| 0       | x    | 42     |

Et ensuite nous avons terminé. Vous commencez à avoir le coup de
main ? C'est comme empiler de la vaisselle : vous ajoutez en haut, et
vous reprenez d'en haut.

# Le tas

Tout cela marche plutôt bien, mais tout ne peut pas marcher comme
cela. Parfois, vous devez passer de la mémoire entre différentes
fonctions, ou la garder en vie plus longtemps que la durée d'exécution
d'une seule fonction. Pour faire cela, nous pouvons utiliser le tas.

Dans Rust, vous pouvez allouer de la mémoire sur le tas avec le [type `Box<T>`][https://doc.rust-lang.org/stable/std/boxed/struct.Box.html].
Voici un exemple :

```rust
fn main() {
    let x = Box::new(5);
    let y = 42;
}
```

[box]: ../std/boxed/index.html

Voilà ce qui se passe en mémoire lorsque `main()` est appelée :

| Adresse | Nom  | Valeur |
|---------|------|--------|
| 1       | y    | 42     |
| 0       | x    | ?????? |

On alloue de l'espace pour deux variables sur la pile. `y` vaut `42`,
comme ça a toujours été le cas, mais qu'en est-il de `x` ? Hé bien, `x`
est une `Box<i32>`, et les *boxes* allouent de la mémoire sur le tas.
La valeur effective de la *box* est une structure qui a un pointeur
vers « le tas ». Quand on commence à exécuter la fonction, et que
`Box::new()` est appelée, cela alloue de la mémoire sur le tas, et
place `5` à cet endroit. La mémoire ressemble maintenant à ça :

| Adresse              | Nom  | Valeur                 |
|----------------------|------|------------------------|
| (2<sup>30</sup>) - 1 |      | 5                      |
| ...                  | ...  | ...                    |
| 1                    | y    | 42                     |
| 0                    | x    | → (2<sup>30</sup>) - 1 |

On a (2<sup>30</sup>) - 1 dans notre ordinateur hypothétique avec 1Go
de RAM. Et comme la pile grandit à patir de zéro, l'endroit le plus
facile où allouer de la mémoire est depuis l'autre bout. Notre
première valeur est donc à l'emplacement le plus haut en mémoire. Et
la valeur de la structure de `x` a un [pointeur brut][rawpointer]
(*raw pointer*) vers l'endroit que nous avons alloué sur le tas, donc
la valeur de `x` est (2<sup>30</sup>) - 1, l'emplacement mémoire que
nous avons demandé.

[rawpointer]: raw-pointers.html

Nous n'avons pas encore beaucoup abordé ce que veut dire allouer et
libérer de la mémoire dans ces contextes. Rentrer vraiment dans les
détails n'est pas le sujet de ce tutoriel, mais ce qu'il est important
de souligner ici est que le tas n'est pas juste une pile qui grandit
depuis l'autre bout. Nous verrons un exemple de cela plus tard dans ce
livre, mais comme le tas peut être alloué et libéré dans n'importe
quel ordre, il peut finir avec des « trous ». Voilà un schéma de la
disposition de la mémoire d'un programme qui tourne maintenant depuis
un certain temps :


| Adresse              | Nom  | Valeur                 |
|----------------------|------|------------------------|
| (2<sup>30</sup>) - 1 |      | 5                      |
| (2<sup>30</sup>) - 2 |      |                        |
| (2<sup>30</sup>) - 3 |      |                        |
| (2<sup>30</sup>) - 4 |      | 42                     |
| ...                  | ...  | ...                    |
| 3                    | y    | → (2<sup>30</sup>) - 4 |
| 2                    | y    | 42                     |
| 1                    | y    | 42                     |
| 0                    | x    | → (2<sup>30</sup>) - 1 |


Dans ce cas, nous avons alloué quatre choses sur le tas, mais en avons
libéré deux. Il y a un vide entre (2<sup>30</sup>) - 1 et
(2<sup>30</sup>) - 4 qui n'est pas utilisé actuellement. Les détails
précis de commment et pourquoi cela se produit dépendent du type de
stratégie que vous utilisez pour gérer le tas. Des programmes
différents peuvent utiliser différents « attributeurs de mémoire » (*memory
allocators*), qui sont des bibliothèques qui s'occupent de ça pour
vous. Les programmes Rust utilisent [jemalloc][jemalloc] à cette fin.

[jemalloc]: http://www.canonware.com/jemalloc/

Revenons à notre exemple. Comme cette mémoire est sur le tas, elle
peut rester en vie plus longtemps que la fonction qui alloue la
*box*. Ce n'est cependant pas le cas ici. [^moving] Quand la fonction
est terminée, on doit libérer la structure de pile pour
`main()`. `Box<T>` a cependant un tour dans son sac :
[Drop][drop]. L'implémentation de `Drop` pour `Box` libère la mémoire
qui a été allouée quand elle a été crée. Bien ! Donc quand `x`
disparaît, cela libère d'abord la mémoire allouée sur le tas :

| Adresse | Nom  | Valeur  |
|---------|------|---------|
| 1       | y    | 42      |
| 0       | x    | ??????  |

[drop]: drop.html
[^moving]: Il est possible de faire en sorte que la mémoire vive plus
           longtemps en transférant la propriété, ce qui est parfois appelé
           *moving out of the box* (déplacer hors de la boîte). Des
           exemples plus complexes seront présentés ultérieurement.


Après quoi la structure de pile disparaît, libérant toute notre mémoire.

# Arguments et emprunt

Nous avons quelques exemples avec la pile et le tas, mais qu'en est-il
des arguments de fonction et l'emprunt (*borrowing*) ? Voici un petit
programme Rust :

```rust
fn foo(i: &i32) {
    let z = 42;
}

fn main() {
    let x = 5;
    let y = &x;

    foo(y);
}
```

Quand on entre dans `main()`, la mémoire ressemble à ça :

| Adresse | Nom  | Valeur  |
|---------|------|---------|
| 1       | y    | → 0     |
| 0       | x    | 5       |

`x` est un bon vieux `5`, et `y` est une référence vers `x`. Sa valeur
est donc l'emplacement mémoire dans lequel vit `x`, ce qui dans ce cas
est `0`.

Que se passe-t-il quand on appelle `foo()`, en passant `y` comme
argument ?

| Adresse | Nom  | Valeur  |
|---------|------|---------|
| 3       | z    | 42      |
| 2       | i    | → 0     |
| 1       | y    | → 0     |
| 0       | x    | 5       |

Les structures de pile ne servent pas juste aux associations de
variables locales, mais aussi pour les arguments. Dans ce cas, nous
devons donc à la fois avoir `i`, notre argument, et `z`, notre
association de variable locale. `i` est une copie de cet argument,
`y`. Comme la valeur de `y` est `0`, celle de `i` est identique.

C'est une raison qui explique pourquoi emprunter une variable ne
libère pas de mémoire : la valeur d'une référence est juste un
pointeur vers un emplacement mémoire. Si on se débarrasse de cette
mémoire sous-jacente, les choses ne vont pas très bien marcher.

# Un exemple complexe

D'accord, examinons pas à pas ce programme complexe :

```rust
fn foo(x: &i32) {
    let y = 10;
    let z = &y;

    baz(z);
    bar(x, z);
}

fn bar(a: &i32, b: &i32) {
    let c = 5;
    let d = Box::new(5);
    let e = &d;

    baz(e);
}

fn baz(f: &i32) {
    let g = 100;
}

fn main() {
    let h = 3;
    let i = Box::new(20);
    let j = &h;

    foo(j);
}
```

D'abord, on appelle `main()` :

| Adresse              | Nom  | Valeur                 |
|----------------------|------|------------------------|
| (2<sup>30</sup>) - 1 |      | 20                     |
| ...                  | ...  | ...                    |
| 2                    | j    | → 0                    |
| 1                    | i    | → (2<sup>30</sup>) - 1 |
| 0                    | h    | 3                      |

On alloue de la mémoire pour `j`, `i`, et `h`. `i` est sur le tas, et a
donc une valeur qui pointe là-bas. 

Ensuite, à la fin de `main()`, `foo()` est appelée :

| Adresse              | Nom  | Valeur                 |
|----------------------|------|------------------------|
| (2<sup>30</sup>) - 1 |      | 20                     |
| ...                  | ...  | ...                    |
| 5                    | z    | → 4                    |
| 4                    | y    | 10                     |
| 3                    | x    | → 0                    |
| 2                    | j    | → 0                    |
| 1                    | i    | → (2<sup>30</sup>) - 1 |
| 0                    | h    | 3                      |

De l'espace est alloué pour `x`, `y`, et `z`. L'argument `x` a la même
valeur que `j`, car c'est ce qu'on a passé. C'est un pointeur vers
l'adresse `0` car `j` pointe vers `h`.

Ensuite, `foo()` appelle `baz()`, en passant `z` :

| Adresse              | Nom  | Valeur                 |
|----------------------|------|------------------------|
| (2<sup>30</sup>) - 1 |      | 20                     |
| ...                  | ...  | ...                    |
| 7                    | g    | 100                    |
| 6                    | f    | → 4                    |
| 5                    | z    | → 4                    |
| 4                    | y    | 10                     |
| 3                    | x    | → 0                    |
| 2                    | j    | → 0                    |
| 1                    | i    | → (2<sup>30</sup>) - 1 |
| 0                    | h    | 3                      |

On a alloué de la mémoire pour `f` et `g`. `baz()` est très courte,
donc lorsqu'elle a fini, on se débarrasse de sa structure de pile :

| Adresse              | Nom  | Valeur                 |
|----------------------|------|------------------------|
| (2<sup>30</sup>) - 1 |      | 20                     |
| ...                  | ...  | ...                    |
| 5                    | z    | → 4                    |
| 4                    | y    | 10                     |
| 3                    | x    | → 0                    |
| 2                    | j    | → 0                    |
| 1                    | i    | → (2<sup>30</sup>) - 1 |
| 0                    | h    | 3                      |

Ensuite, `foo()` appelle `bar()` avec `x` et `z` :

| Adresse              | Nom  | Valeur                 |
|----------------------|------|------------------------|
| (2<sup>30</sup>) - 1 |      | 20                     |
| (2<sup>30</sup>) - 2 |      | 5                      |
| ...                  | ...  | ...                    |
| 10                   | e    | → 9                    |
| 9                    | d    | → (2<sup>30</sup>) - 2 |
| 8                    | c    | 5                      |
| 7                    | b    | → 4                    |
| 6                    | a    | → 0                    |
| 5                    | z    | → 4                    |
| 4                    | y    | 10                     |
| 3                    | x    | → 0                    |
| 2                    | j    | → 0                    |
| 1                    | i    | → (2<sup>30</sup>) - 1 |
| 0                    | h    | 3                      |

On se retrouve à allouer une autre valeur sur le tas, et on doit donc
soustraire un à (2<sup>30</sup>) - 1. Il est plus facile d'écrire ça
que `1,073,741,822`. Dans tous les cas, on initialise les variables
comme d'habitude.

À la fin de `bar()`, on appelle `baz()` :

| Adresse              | Nom  | Valeur                 |
|----------------------|------|------------------------|
| (2<sup>30</sup>) - 1 |      | 20                     |
| (2<sup>30</sup>) - 2 |      | 5                      |
| ...                  | ...  | ...                    |
| 12                   | g    | 100                    |
| 11                   | f    | → 9                    |
| 10                   | e    | → 9                    |
| 9                    | d    | → (2<sup>30</sup>) - 2 |
| 8                    | c    | 5                      |
| 7                    | b    | → 4                    |
| 6                    | a    | → 0                    |
| 5                    | z    | → 4                    |
| 4                    | y    | 10                     |
| 3                    | x    | → 0                    |
| 2                    | j    | → 0                    |
| 1                    | i    | → (2<sup>30</sup>) - 1 |
| 0                    | h    | 3                      |

Là, on en est au point le plus profond ! Ouah ! Félicitations d'avoir
suivi aussi loin. 

Après que `baz()` ait fini, on se débarrasse de `f` et `g` :

| Adresse              | Nom  | Valeur                 |
|----------------------|------|------------------------|
| (2<sup>30</sup>) - 1 |      | 20                     |
| (2<sup>30</sup>) - 2 |      | 5                      |
| ...                  | ...  | ...                    |
| 10                   | e    | → 9                    |
| 9                    | d    | → (2<sup>30</sup>) - 2 |
| 8                    | c    | 5                      |
| 7                    | b    | → 4                    |
| 6                    | a    | → 0                    |
| 5                    | z    | → 4                    |
| 4                    | y    | 10                     |
| 3                    | x    | → 0                    |
| 2                    | j    | → 0                    |
| 1                    | i    | → (2<sup>30</sup>) - 1 |
| 0                    | h    | 3                      |

Ensuite, on revient de `bar()`. `d` est dans ce cas une `Box<T>`, donc
elle libère aussi ce vers quoi elle pointe : (2<sup>30</sup>) - 2.

| Adresse              | Nom  | Valeur                 |
|----------------------|------|------------------------|
| (2<sup>30</sup>) - 1 |      | 20                     |
| ...                  | ...  | ...                    |
| 5                    | z    | → 4                    |
| 4                    | y    | 10                     |
| 3                    | x    | → 0                    |
| 2                    | j    | → 0                    |
| 1                    | i    | → (2<sup>30</sup>) - 1 |
| 0                    | h    | 3                      |

Et après ça, `foo()` se termine :

| Adresse              | Nom  | Valeur                 |
|----------------------|------|------------------------|
| (2<sup>30</sup>) - 1 |      | 20                     |
| ...                  | ...  | ...                    |
| 2                    | j    | → 0                    |
| 1                    | i    | → (2<sup>30</sup>) - 1 |
| 0                    | h    | 3                      |

Et, enfin, `main()` se termine, en nettoyant le reste. Quand `i` est
`Drop`pé, cela libérera aussi ce qui reste sur le tas.

# Que font les autres langages ?

La plupart des langages avec un ramasse-miettes (*garbage collector*)
allouent sur le tas par défaut. Cela veut dire que chaque valeur est
« encadrée » (*boxed*). Il y a un certain nombre de raisons qui
expliquent cela, mais qui sortent du champ de ce tutoriel. Il existe
des optimisations possibles qui font que cela n'est pas vrai tout le
temps non plus. Plutôt que de se reposer sur la pile et `Drop` pour
nettoyer la mémoire, le ramasse-miettes s'occupe du tas à la place.

# Que faut-il utiliser ?

Alors, si la pile est plus rapide et plus facile à gérer, pourquoi
avons-nous besoin d'utiliser le tas ? Une raison importante est que
l'allocation uniquement sur la pile signifie que vous n'aurez qu'une
sémantique « dernier entré, premier sorti » (*LIFO*) pour libérer la
mémoire. L'allocation sur le tas est plus générale, permettant
d'allouer et de libérer de la mémoire dans un ordre arbitraire, mais
au prix d'une plus grande complexité.

En général, vous devriez préférer l'allocation sur la pile. C'est pour
cela que Rust alloue sur la pile par défaut. Le modèle « dernier
entré, premier sorti » est fondamentalement plus simple. Cela a deux
grands impacts : l'efficacité à l'exécution et l'impact sur la sémantique.

## Efficacité à l'exécution

Gérer la mémoire pour la pile est trivial : la machine se contente
d'incrémenter et de décrémenter une simple valeur, appelée « pointeur
de pile ». Gérer la mémoire pour le tas n'est pas si simple : la
mémoire allouée sur le tas est libérée à des endroits arbitraires, et
chaque bloc de mémoire alloué sur le tas peut être d'une taille
arbitraire. Le gestionnaire de mémoire doit donc travailler beaucoup
plus dur pour identifier la mémoire à réutiliser.

Si vous désirez vous plonger dans ce sujet avec plus de détails, [cet article][wilson]
est une très bonne introduction.

[wilson]: http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.143.4688

## Impact sur la sémantique

L'allocation sur la pile a un impact sur le langage Rust lui-même, et
ainsi sur le modèle mental du développeur. La sémantique « dernier
entré, premier sorti » est ce qui dirige la façon dont le langage Rust
gère la gestion automatique de mémoire. Même la libération d'une
*box* ayant un propriétaire unique allouée sur le tas peut être
dirigée par la sémantique « dernier entré, premier sorti » (*LIFO*) basée sur la
pile, comme cela a été discuté à l'intérieur de ce chapitre. La
flexibilité (c'est-à-dire l'expressivité) de sémantiques non-LIFO
signifie qu'en général le compilateur ne peut pas déduire
automatiquement au moment de la compilation où la mémoire devrait être
libérée ; il doit se reposer sur des protocoles dynamiques, qui
viennent potentiellement de l'extérieur du langage lui-même, pour diriger la
libération de mémoire. Le comptage de références (*reference counting* – *RC*)
tel qu'utilisé par `Rc<T>` et `Arc<T>` est un bon exemple.

Lorsqu'elle est poussée à l'extrême, l'augmentation de l'expressivité
de l'allocation sur le tas vient au prix soit d'un support à
l'exécution significatif (par exemple sous la forme d'un
ramasse-miettes) soit d'un effort important du programmeur (sous 
la forme d'appels explicites pour gérer la mémoire qui requièrent une
vérification qui n'est pas fournie par le compilateur Rust).
