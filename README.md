% Le langage de programmation Rust

Bienvenue ! Ce livre va vous apprendre le [langage de programmation Rust][rust].
Rust est un langage de programmation système axé sur les trois buts suivants :
sécurité, rapidité et concurrence. Il atteint ces trois buts sans utiliser de
ramasse-miette, le rendant utile dans un grand nombre de cas où d'autres
langages ne conviendraient pas : utilisation dans d'autres langages, logiciels
avec un espace défini et des performances restreintes ainsi que l'écriture de
code bas niveau (comme des drivers ou des systèmes d'exploitation). Il est plus
performant que les langages existants visant ces buts grâce à des vérifications
au moment de la compilation qui permettent d'éviter des coûts de performance
au moment de l'exécution tout en éliminant les risques de situation de
compétition. Rust a aussi pour but d'achever ‘zero-cost abstractions’ (ou
"abstration sans surcoût lors de l'exécution") même si certaines de ces
abstractions sont similaires à celles d'un langage de haut niveau. Malgré cela,
Rust permet quand même un contrôle précis tout comme un langage de bas niveau
le ferait.

[rust]: https://www.rust-lang.org

"Le langage de programmation Rust" est divisé en huit sections. Cette
introduction est la première. Après il y a :

* [Bien commencer][gs] - Configurer son ordinateur pour développer en Rust.
* [Apprendre Rust][lr] - Apprendre à programmer en Rust au travers de petits projets.
* [Rust en action][er] - Concepts de plus haut niveau pour écrire de l'excellent code Rust.
* [Syntaxe et Sémantiques][ss] - Chaque petit bout de rust, Each bit of Rust, décomposé en petits morceaux.
* [Nightly Rust][nr] - Dernières fonctionnalités pas encore disponibles dans la version stable.
* [Sommaire][gl] - Une référence des termes utilisés dans ce livre.
* [Bibliographie][bi] - Background des influences de Rust, articles parlant de Rust.

[gs]: commencer.html
[lr]: apprendre-rust.html
[er]: rust-en-action.html
[ss]: syntaxe-et-semantiques.html
[nr]: rust-nightly.html
[gl]: sommaire.html
[bi]: bibliographie.html

Après avoir lu cette introduction, vous voudrez sans doute commencer par "Apprendre Rust"
ou "Syntaxe et Sémantiques". Selon votre préférence : "Apprendre Rust" si vous souhaitez
plonger dans un projet, ou "Syntaxe et Sémantiques" si vous préférez commencer plus petit
et apprendre les concepts les uns après les autres. Des liens sont faits entre les deux
parties.

### Contribuer

Les fichiers sources depuis lesquels ce livre est généré peuvent être trouvé sur Github :
[https://github.com/GuillaumeGomez/fr_rust_doc/edit/master](https://github.com/GuillaumeGomez/fr_rust_doc/edit/master) et la version originale (en anglais) est disponible à cette adresse :
[github.com/rust-lang/rust/tree/master/src/doc/trpl](https://github.com/rust-lang/rust/tree/master/src/doc/trpl).

## Une brève introduction à Rust

Est-ce que Rust est un langage qui pourrait vous intéresser ? Examinons quelques bouts de
code pour démontrer quelques-unes de ces forces.

Le principal concept qui rend Rust unique est appelé "propriété" (ou "ownership"). Regardez
l'exemple suivant :

```rust
fn main() {
    let mut x = vec!["Hello", "world"];
}
```

Ce programme crée une [variable][var] appelée `x`. La valeur de cette assignation
est un `Vec<T>`, un "vecteur", que nous créons avec une [macro][macro] définie
dans la bibliothèque standard. Cette macro est appelée `vec`, et nous appelons
les macros avec un `!`. Cela suit un principe global de Rust : rendre les choses
explicites. Les macros peuvent faire des choses beaucoup plus compliquées que
de simple appels de fonctions, et sont visuellement distinctes. Le `!` aide aussi
pour le parsing, rendant les outils plus simple à écrire, ce qui est aussi
important.

Nous avons utiliser `mut` pour rendre `x` mutable : les variables sont
constantes par défaut en Rust. Nous modifierons ce vecteur plus tard dans cet
exemple.

Il est aussi important de noter que n'avons pas eu besoin de donner un type à
notre variable ici : tant que Rust est statiquement typé, nous n'avions pas
besoin de définir le type explicitement. Rust fait de l'inférence de type
pour contrebalancer les forces du typage statique avec la verbosité des
annotations de type.

Rust préfère les allocations sur la pile (stack memory) plutôt que sur le tas
(heap memory) : `x` est mis directement sur la pile. Cependant, le type
`Vec<T>` alloue de l'espace pour ses éléments sur le tas. Si vous n'êtes pas
familiers avec cette distinction, vous pouvez vous contenter de l'ignorer pour
le moment ou bien aller lire [‘The Stack and the Heap’][heap]. En tant que
langage de programmation système, Rust vous donne la possibilité de contrôler
la façon dont votre mémoire est allouée, mais quand vous commencez, cela pas
d'importance.

[var]: variables.html
[macro]: macros.html
[heap]: the-stack-and-the-heap.html

Plus tôt, nous avons mentionné le fait que la "propriété" est le nouveau concept
clé en Rust. Dans le langage de Rust, `x` est désigné comme étant le
"propriétaire" du vecteur. Cela signifie que quand `x` sort du scope (portée),
la mémoire du vecteur va être libérée. C'est fait par le compilateur de Rust de
manière déterministe plutôt qu'à travers un mécanisme tel qu'un ramasse-miette.
En d'autres mots, en Rust, vous n'appelez pas des fonctions telles que `malloc`
et `free` vous-même : le compilateur va déterminer statiquement quand est-ce que
vous aurez besoin d'allouer ou libérer de la mémoire, et ajoutera ces appels lui-
même. L'erreur est humaine, mais les compilateurs n'oublient jamais.

Ajoutons une autre ligne à notre exemple :

```rust
fn main() {
    let mut x = vec!["Hello", "world"];

    let y = &x[0];
}
```

Nous avons introduit une nouvelle variable : `y`. Dans le cas présent, `y` est
une référence sur le premier élément du vecteur. Les références en Rust sont
similaires aux pointeurs dans d'autres langages, mais avec une vérification de
sécurité supplémentaire au moment de la compilation. Les références intéragissent
avec le système de propriété en ["empruntant"][borrowing] (ou "borrowing") ce sur
quoi elles pointent plutôt que d'en devenir le propriétaire. La différence est
que, lorsque la référence est détruite, la mémoire de l'objet pointé ne l'est
pas. Si c'était le cas, nous aurions une double libération de mémoire, ce qui
serait assez problématique !

[borrowing]: references-et-emprunt.html

Ajoutons maintenant une troisième ligne. Ça semble assez innocent, mais cause
pourtant une erreur du compilateur :

```rust,ignore
fn main() {
    let mut x = vec!["Hello", "world"];

    let y = &x[0];

    x.push("foo");
}
```

`push` est une méthode de `Vec` qui permet d'ajouter un élément à la fin du
vecteur. Quand on essaie de compiler ce code, on obtient l'erreur suivante :

```text
error: cannot borrow `x` as mutable because it is also borrowed as immutable
    x.push("foo");
    ^
note: previous borrow of `x` occurs here; the immutable borrow prevents
subsequent moves or mutable borrows of `x` until the borrow ends
    let y = &x[0];
             ^
note: previous borrow ends here
fn main() {

}
^
```

Whew ! La compilateur Rust donne des erreurs plutôt détaillées à certains moments,
et c'est justement l'un de ces moments. Comme l'erreur l'explique, alors que nous
avons crée une variable mutable, nous ne pouvons pas utiliser la méthode `push`.
C'est parce que nous avons déjà une référence sur un élément du vecteur, `y`.
Modifier quelque chose alors qu'une autre référence sur cet objet existe est
dangereux, parce que cela pourrait rendre cette référence invalide. Dans ce cas
spécifique, quand nous créons le vecteur, nous n'avons alloué de la mémoire que
pour deux éléments. En ajouter un troisième signifierait allouer un nouveau bout
de mémoire pour tous ces nouveaux éléments, copier les anciennes valeurs dedans
et mettre à jour le pointeur interne pointant sur cette mémoire. Ça fonctionne
très bien. Le problème est que `y` ne serait pas mis à jour et nous nous
retrouverions avec un pointeur invalide. Ce qui n'est pas bon. Tout usage de `y`
serait une erreur dans ce contexte, et donc le compilateur nous a montré notre
erreur.

Donc maintenant, comment résoudre ce problème ? Il y a 2 façons de faire. La
première est de créer une copie plutôt que d'utiliser une référence :

```rust
fn main() {
    let mut x = vec!["Hello", "world"];

    let y = x[0].clone();

    x.push("foo");
}
```

Rust utilise la [sémantique move][move] par défaut, donc si nous voulons créer une
copie de données, nous pouvons appeler la méthode `clone()`. Dans cet exemple, `y`
n'est plus une référence sur le vecteur stocké dans `x`, mais une copie de son
premier élément, `"Hello"`. Maintenant que nous n'avons plus de référence, notre
`push()` fonctionne parfaitement.

[move]: propriete.html#move-semantics

Si nous voulons vraiment une référence, nous allons devoir utiliser l'autre
solution : sortir du scope courant avant d'essayer de modifier notre variable.
Ça ressemble à ça :

```rust
fn main() {
    let mut x = vec!["Hello", "world"];

    {
        let y = &x[0];
    }

    x.push("foo");
}
```

Nous avons créé un sous-scope (inner scope) en ajoutant des accolades. `y` sortira
du scope avant que l'on appelle `push()`, et donc tout est bon.

Ce concept de propriété n'est pas seulement bon pour prévenir des pointeurs invalides,
mais aussi un ensemble complets d'autres problèmes liés, comme des itérateurs
invalides, de la concurrence et bien plus.
