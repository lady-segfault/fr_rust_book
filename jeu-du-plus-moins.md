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

Ensuite, nous avons ajouter une autre ligne `use` : `use rand::Rng`. Nous
allons bientôt utiliser une méthode qui a besoin que `Rng` soit importé
dans le scope courant. L'idée de base est la suivante : les méthodes sont
définies sur ce que l'on appelles des "traits", et pour qu'une méthode
fonctionne, il y a besoin que le trait soit importé. Pour plus de détails,
lisez la section [traits][traits].

[traits]: traits.html

Nous avons aussi ajouté deux autres lignes au milieu :

```rust,ignore
    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("Le nombre secret est : {}", secret_number);
```

Nous utilisons la fonction `rand::thread_rng()` pour obtenir une copie
du générateur de nombre aléatoire, ce qui est local au [thread][concurrence]
d'exécution dans lequel nous nous trouvons. Parce que nous avons fait
`use rand::Rng` au-dessus, la méthode `gen_range()` est disponible. Cette
méthode prend deux arguments et génère un nombre compris entre les deux.
Cela inclut le plus petit nombre mais exclut le plus grand, donc nous avons
besoin de `1` et `101` pour obtenir un nombre compris entre 1 (inclus) et
100.

[concurrence]: concurrence.html

La deuxième ligne affiche le nombre secret. C'est utile pendant le
développement de notre programme pour que nous puissions le tester
facilement. Nous le supprimerons de la version finale (ce ne serait
pas vraiment un jeu si nous affichions la réponse avant même de
commencer !).

Essayez de lancer notre programme plusieurs fois :

```bash
$ cargo run
   Compiling jeu_plus_moins v0.1.0 (file:///home/vous/projets/jeu_plus_moins)
     Running `target/debug/jeu_plus_moins`
Devinez le nombre !
Le nombre secret est : 7
Veuillez entrer un nombre.
4
Vous avez entré : 4
$ cargo run
     Running `target/debug/jeu_plus_moins`
Devinez le nombre !
Le nombre secret est : 83
Veuillez entrer un nombre.
5
Vous avez entré : 5
```

Excellent ! Prochaine étape : comparer le nombre entré avec le nombre
secret.

# Comparaisons

Maintenant que nous avons récupéré ce que l'utilisateur a rentré, il va
falloir le comparer avec le nombre aléatoire généré. Voici la prochaine
étape, même si elle ne compile pas pour le moment :

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Devinez le nombre !");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("Le nombre secret est : {}", secret_number);

    println!("Veuillez entrer un nombre.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .ok()
        .expect("Impossible de lire la ligne");

    println!("Vous avez entré : {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less    => println!("Trop petit !"),
        Ordering::Greater => println!("Trop grand !"),
        Ordering::Equal   => println!("Vous l'avez trouvé !"),
    }
}
```

Pas mal de nouvelles choses encore une fois. La première est un nouveau
`use`. Nous avons importé dans le scope un type appelé `std::cmp::Ordering`.
Ensuite, 5 lignes en bas l'utilisent :

```rust,ignore
match guess.cmp(&secret_number) {
    Ordering::Less    => println!("Trop petit !"),
    Ordering::Greater => println!("Trop grand !"),
    Ordering::Equal   => println!("Vous avez gagné !"),
}
```

La méthode `cmp()` peut être appelée sur n'importe quoi qui peut être
comparé et prend une référence sur la chose avec laquelle on veut
comparer. Elle retourne le type `Ordering` que nous avons importé
précédemment. Nous utilisons un [`match`][match] pour déterminé
exactement quel genre de `Ordering` cela peut bien être. `Ordering`
est une [`enum`][enum], raccourci pour "énumération" qui ressemble
à ça :

```rust
enum Foo {
    Bar,
    Baz,
}
```

[match]: match.html
[enum]: enums.html

Avec cette définition, n'importe quoi de type `Foo` peut être
un `Foo::Bar` ou un `Foo::Baz`. Nous utilisons le `::` pour
indiquer le namespace pour un élément d'`enum` particulier.

L'`enum` [`Ordering`][ordering] a trois éléments : `Less`, `Equal`,
et `Greater`. Le `match` prend une valeur d'un type et vous laisse
créer une "branche" pour chaque valeur potentielle. Comme nous avons
trois types de `Ordering`, nous avons trois branches :

```rust,ignore
match guess.cmp(&secret_number) {
    Ordering::Less    => println!("Trop petit !"),
    Ordering::Greater => println!("Trop grand !"),
    Ordering::Equal   => println!("Vous avez gagné !"),
}
```

[ordering]: https://doc.rust-lang.org/stable/std/cmp/enum.Ordering.html

Si c'est `Less`, nous avons `Trop petit !`, si c'est `Greater`, `Trop grand !`
et si c'est `Equal`, nous affichons `Vous avez gagné !`. `match` est très
utile et est utilisé fréquemment en Rust.

J'ai dit que cela ne compilait pas encore cependant. Essayons :

```bash
$ cargo build
   Compiling jeu_plus_moins v0.1.0 (file:///home/vous/projets/jeu_plus_moins)
src/main.rs:28:21: 28:35 error: mismatched types:
 expected `&collections::string::String`,
    found `&_`
(expected struct `collections::string::String`,
    found integral variable) [E0308]
src/main.rs:28     match guess.cmp(&secret_number) {
                                   ^~~~~~~~~~~~~~
error: aborting due to previous error
Could not compile `jeu_plus_moins`.
```

Whew ! C'est une grosse error. Le coeur de toute cette bouillie est le
"mismatched types". Rust a un système de typage fort et statique. Cependant,
il a aussi de l'inférence. Quand nous avons écrit `let guess = String::new()`,
Rust a été capable d'inférer que `guess` devait être une `String`, et donc
nous n'avons pas été obligé de spécifier le type nous-même. Et avec notre
`secret_number`, il y a un certain nombre de types qui pouvaient avoir
une valeur entre 1 et 100 : `i32`, un nombre sur 32 bits, ou `u32`,
un nombre sur 32 bits non-signé, ou `i64`, un nombre sur 64 bits, etc...
Jusque là, ça n'avait pas d'importance, et Rust utilise `i32` par défaut.
Cependant, ici, Rust ne sait pas comment comparer `guess` et `secret_number`.
Ils ont besoin d'être du même type. En fin de compte, nous voudrions
convertir la `String` que nous avons récupéré de l'entrée utilisateur en
un "vrai" type représentant un nombre pour la comparaison. Nous pouvons
le faire en ajoutant trois lignes. Voici notre nouveau programme :

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Devinez le nombre !");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("Le nombre secret est : {}", secret_number);

    println!("Veuillez entrer un nombre.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .ok()
        .expect("Impossible de lire la ligne");

    let guess: u32 = guess.trim().parse()
        .ok()
        .expect("Veuillez entrer un nombre !");

    println!("Vous avez entré : {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less    => println!("Trop petit !"),
        Ordering::Greater => println!("Trop grand !"),
        Ordering::Equal   => println!("Vous l'avez trouvé !"),
    }
}
```

Les trois nouvelles lignes :

```rust,ignore
    let guess: u32 = guess.trim().parse()
        .ok()
        .expect("Veuillez entrer un nombre !");
```

Attendez une minute, je pensais que nous avions déjà un `guess` ? C'est le cas,
mais Rust permet de "cacher" le précédent `guess` avec un nouveau (on appelle
ça le "shadowing"). C'est souvent utilisé dans ce genre de situation. Dans le
cas présent, `guess` commence en tant que `String` mais nous le convertissons
en `u32`. Le "shadowing" permet de réutiliser le nom `guess` plutôt que de
nous forcer à créer deux noms uniques tels que `guess_str` et `guess` ou
quelque chose dans le genre.

Nous lions `guess` à une expression qui ressemble à quelque chose que nous
avons écrit un peu plus tôt :

```rust,ignore
guess.trim().parse()
```

Suivi par un appel à `ok().expect()`. Ici, `guess` est toujours l'ancien
`guess`, celui qui était une `String` contenant ce qu'avait entré l'utilisateur.
La méthode `trim` de `String` supprime tous les "white spaces" (tabulation,
retour à la ligne, espace) au début et à la fin de notre string. C'est important
car nous avons appué sur la touche "Entrée" pour valider notre saisie et ainsi
"satisfaire" `read_line()`. Cela signifie que si nous entrons `5` et appuyons
sur la touche Entrée, `guess` ressemblera à `5\n`. Le `\n` représente une
"nouvelle ligne", la touche Entrée donc. `trim()` supprime le `\n`, laissant
notre string avec juste `5`. La [méthode `parse()` d'une string][parse] la
parse en un nombre. Puisqu'elle peut parser plusieurs variétés de nombres,
nous devons donner à Rust un "indice" pour lui permettre de déterminer le
type de nombre exact que nous voulons. Par conséquent, `let guess: u32`.
Le `:` après `guess` dit à Rust que nous allons annoter ce type. `u32`
est un entier non signé de 32 bits. Rust a [plusieurs types de nombres
"built-in"][number], mais nous avons choisi `u32`. C'est un bon choix par
défaut pour un petit nombre positif (> 0).

[parse]: https://doc.rust-lang.org/stable/std/primitive.str.html#method.parse
[number]: types-primitifs.html#types-numeriques

Tout comme `read_line()`, notre appel à `parse()` pourrait causer une
erreur. Et si notre string contenait `A👍%` ? Impossible de convertir ça en
nombre. Du coup, nous devrons faire la même chose qu'avec `read_line()` :
utiliser les méthodes `ok()` et `expect()` pour crasher en cas d'erreur.

Essayons notre programme !

```bash
$ cargo run
   Compiling jeu_plus_moins v0.1.0 (file:///home/vous/projets/jeu_plus_moins)
     Running `target/jeu_plus_moins`
Devinez le nombre !
Le nombre secret est : 58
Veuillez entrer un nombre.
  76
Vous avez entré : 76
Trop grand !
```

Cool ! Vous pouvez voir que j'ai même ajouter des espaces avant d'entrer mon
nombre, et il devine quand même que c'est 76. Lancez le programme plusieurs
fois, et vérifiez quand le nombre fonctionne correctement, tout comme
rentrer un nombre trop petit.

Maintenant nous avons la majorité de notre jeu qui fonctionne, mais nous
n'avons qu'une seule chance pour trouver le nombre secret (difficulté un peu
trop élevée donc !). Changeons cela en ajoutons des boucles !

# Boucler

Le mot-clé `loop` nous permet de créer des boucles infinies. Ajoutons-le :

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Devinez le nombre !");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("Le nombre secret est : {}", secret_number);

    loop {
        println!("Veuillez entrer un nombre.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .ok()
            .expect("Impossible de lire la ligne");

        let guess: u32 = guess.trim().parse()
            .ok()
            .expect("Veuillez entrer un nombre !");

        println!("Vous avez entré : {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less    => println!("Trop petit !"),
            Ordering::Greater => println!("Trop grand !"),
            Ordering::Equal   => println!("Vous avez gagné !"),
        }
    }
}
```

Et maintenant essayez. Non mais attends, on vient pas juste d'ajouter une
boucle infinie ? Yup. Vous vous souvenez de notre discussion au sujet de
`parse()` ? Si nous entrons un nombre invalide, nous allons `return` et
quitter. Observez :

```bash
$ cargo run
   Compiling jeu_plus_moins v0.1.0 (file:///home/you/projects/jeu_plus_moins)
     Running `target/jeu_plus_moins`
Devinez le nombre !
Le nombre secret est : 59
Veuillez entrer un nombre.
45
Vous avez entré : 45
Trop petit !
Veuillez entrer un nombre.
60
Vous avez entré : 60
Trop grand !
Veuillez entrer un nombre.
59
Vous avez entré : 59
Vous avez gagné !
Veuillez entrer un nombre.
quit
thread '<main>' panicked at 'Veuillez entrer un nombre !'
```

Ha! `quit` a vraiment quitter. Tout comme n'importe quel input qui ne serait
pas un nombre. Bien, cela est sous-optimale pour ne pas dire plus.
Premièrement, quittons le jeu quand nous gagnons :

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Devinez le nombre !");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("Le nombre secret est : {}", secret_number);

    loop {
        println!("Veuillez entrer un nombre.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .ok()
            .expect("Impossible de lire la ligne");

        let guess: u32 = guess.trim().parse()
            .ok()
            .expect("Veuillez entrer un nombre !");

        println!("Vous avez entré : {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less    => println!("Trop petit !"),
            Ordering::Greater => println!("Trop grand !"),
            Ordering::Equal   => {
                println!("Vous avez gagné !");
                break;
            }
        }
    }
}
```

En ajoutant la ligne `break` après le `Vous avez gagné !`, nous allons quitter
la boucle quand nous gagnerons. Quitter la boucle signifie aussi quitter le
programme dans le cas présent car c'est la dernière chose dans notre
`main()`. Nous avons juste un truc à faire : quand quelqu'un entre un input
qui n'est pas un nombre, nous ne voulons pas quitter, nous voulons juste
l'ignorer. Nous pouvons le faire comme ceci :

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Devinez le nombre !");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("Le nombre secret est : {}", secret_number);

    loop {
        println!("Veuillez entrer un nombre.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .ok()
            .expect("Impossible de lire la ligne");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("Vous avez entré : {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less    => println!("Trop petit !"),
            Ordering::Greater => println!("Trop grand !"),
            Ordering::Equal   => {
                println!("Vous avez gagné !");
                break;
            }
        }
    }
}
```

Voici les lignes qui ont changé :

```rust,ignore
let guess: u32 = match guess.trim().parse() {
    Ok(num) => num,
    Err(_) => continue,
};
```

C'est généralement comment on passe de "crash on error" (planter quand on a
une erreur) à "vraiment gérer l'erreur", en transformant `ok().expect()` en
un `match`. Le `Result` retourné par `parse()` est un `enum` tout comme
`Ordering`, mais dans le cas présent, chaque élément a des données
associées : `Ok` est une succès tandis que `Err` est un échec. Chacun
contient des informations complémentaires : l'entier parsé avec succès
ou un type d'erreur. Pour le premier, nous faisons un `match` sur `Ok(num)`
qui met la valeur interne du `Ok` au nom `num`, et ensuite on se contente
de le retourner au "right-hand side" (côté droit de l'expression pour
faire simple). Dans le cas du `Err`, nous nous fichons de savoir quel
type d'erreur cela peut bien être et nous utilisons plutôt `_` plutôt
qu'un nom. Cela ignore l'erreur, et `continue` nous fait aller directement
à l'itération suivante de `loop`.

Maintenant ça devrait être bon ! Essayons :

```bash
$ cargo run
   Compiling jeu_plus_moins v0.1.0 (file:///home/vous/projets/jeu_plus_moins)
     Running `target/jeu_plus_moins`
Devinez le nombre !
Le nombre secret est : 61
Veuillez entrer un nombre.
10
Vous avez entré : 10
Trop petit !
Veuillez entrer un nombre.
99
Vous avez entré : 99
Trop grand !
Veuillez entrer un nombre.
foo
Veuillez entrer un nombre.
61
Vous avez entré : 61
Vous avez gagné !
```

Merveilleux ! 
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
    println!("Devinez le nombre !");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    loop {
        println!("Veuillez entrer un nombre.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .ok()
            .expect("Impossible de lire la ligne");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("Vous avez entré : {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less    => println!("Trop petit !"),
            Ordering::Greater => println!("Trop grand !"),
            Ordering::Equal   => {
                println!("Vous avez gagné !");
                break;
            }
        }
    }
}
```

# Fini !

À ce point, vous avez réussi à compiler le Jeu du Plus ou Moins ! Félicitations !

Ce premier projet vous en a montré beaucoup : `let`, `match`, les méthodes, les
fonctions assoécies, utiliser des crates externes, et bien plus. Notre prochain
projet montrera encore plus !
