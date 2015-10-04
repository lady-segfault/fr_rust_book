% Hello, Cargo!

[Cargo][cratesio] est un outil que les Rustacés utilise pour gérer leurs projets
Rust. Cargo est actuellement en pre-1.0, et donc est toujours en cours de
développement. Cependant, il est déjà suffisamment avancé pour être utilisé
dans de nombreux projets Rust, et donc il est considéré comme commun que les
projets Rust utilisent Cargo depuis leur création.

[cratesio]: http://doc.crates.io

Cargo gère trois choses : compiler votre code, télécharger les dépendances dont
votre code a besoin et compiler ces dépendances. Au début, votre programme n'a
aucune dépendance, donc nous n'utiliserons que les fonctionnalités de base.
Eventuellement, nous en ajouterons. Puisque nous avons commencé en utilisant
Cargo, il sera facile d'en ajouter plus tard.

Si vous avez installé Rust via les installeurs officiels, vous aurez aussi
Cargo. Sinon, vous voudrez sans doute [regarder le README de Cargo][cargoreadme]
pour obtenir les instructions d'installation.

[cargoreadme]: https://github.com/rust-lang/cargo#installing-cargo-from-nightlies

## Convertir à Cargo

Commençons par convertir Hello World à Cargo.

Pour Cargo-ifier votre projet, nous aurons besoin de trois choses :
 * Créer un fichier de configuration `Cargo.toml`.
 * Mettre notre code source au bon endroit.
 * Supprimer les anciens fichiers exécutables (`main.exe` sur Windows, `main`
partout ailleurs).
Commencer par la dernière partie :

```bash
$ mkdir src
$ mv main.rs src/main.rs
$ rm main  # or main.exe on Windows
```

Vous noterez que comme nous avons créé un exécutable, nous conservons `main.rs`
comme fichier source. Si nous voulons créer une bibliothèque plutôt, nous
devrions utiliser `lib.rs` à la place. Cette convention est utilisée par Cargo
pour bien compiler nos projets, mais on peut s'en passer si on le souhaite.
Des emplacements de fichiers spécifiques peuvent être spécifiés avec la clé
[`[lib]` ou `[[bin]]`][crates-custom] dans le fichier TOML.

[crates-custom]: http://doc.crates.io/manifest.html#configuring-a-target

Cargo s'attend à ce que vos fichiers sources soient dans un dossier `src`. Ainsi,
le dossier supérieur peut être utilisé pour d'autres choses, comme des README,
des informations de licence et n'importe quoi d'autre qui ne soient pas
directement lié à votre code. Cargo vous aide à garder vos projets propres et
structurés. Chaque chose à sa place et chaque place a sa chose.

Ensuite, notre fichier de configuration :

```bash
$ editor Cargo.toml
```

Faites bien attention au nom : vous avez besoin du `C` majuscule !

Ajoutez ceci dans le fichier :

```toml
[package]

name = "hello_world"
version = "0.0.1"
authors = [ "Votre nom <vous@exemple.com>" ]
```

Ce fichier utilise le format [TOML][toml]. Le TOML est similaire au INIT, mais
propose plus de choses. D'après la documentation de TOML :

> TOML aims to be a minimal configuration file format that's easy to read due
> to obvious semantics. TOML is designed to map unambiguously to a hash table.
> TOML should be easy to parse into data structures in a wide variety of
> languages.

[toml]: https://github.com/toml-lang/toml

Une fois que nous avons ce fichier en place dans le dossier de notre projet,
nous devrions être prêts à compiler. Pour ce faire :

```bash
$ cargo build
   Compiling hello_world v0.0.1 (file:///home/yourname/projects/hello_world)
$ ./target/debug/hello_world
Hello, world!
```

Bam ! Nous avons compilé notre projet avec `cargo build`, et nous l'avons
exécuté avec `./target/debug/hello_world`. Il est possible de faire les
deux d'un coup avec `cargo run` :

```bash
$ cargo run
     Running `target/debug/hello_world`
Hello, world!
```

Vous noterez que le projet n'a pas été recompilé cette fois. Cargo a
remarqué qu'aucun fichier source n'a été modifié, et s'est donc contenté
de lancer l'exéctuable. Si nous avions fait une modification, vous auriez
eu :

```bash
$ cargo run
   Compiling hello_world v0.0.1 (file:///home/yourname/projects/hello_world)
     Running `target/debug/hello_world`
Hello, world!
```

Ça ne nous a pas apporté grand chose par rapport à une simple utilisation
de `rustc`, mais pensez plus loin : quand notre projet deviendra plus
complexe, nous aurons besoin de faire plus de choses pour compiler
correctement. Avec Cargo, la commande restera toujours `cargo build` et
ça continuera de fonctionner.

Quand votre projet est finalement prêt pour sortir en release, vous
pouvez utiliser `cargo build --release` pour compiler votre projet
avec des optimisations.

Vous remarquerez aussi que Cargo a créé un nouveau fichier : `Cargo.lock`.

```toml
[root]
name = "hello_world"
version = "0.0.1"
```

`Cargo.lock` est utilisé par Cargo pour garder une trace de vos dépendances dans
votre programme. Pour l'instant, nous n'en avons pas, donc le fichier n'est pas
très rempli. Vous n'aurez jamais besoin de toucher à ce fichier, laissez Cargo
s'en charger.

C'est bon ! Nous avons réussi à compiler `hello_world` avec Cargo. Même si
notre programme est basique, il utilise les "vrais" outils dont vous vous
servirez tout au long de votre carrière dans Rust. Vous pouvez vous attendre à
faire ceci avec à peu près tout vos projets Rust :

```bash
$ git clone someurl.com/foo
$ cd foo
$ cargo build
```

## Un nouveau projet

Vous n'avez pas besoin de refaire tout ce que l'on vient de faire à chaque fois
que vous créerez un nouveau projet ! Cargo permet de créer un dossier de projet
dans lequel vous pourrez commencer à développer de suite.

Pour créer un nouveau projet avec Cargo, utilisez `cargo new` :

```bash
$ cargo new hello_world --bin
```

Nous passons `--bin` parce que notre but est de créer un projet exécutable, et non
pas une bibliothèque. Les exécutables sont souvent appelés "binaires" (comme dans
`/usr/bin`, si vous êtes sur un système Unix).

Vérifions ce que Cargo a généré pour nous :

```bash
$ cd hello_world
$ tree .
.
├── Cargo.toml
└── src
    └── main.rs

1 directory, 2 files
```

Si vous n'avez pas la commande `tree`, vous pouvez certainement l'installer
depuis le manager de paquet de votre distribution. Ce n'est pas obligatoire,
mais c'est utile.

C'est tout ce dont on a besoin pour commencer. Premièrement, jettons un coup
d'oeil à `Cargo.toml` :

```toml
[package]

name = "hello_world"
version = "0.1.0"
authors = ["Your Name <you@example.com>"]
```

Cargo a rempli ce fichier avec des valeurs par défaut basées sur les
arguments que vous lui avez donné et sur votre configuration `git` globale.
Vous remarquerez que Cargo a aussi initialisé le dossier `hello_world`
commme un répertoire `git`.

Voilà ce qu'il y a dans `src/main.rs` :

```rust
fn main() {
    println!("Hello, world!");
}
```

Cargo a généré un "Hello World!" pour nous, et vous êtes prêt à commencer
à coder ! Cargo a son propre [guide][guide] qui couvre toutes ses
fonctionnalités plus en profondeur.

[guide]: http://doc.crates.io/guide.html

Maintenant que l'on a vu les outils, il est temps d'apprendre le langage
Rust lui-même. Ce que nous venons de voir sont les bases que vous serviront
tout au long de votre utilisation de Rust.

Vous avez deux options : plongez dans un projet avec «[Apprendre Rust][learnrust]»,
ou commencer par les bases et augmenter la difficulté progressivement avec
«[Syntaxe et Sémantique][syntaxe] ». Les développeurs systèmes plus expérimentés
préféreront probablement «Apprendre Rust », tandis que ceux issus de milieux
dynamiques préféreront sans doute l'autre. Différentes personnes apprennent
différemment, choisissez ce qui vous convient le mieux !

[learnrust]: learn-rust.html
[syntax]: syntax-and-semantics.html
