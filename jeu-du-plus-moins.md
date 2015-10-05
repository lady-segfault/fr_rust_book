% Jeu du plus ou moins

Pour notre premier projet, nous allons implémenter un problème de programmation
classique pour les débutants : le jeu du plus ou moins. Voilà comment il
fonctionne : notre programme va générer un nombre aléatoire entre 1 et 100.
Il affichera ensuite un prompt pour nous permettre d'entrer le nombre de
notre choix. Selon ce que nous aurons entré, il nous dira si c'est plus ou
moins. Quand nous aurons deviné le nombre, il nous félicitera. C'est bon pour
vous ?

# Mise en place

Commençons par créer un nouveau projet. Allez dans votre dossier projet. Vous
vous souvenez comment nous avons créé la structure de notre dossier et le
fichier `Cargo.toml` pour `hello_world` ? Cargo a une commande qui fait tout
ça pour nous. Essayons-la :

```bash
$ cd ~/projects
$ cargo new jeu_plus_moins --bin
$ cd jeu_plus_moins
```

Nous passons le nom de notre projet à `cargo new` ainsi que le flag `--bin`,
étant donné que nous créons un exécutable plutôt qu'une bibliothèque.

Jetons un coup d'oeil au fichier `Cargo.toml` généré :

```toml
[package]

name = "jeu_plus_moins"
version = "0.1.0"
authors = ["Votre nom <vous@exemple.com>"]
```

Cargo obtient cette information de votre environnement. Si c'est incorrect,
corrigez la.

Enfin, Cargo a généré un "Hello, world!" pour nous. Regardez dans
`src/main.rs` :

```rust
fn main() {
    println!("Hello, world!");
}
```

Compilons ce que Cargo nous a donné :

```{bash}
$ cargo build
   Compiling jeu_plus_moins v0.1.0 (file:///home/vous/projets/jeu_plus_moins)
```

Excellent ! Ouvrez votre fichier `src/main.rs` de nous nouveau. Nous
écrirons tout notre code à l'intérieur.

Avant de continuer, laissez-moi vous montrer une autre commande de Cargo :
`run`. `cargo run` ressemble à `cargo build`, mais il lance aussi l'exécutable
généré si la compilation s'est bien déroulée. Essayez la :

```bash
$ cargo run
   Compiling jeu_plus_moins v0.1.0 (file:///home/vous/projets/jeu_plus_moins)
     Running `target/debug/jeu_plus_moins`
Hello, world!
```

Merveilleux ! La commande `run` est pratique quand vous avez besoin d'itérer
rapidement sur un projet. Notre jeu correspond à ce cas, nous avons besoin de
tester chaque nouvelle itération avant de continuer sur la suivante.

# Entrer un choix

La première chose que nous avons besoin de savoir faire pour notre jeu du
plus ou moins est de permettre d'entrer un nombre. Rajouter ceci dans votre
`src/main.rs` :

```rust,no_run
use std::io;

fn main() {
    println!("Devinez le nombre !");

    println!("Veuillez entrer un nombre.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .ok()
        .expect("Impossible de lire la ligne");

    println!("Vous avez entré : {}", guess);
}
```

Il y en a beaucoup ici ! Je vais vous expliquer tout ça, petit à petit.

```rust,ignore
use std::io;
```

Nous aurons besoin de récupérer ce que l'utilisateur va rentrer et ensuite
l'afficher. Du coup, nous avons besoin de la bibliothèque `io` de la bibliothèque
standard. Rust importe seulement quelques trucs par défaut dans tous les
programmes, le [prélude][prelude]. Si ce n'est pas dans le prélude, vous devrez
le `use` directement. Il y a aussi un second "prélude", le [prélude `io`]
[ioprelude] qui donne accès à des fonctions similaires : vous l'importez et il
importe un grand de choses utiles liées à `io`.

[prelude]: https://doc.rust-lang.org/stable/std/prelude/index.html
[ioprelude]: https://doc.rust-lang.org/stable/std/io/prelude/index.html

```rust,ignore
fn main() {
```

Comme vous l'avez vu précédemment, la fonction `main()` est le point d'entrée
de votre programme. Le mot-clé `fn` permet de déclarer une nouvelle fonction,
les `()` indiquent qu'il n'y a pas d'argument et `{` débute le corps de la
fonction. Parce que nous n'avons pas inclus de type de retour, il est assumé
que c'est `()`, un [tuple][tuples] vide.

[tuples]: primitive-types.html#tuples

```rust,ignore
    println!("Devinez le nombre !");

    println!("Veuillez entrer un nombre.");
```

Nous avons vu précédemment que `println!()` était une [macro][macros]
qui affichait une [string][strings] à l'écran.

[macros]: macros.html
[strings]: strings.html

```rust,ignore
    let mut guess = String::new();
```

Voilà quelque chose d'intéressant ! Il se passe beaucoup de choses dans
cette petite ligne. La première chose à noter est que c'est une [déclaration
let][let] qui est utilisée pour créer une variable ("variable bindings" en
anglais). Elles prennent cette forme :

```rust,ignore
let foo = bar;
```

[let]: variable-bindings.html

Cela créera une nouvelle variable appelée `foo` and lui donne la valeur de
`bar`. Dans beaucoup de langages, on dirait simplement que c'est une variable,
mais les associations de variable en Rust ont quelques tours dans leur sac.

Par exemple, elles sont [immutables][immutable] par défaut. C'est pourquoi
notre exemple utilise le mot-clé `mut` : cela rend une variable mutable.
`let` ne prend pas de nom à gauche de l'assignation, il accepte un "[pattern]
[patterns]". Nous utiliserons les patterns plus tard. Pour le moment nous
nous contenterons des bases :

```rust
let foo = 5; // immutable.
let mut bar = 5; // mutable
```

[immutable]: mutability.html
[patterns]: patterns.html

Oh, et `//` commencera un commentaire jusqu'à la fin de la ligne. Rust
ignore tout ce qui est contenu à l'intérieur des [commentaires]
[commentaires].

[commentaires]: comments.html

Maintenans nous savons que `let mut guess` va créer une variable mutable
appeléee `guess`, mais devons maintenant maintenant regarder de l'autre
côté du `=` pour voir ce qui lui est assignée : `String::new`.

`String` est un type String, fourni par la bibliothèque standard. Une
[`String`][string] est un bout de texte en UTF-8 donc on peut modifier
la taille.

[string]: https://doc.rust-lang.org/stable/std/string/struct.String.html

Le syntaxe `::new()` utilise `::` parce que c'est une "fonction associée"
d'un type particulier. Pour dire les choses plus simplement, elle est
associée au type `String` lui-même plutôt qu'à une instance de `String`.
Certains langages appellent ça une méthode statique.

Cette fonction est appelée `new()` parce qu'elle crée une nouvelle `String`
vide. Vous trouverez une fonction `new()` sur beaucoup d'autres types car
c'est un nom commun pour créer une nouvelle valeur d'un type.

Avançons :

```rust,ignore
    io::stdin().read_line(&mut guess)
        .ok()
        .expect("Impossible de lire la ligne");
```

Voilà qui est beaucoup plus ! Continuons de nouveau petit à petit. La
première ligne a deux partie. Voici la première :

```rust,ignore
io::stdin()
```

Vous vous souvenez quand nous avons `use`d `std::io` sur la première ligne
de notre programme ? Nous appelons maintenant une fonction qui y est associée.
Si nous n'avions pas utilisé `use std::io`, nous aurions pu appeler cette
fonction de cette façon : `std::io::stdin()`.

Cette fonction particulière retourne un "lien" avec l'entrée standard de
votre terminal. Plus particulièrement, un [std::io::Stdin][iostdin].

[iostdin]: https://doc.rust-lang.org/stable/std/io/struct.Stdin.html

La partie suivante va utiliser ce "lien" pour obtenir ce que l'utilisateur
va rentrer :

```rust,ignore
.read_line(&mut guess)
```

Ici, nous avons la méthode [`read_line()`][read_line] de notre lien.
[Les methodes][method] sont comme des fonctions associées, mais sont
seulement disponibles sur une instance particulière d'un type plutôt
que sur le type lui-même. Nous passons aussi un argument à
`read_line()` : `&mut guess`.

[read_line]: https://doc.rust-lang.org/stable/std/io/struct.Stdin.html#method.read_line
[method]: methode-syntaxe.html

Vous vous souvenez comment on a créé `guess` juste au-dessus ? Nous avions
dit que cette variable était mutable. Cependant, `read_line` ne prend pas
une `String` comme argument : elle prend `&mut String`. Rust a une
fonctionnalité appelée "[références][references]" qui vous permet d'avoir
de multiples références sur une même donnée, ce qui permet de réduire le
nombre de copies. Les références sont une fonctionnalité complexe et l'un
des points forts majeurs de Rust car elles sont sécurisées et faciles
d'utilisation. Nous n'avons pas besoin de rentrer dans les détails pour
finir notre programme actuel. Pour le moment, tout ce que nous avons besoin
de savoir est que, tout comme les assignations `let`, les références sont
immutables par défaut. Du coup, nous devons écrire `&mut guess` plutôt que
`&guess`.

Pourquoi `read_line()` prend une référence mutable sur une string ? Son
but est de récupérer ce que l'utilisateur a entré dans l'entrée standard
et de le mettre dans notre string. Donc elle prend cette string comme
argument, et pour ajouter les données, elle a besoin qu'elle soit mutable.

[references]: references-et-emprunt.html

Nous n'avons toujours pas fini avec cette ligne de code. Alors que c'est
une simple ligne de texte, c'est seulement la première partie de la
logique de la ligne de code :

```rust,ignore
        .ok()
        .expect("Impossible de lire la ligne");
```

Quand vous appelez une méthode avec la syntaxe `.foo()`, vous devriez
ajouter une une nouvelle ligne ou tout autre "espace blanc" (whitespace).
Ça aide à diviser les longues et à rendre le code plus lisible. Nous
aurions pu écrire :

```rust,ignore
    io::stdin().read_line(&mut guess).ok().expect("Impossible de lire la ligne");
```

Mais du coup, ça devient difficile à lire, donc nous l'avons divisé en
trois lignes pour trois appels de méthodes. Nous avons déjà parlé de
`read_line()`, mais qu'en est-il de `ok()` et `expect()` ? Hé bien, nous
avons déjà mentionné que `read_line()` met ce que l'utilisateur rentre
dans `&mut String` que nous lui avons passé, mais il retourne aussi une
valeur : dans notre cas, un [`io::Result`][ioresult]. Rust a un certain
nombre de types appelés `Result` dans sa bibliothèque standard : un
[`Result`][result] générique et d'autres versions spécifiques pour des
sous-bibliothèques, comme `io::Result`.

[ioresult]: https://doc.rust-lang.org/stable/std/io/type.Result.html
[result]: https://doc.rust-lang.org/stable/std/result/enum.Result.html

Le but de ces `Result` est d'encoder les informations de l'erreur. Les
valeurs du type `Result`, comme n'importe quel type, a des méthodes à lui.
Dans le cas présent, `io::Result` a une méthode `ok()`, qui dit "nous
voulons partir du principe que cette valeur est bonne, Sinon, renvoie juste
les informations de l'erreur".  Pourquoi la renvoyer ? Hé bien, pour un
programme aussi simple, nous voulons juste afficher l'erreur générique
et quitter car nous ne pouvons continuer si nous avons une erreur.
La [méthode `ok()`][ok] retourne une valeur qui contient une autre méthode
définie : `expect()`. La [méthode `expect()`][expect] prend la valeur sur
laquelle elle a été appelée, et si ce n'est pas une "bonne", [`panic!`]
[panic] sera appelée avec le message que vous avez donné. Un appel à
`panic!` fera planter notre programme et affichera le message.

[ok]: https://doc.rust-lang.org/stable/std/result/enum.Result.html#method.ok
[expect]: https://doc.rust-lang.org/stable/std/result/enum.Result.html#method.expect
[panic]: gestion-erreur.html

Si nous laissons les appels de ces deux méthodes, notre programme compilera
mais nous aurons un warning :

```bash
$ cargo build
   Compiling jeu_plus_moins v0.1.0 (file:///home/vous/projets/jeu_plus_moins)
src/main.rs:10:5: 10:39 warning: unused result which must be used,
#[warn(unused_must_use)] on by default
src/main.rs:10     io::stdin().read_line(&mut guess);
                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```

Rust nous averti que nous n'avons pas utilisé le `Result` retourné. Le warning
vient de l'annotation spécial qu'a `io::Result`. Rust essaie de dire que nous
n'avons pas géré une potentielle erreur. La bonne façon de supprimer cette
erreur est d'afficher sa description. Par chance, si nous voulons juste
crasher s'il y a un problème, nous pouvons utiliser ces deux petites méthodes.
Si nous pouvons récupérer de l'erreur d'une façon ou d'une autre, nous
devrions le faire, mais nous verrons cela dans un autre projet.

Il ne reste maintenant plus qu'une seule ligne de notre exemple :

```rust,ignore
    println!("You guessed: {}", guess);
}
```

Cela affiche la string dans laquelle nous avions sauvegardé ce que
l'utilisateur avait rentré. `{}` sert à indiquer que nous afficherons
quelque chose à cet emplacement, et donc nous lui passons `guess`
comme argument. Si vous avions plusieurs `{}`, nous devrions passer
plusieurs arguments :

```rust
let x = 5;
let y = 10;

println!("x et y: {} et {}", x, y);
```

Facile.

Bref, voilà qui est fait. Nous pouvons lancer ce que nous avons
avec `cargo run` :

```bash
$ cargo run
   Compiling jeu_plus_moins v0.1.0 (file:///home/vous/projets/jeu_plus_moins)
     Running `target/debug/jeu_plus_moins`
Devinez le nombre !
Veuillez entrer un nombre.
6
Vous avez entré : 6
```

Parfait ! Notre première partie est terminée : nous pouvons récupérer
l'entrée d'un utilisateur puis l'afficher.

# Generer a nombre secret

Ensuite, nous allons avoir besoin de générer un nombre secret. Rust n'inclut
pas encore de fonctionner de nombre aléatoire dans sa bibliothèque standard.
L'équipe Rust fournit cependant une [crate `rand`][randcrate]. Une "crate"
est un paquet de code Rust. Pour le moment, nous avons compilé des "crates
binaires", ou plus simplement des exécutables. `rand` est une "crate
bibliothèque" qui contient du code destiné à être utilisé dans d'autres
programmes.

[randcrate]: https://crates.io/crates/rand

C'est dans l'utilisation de crates externes que Cargo brille vraiment.
Avant que n'écrivions du code utilisant `rand`, nous aurons besoin de
modifier notre `Cargo.toml`. Ouvrez-le et ajoutez les lignes suivantes
à la fin :

```toml
[dependencies]

rand="0.3.0"
```

La section `[dependencies]` de `Cargo.toml` ressemble à la section `[package]` :
tout ce qui suit en fait partie tant que la section suivante n'a pas commencé.
Cargo utilise la section `dependencies` pour savoir quelles dépendances nous
avons sur des crates externes et de quelles versions nous avons besoin. Dans le
cas présent, nous avons spécifié la version `0.3.0`, ce que Cargo interprète
comme étant n'importe quelle release compatible avec la version spécifiée.
Cargo utilise la [Sématique de versionnage][semver] qui est un standard pour
écrire des numéros de version. Si nous voulions uniquement utiliser
spécifiquement `0.3.0`, nous devrions utiliser `=0.3.0`. Si nous voulions
utiliser la dernière version, nous pourrions utiliser `*`. Il est aussi possible
d'utiliser un rang de version, la [documentation de Cargo][cargodoc] contient
plus de détails.

[semver]: http://semver.org
[cargodoc]: http://doc.crates.io/crates-io.html

Maintenant, sans rien changer dans notre code, compilons dans notre projet :

```bash
$ cargo build
    Updating registry `https://github.com/rust-lang/crates.io-index`
 Downloading rand v0.3.8
 Downloading libc v0.1.6
   Compiling libc v0.1.6
   Compiling rand v0.3.8
   Compiling guessing_game v0.1.0 (file:///home/vous/projets/jeu_plus_moins)
```

(Vous pouvez voir d'autres versions, bien évidemment).

Beaucoup de nouveaux affichages ! Maintenant que nous avons une dépendance
externe, Cargo va chercher les dernières versions de tout ce que contient
le registre, qui est une copie de [crates.io][cratesio]. `crates.io` est
l'endroit où les gens de l'écosystème Rust postent leur projets Rust open
source pour que les autres puissent s'en servir.

[cratesio]: https://crates.io

Après avoir mis à jour le registre, Cargo va vérifier notre `[dependencies]`
et télécharger tout ce que nous n'avons pas encore. Dans notre cas, alors
que nous avions seulement dit que nous voulions dépendre de `rand`, nous
avons aussi récupéré une copie de `libc`. C'est parce que `rand` dépend de
`libc` pour fonctionner. Après les avoir téléchargé, it les compile puis
compile notre projet.

Si nous relançons `cargo build`, nous obtiendrons une sortie différente :

```bash
$ cargo build
```

Hé oui, rien ne s'affiche ! Cargo sait que notre projet a été compilé et
que toutes ses dépendances l'ont été aussi, donc il n'a aucune raison de le
refaire. Comme il n'a rien à faire, il se contente de terminer son
exécution. Si nous ouvrons `src/main.rs`, faisions une petite modification,
nous verrions alors une ligne :

```bash
$ cargo build
   Compiling jeu_plus_moins v0.1.0 (file:///home/vous/projets/jeu_plus_moins)
```

Donc, nous avons dit à Cargo que nous voulions n'importe quelle version
`0.3.x`, donc il est allé chercher la dernière version correspondante
disponible, la `0.3.8`. Qu'est-ce qu'il se passera quand la version `0.3.9`
sortira avec un important bugfix ? Même si les bugfixes sont importants,
que se passera-t-il si la `0.3.9` contient une régression qui casse notre
code ?

La réponse a cette question est le fichier `Cargo.lock` que vous trouverez
dans dossier projets. Quand vous compilez votre projet pour la première
fois, Cargo détermine toutes les versions qui correspondent à nos critères
et les écris ensuite dans le fichier `Cargo.lock`. Quand vous compilerez
votre projet dans le futur, Cargo verra que le fichier `Cargo.lock` existe
et utilisera la version spécifiée plutôt que de devoir chercher de nouveau
la version que nous voulons. En d'autres mots, nous resterons à la version
`0.3.8` tant que nous ne ferons pas d'upgrade nous-même.

Que faire si nous _voulons_ nous servir de la version `0.3.9` ? Cargo a une
autre commande : `update`, qui dit "ignore le lock, retrouve les dernières
versions qui correspondent à ce que je veux. Si t'en trouves, écris-les
dans le fichier lock". Mais, par défaut, Cargo regardera toujours plus
grand que `0.3.0` et plus petit que `0.4.0`. Si nous voulons la `0.4.x`,
nous devrons mettre à jour le fichier `Cargo.toml` directement. Si nous le
faisions, la prochaine fois que nous lancerions `cargo build`, Cargo mettra
l'index à jour et ré-évaluera les requis de `rand`.

Il y a beaucoup à dire au sujet de [Cargo][doccargo] et [son écosystème]
[doccratesio], mais pour l'instant, c'est tout ce que vous avez besoin de
savoir. Cargo facilite grandement l'utilisation de bibliothèques externes,
et les Rustacés tendent à écrire de plus petits projets qui sont assemblés
à partir de plusieurs sous-paquets.

[doccargo]: http://doc.crates.io
[doccratesio]: http://doc.crates.io/crates-io.html

Il est temps de réellement _utiliser_ `rand`. Voici l'étape suivante :

```rust,ignore
extern crate rand;

use std::io;
use rand::Rng;

fn main() {
    println!("Devinez le nombre !");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The nombre secret est : {}", secret_number);

    println!("Veuillez entrer un nombre.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .ok()
        .expect("Impossible de lire la ligne");

    println!("Vous avez entré : {}", guess);
}
```

La première chose que nous avons fait a été de changer la première
ligne. Il est maintenant écrit `extern crate rand`. Parce que nous
avons déclaré `rand` dans notre `[dependencies]`, nous pouvons
utiliser `extern crate` pour permettre à Rust de savoir que nous
allons nous en servir. C'est d'ailleurs un équivalent à `use rand;`,
donc nous pouvons utiliser tout ce qui se trouve dans `rand` avec
`rand::`.

Next, we added another `use` line: `use rand::Rng`. We’re going to use a
method in a moment, and it requires that `Rng` be in scope to work. The basic
idea is this: methods are defined on something called ‘traits’, and for the
method to work, it needs the trait to be in scope. For more about the
details, read the [traits][traits] section.

[traits]: traits.html

There are two other lines we added, in the middle:

```rust,ignore
    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);
```

We use the `rand::thread_rng()` function to get a copy of the random number
generator, which is local to the particular [thread][concurrency] of execution
we’re in. Because we `use rand::Rng`’d above, it has a `gen_range()` method
available. This method takes two arguments, and generates a number between
them. It’s inclusive on the lower bound, but exclusive on the upper bound,
so we need `1` and `101` to get a number ranging from one to a hundred.

[concurrency]: concurrency.html

The second line just prints out the secret number. This is useful while
we’re developing our program, so we can easily test it out. But we’ll be
deleting it for the final version. It’s not much of a game if it prints out
the answer when you start it up!

Try running our new program a few times:

```bash
$ cargo run
   Compiling guessing_game v0.1.0 (file:///home/you/projects/guessing_game)
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 7
Please input your guess.
4
You guessed: 4
$ cargo run
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 83
Please input your guess.
5
You guessed: 5
```

Great! Next up: let’s compare our guess to the secret guess.

# Comparing guesses

Now that we’ve got user input, let’s compare our guess to the random guess.
Here’s our next step, though it doesn’t quite compile yet:

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .ok()
        .expect("failed to read line");

    println!("You guessed: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less    => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal   => println!("You win!"),
    }
}
```

A few new bits here. The first is another `use`. We bring a type called
`std::cmp::Ordering` into scope. Then, five new lines at the bottom that use
it:

```rust,ignore
match guess.cmp(&secret_number) {
    Ordering::Less    => println!("Too small!"),
    Ordering::Greater => println!("Too big!"),
    Ordering::Equal   => println!("You win!"),
}
```

The `cmp()` method can be called on anything that can be compared, and it
takes a reference to the thing you want to compare it to. It returns the
`Ordering` type we `use`d earlier. We use a [`match`][match] statement to
determine exactly what kind of `Ordering` it is. `Ordering` is an
[`enum`][enum], short for ‘enumeration’, which looks like this:

```rust
enum Foo {
    Bar,
    Baz,
}
```

[match]: match.html
[enum]: enums.html

With this definition, anything of type `Foo` can be either a
`Foo::Bar` or a `Foo::Baz`. We use the `::` to indicate the
namespace for a particular `enum` variant.

The [`Ordering`][ordering] `enum` has three possible variants: `Less`, `Equal`,
and `Greater`. The `match` statement takes a value of a type, and lets you
create an ‘arm’ for each possible value. Since we have three types of
`Ordering`, we have three arms:

```rust,ignore
match guess.cmp(&secret_number) {
    Ordering::Less    => println!("Too small!"),
    Ordering::Greater => println!("Too big!"),
    Ordering::Equal   => println!("You win!"),
}
```

[ordering]: ../std/cmp/enum.Ordering.html

If it’s `Less`, we print `Too small!`, if it’s `Greater`, `Too big!`, and if
`Equal`, `You win!`. `match` is really useful, and is used often in Rust.

I did mention that this won’t quite compile yet, though. Let’s try it:

```bash
$ cargo build
   Compiling guessing_game v0.1.0 (file:///home/you/projects/guessing_game)
src/main.rs:28:21: 28:35 error: mismatched types:
 expected `&collections::string::String`,
    found `&_`
(expected struct `collections::string::String`,
    found integral variable) [E0308]
src/main.rs:28     match guess.cmp(&secret_number) {
                                   ^~~~~~~~~~~~~~
error: aborting due to previous error
Could not compile `guessing_game`.
```

Whew! This is a big error. The core of it is that we have ‘mismatched types’.
Rust has a strong, static type system. However, it also has type inference.
When we wrote `let guess = String::new()`, Rust was able to infer that `guess`
should be a `String`, and so it doesn’t make us write out the type. And with
our `secret_number`, there are a number of types which can have a value
between one and a hundred: `i32`, a thirty-two-bit number, or `u32`, an
unsigned thirty-two-bit number, or `i64`, a sixty-four-bit number or others.
So far, that hasn’t mattered, and so Rust defaults to an `i32`. However, here,
Rust doesn’t know how to compare the `guess` and the `secret_number`. They
need to be the same type. Ultimately, we want to convert the `String` we
read as input into a real number type, for comparison. We can do that
with three more lines. Here’s our new program:

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .ok()
        .expect("failed to read line");

    let guess: u32 = guess.trim().parse()
        .ok()
        .expect("Please type a number!");

    println!("You guessed: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less    => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal   => println!("You win!"),
    }
}
```

The new three lines:

```rust,ignore
    let guess: u32 = guess.trim().parse()
        .ok()
        .expect("Please type a number!");
```

Wait a minute, I thought we already had a `guess`? We do, but Rust allows us
to ‘shadow’ the previous `guess` with a new one. This is often used in this
exact situation, where `guess` starts as a `String`, but we want to convert it
to an `u32`. Shadowing lets us re-use the `guess` name, rather than forcing us
to come up with two unique names like `guess_str` and `guess`, or something
else.

We bind `guess` to an expression that looks like something we wrote earlier:

```rust,ignore
guess.trim().parse()
```

Followed by an `ok().expect()` invocation. Here, `guess` refers to the old
`guess`, the one that was a `String` with our input in it. The `trim()`
method on `String`s will eliminate any white space at the beginning and end of
our string. This is important, as we had to press the ‘return’ key to satisfy
`read_line()`. This means that if we type `5` and hit return, `guess` looks
like this: `5\n`. The `\n` represents ‘newline’, the enter key. `trim()` gets
rid of this, leaving our string with just the `5`. The [`parse()` method on
strings][parse] parses a string into some kind of number. Since it can parse a
variety of numbers, we need to give Rust a hint as to the exact type of number
we want. Hence, `let guess: u32`. The colon (`:`) after `guess` tells Rust
we’re going to annotate its type. `u32` is an unsigned, thirty-two bit
integer. Rust has [a number of built-in number types][number], but we’ve
chosen `u32`. It’s a good default choice for a small positive number.

[parse]: ../std/primitive.str.html#method.parse
[number]: primitive-types.html#numeric-types

Just like `read_line()`, our call to `parse()` could cause an error. What if
our string contained `A👍%`? There’d be no way to convert that to a number. As
such, we’ll do the same thing we did with `read_line()`: use the `ok()` and
`expect()` methods to crash if there’s an error.

Let’s try our program out!

```bash
$ cargo run
   Compiling guessing_game v0.1.0 (file:///home/you/projects/guessing_game)
     Running `target/guessing_game`
Guess the number!
The secret number is: 58
Please input your guess.
  76
You guessed: 76
Too big!
```

Nice! You can see I even added spaces before my guess, and it still figured
out that I guessed 76. Run the program a few times, and verify that guessing
the number works, as well as guessing a number too small.

Now we’ve got most of the game working, but we can only make one guess. Let’s
change that by adding loops!

# Looping

The `loop` keyword gives us an infinite loop. Let’s add that in:

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .ok()
            .expect("failed to read line");

        let guess: u32 = guess.trim().parse()
            .ok()
            .expect("Please type a number!");

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less    => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal   => println!("You win!"),
        }
    }
}
```

And try it out. But wait, didn’t we just add an infinite loop? Yup. Remember
our discussion about `parse()`? If we give a non-number answer, we’ll `return`
and quit. Observe:

```bash
$ cargo run
   Compiling guessing_game v0.1.0 (file:///home/you/projects/guessing_game)
     Running `target/guessing_game`
Guess the number!
The secret number is: 59
Please input your guess.
45
You guessed: 45
Too small!
Please input your guess.
60
You guessed: 60
Too big!
Please input your guess.
59
You guessed: 59
You win!
Please input your guess.
quit
thread '<main>' panicked at 'Please type a number!'
```

Ha! `quit` actually quits. As does any other non-number input. Well, this is
suboptimal to say the least. First, let’s actually quit when you win the game:

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .ok()
            .expect("failed to read line");

        let guess: u32 = guess.trim().parse()
            .ok()
            .expect("Please type a number!");

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less    => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal   => {
                println!("You win!");
                break;
            }
        }
    }
}
```

By adding the `break` line after the `You win!`, we’ll exit the loop when we
win. Exiting the loop also means exiting the program, since it’s the last
thing in `main()`. We have just one more tweak to make: when someone inputs a
non-number, we don’t want to quit, we just want to ignore it. We can do that
like this:

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .ok()
            .expect("failed to read line");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less    => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal   => {
                println!("You win!");
                break;
            }
        }
    }
}
```

These are the lines that changed:

```rust,ignore
let guess: u32 = match guess.trim().parse() {
    Ok(num) => num,
    Err(_) => continue,
};
```

This is how you generally move from ‘crash on error’ to ‘actually handle the
error’, by switching from `ok().expect()` to a `match` statement. The `Result`
returned by `parse()` is an `enum` just like `Ordering`, but in this case, each
variant has some data associated with it: `Ok` is a success, and `Err` is a
failure. Each contains more information: the successfully parsed integer, or an
error type. In this case, we `match` on `Ok(num)`, which sets the inner value
of the `Ok` to the name `num`, and then we just return it on the right-hand
side. In the `Err` case, we don’t care what kind of error it is, so we just
use `_` instead of a name. This ignores the error, and `continue` causes us
to go to the next iteration of the `loop`.

Now we should be good! Let’s try:

```bash
$ cargo run
   Compiling guessing_game v0.1.0 (file:///home/you/projects/guessing_game)
     Running `target/guessing_game`
Guess the number!
The secret number is: 61
Please input your guess.
10
You guessed: 10
Too small!
Please input your guess.
99
You guessed: 99
Too big!
Please input your guess.
foo
Please input your guess.
61
You guessed: 61
You win!
```

Awesome! With one tiny last tweak, we have finished the guessing game. Can you
think of what it is? That’s right, we don’t want to print out the secret
number. It was good for testing, but it kind of ruins the game. Here’s our
final source:

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .ok()
            .expect("failed to read line");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less    => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal   => {
                println!("You win!");
                break;
            }
        }
    }
}
```

# Complete!

At this point, you have successfully built the Guessing Game! Congratulations!

This first project showed you a lot: `let`, `match`, methods, associated
functions, using external crates, and more. Our next project will show off
even more.
