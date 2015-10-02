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

[gs]: commcencer.html
[lr]: apprendre-rust.html
[er]: rust-en-action.html
[ss]: syntaxe-et-semantiques.html
[nr]: nightly-rust.html
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

We’ve introduced another binding, `y`. In this case, `y` is a ‘reference’ to
the first element of the vector. Rust’s references are similar to pointers in
other languages, but with additional compile-time safety checks. References
interact with the ownership system by [‘borrowing’][borrowing] what they point
to, rather than owning it. The difference is, when the reference goes out of
scope, it will not deallocate the underlying memory. If it did, we’d
de-allocate twice, which is bad!

[borrowing]: references-and-borrowing.html

Let’s add a third line. It looks innocent enough, but causes a compiler error:

```rust,ignore
fn main() {
    let mut x = vec!["Hello", "world"];

    let y = &x[0];

    x.push("foo");
}
```

`push` is a method on vectors that appends another element to the end of the
vector. When we try to compile this program, we get an error:

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

Whew! The Rust compiler gives quite detailed errors at times, and this is one
of those times. As the error explains, while we made our binding mutable, we
still cannot call `push`. This is because we already have a reference to an
element of the vector, `y`. Mutating something while another reference exists
is dangerous, because we may invalidate the reference. In this specific case,
when we create the vector, we may have only allocated space for two elements.
Adding a third would mean allocating a new chunk of memory for all those elements,
copying the old values over, and updating the internal pointer to that memory.
That all works just fine. The problem is that `y` wouldn’t get updated, and so
we’d have a ‘dangling pointer’. That’s bad. Any use of `y` would be an error in
this case, and so the compiler has caught this for us.

So how do we solve this problem? There are two approaches we can take. The first
is making a copy rather than using a reference:

```rust
fn main() {
    let mut x = vec!["Hello", "world"];

    let y = x[0].clone();

    x.push("foo");
}
```

Rust has [move semantics][move] by default, so if we want to make a copy of some
data, we call the `clone()` method. In this example, `y` is no longer a reference
to the vector stored in `x`, but a copy of its first element, `"Hello"`. Now
that we don’t have a reference, our `push()` works just fine.

[move]: ownership.html#move-semantics

If we truly want a reference, we need the other option: ensure that our reference
goes out of scope before we try to do the mutation. That looks like this:

```rust
fn main() {
    let mut x = vec!["Hello", "world"];

    {
        let y = &x[0];
    }

    x.push("foo");
}
```

We created an inner scope with an additional set of curly braces. `y` will go out of
scope before we call `push()`, and so we’re all good.

This concept of ownership isn’t just good for preventing dangling pointers, but an
entire set of related problems, like iterator invalidation, concurrency, and more.
