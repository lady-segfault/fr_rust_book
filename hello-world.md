% Hello, world!

Maintenant que vous avez installé Rust, écrivons notre premier programme.
Il est traditionnel lorsqu'on écrit un programme dans un nouveau langage
de le faire afficher "Hello, world!" à l'écran. Le truc sympa en commençant
avec un programme aussi simple est que vous pouvez vérifier que le
compilateur n'est pas uniquement installé mais qu'il fonctionne aussi
correctement. Afficher des informations à l'écran est un moyen commun d'y
parvenir.

La première chose que nous devons faire est de créer un fichier pour y mettre
notre code. J'aime créer un dossier `projets` dans mon dossier home, et y
mettre tous mes projets. Rust se fiche de savoir où se situe votre code.

Une premier problème se pose : ce guide va partir du principe que vous avez
les bases avec les commandes shells. Rust lui-même n'a pas besoin d'outil
pour écrire du code particulier, ou l'endroit où se situe votre code. Si vous
préférez utiliser un IDE plutôt qu'un shell, vous voudrez peut-être jeter un
oeil à [SolidOak][solidoak], ou n'importe quel plugin de votre IDE préféré.
Il y a un grand nombre d'extensions de qualités variées, développées par la
communauté. L'équipe Rust propose aussi quelques [plugins pour divers
éditeurs de texte][plugins]. Configurer votre éditeur de texte ou votre IDE
ne fera pas partie de ce tutoriel, donc veuillez vous référer à la
documentation du plugin/IDE que vous souhaitez utiliser.

[solidoak]: https://github.com/oakes/SolidOak
[plugins]: https://github.com/rust-lang/rust/blob/master/src/etc/CONFIGS.md

Maintenant que cela a été dit, créons un dossier dans dossier de projets.

```bash
$ mkdir ~/projects
$ cd ~/projects
$ mkdir hello_world
$ cd hello_world
```

Si vous êtes sous Windows et que vous n'utilisez pas PowerShell, le `~` risque
de ne pas fonctionner. Veuillez consulter la documentation de votre shell
pour plus de détails.

Créons ensuite un nouveau fichier source. Nous l'appelerons `main.rs`. Les
fichiers Rust se termine toujours par l'extension `.rs`. Si vous utilisez
plusieurs mots dans le nom de fichier, séparez-les par un underscore :
`hello_world.rs` plutôt que `helloworld.rs`.

Maintenant que nous avons créé et ouvert le fichier, entrez ceci à
l'intérieur :

```rust
fn main() {
    println!("Hello, world!");
}
```

Enregistrez le fichier et ensuite tapez ceci dans votre shell :

```bash
$ rustc main.rs
$ ./main # or main.exe on Windows
Hello, world!
```

Ça a marché ! Expliquons ce qui s'est passé en détails :

```rust
fn main() {

}
```

Ces lignes définissent une *fonction* en Rust. La fonction `main` est
spéciale : c'est là que commence tout programme Rust. La première ligne
dit "Je déclare une fonction appelée `main` qui ne prend pas d'argument
et qui ne retourne rien." S'il y avait des arguments, ils iraient entre
les parenthèses (`(` et `)`), et parce que nous ne retournons rien, nous
pouvons complètement omettre de mettre un type de retour. Nous
reviendrons là-dessus plus tard.

Vous noterez aussi que la function est entourée par des accolades (`{` et
`}`). Rust les requière autour de tous les corps des fonctions. Il est aussi
considéré comme une bonne pratique de mettre l'accolade ouvrante sur la
même ligne que la déclaration de la fonction, avec un espace pour les
séparer.

Ensuite vient cette ligne :

```rust
    println!("Hello, world!");
```

Cette ligne fait tout le travail de notre petit programme. Il y a un grand
nombre de détails importants ici. Le premier est que l'indentation est faite
avec 4 espaces, pas des tabulations. Veuillez configurer votre éditeur de
texte pour insérer quatre espaces quand vous appuyez sur la touche de
tabulation. Nous fournissons [des configurations pour plusieurs éditeurs][configs].

[configs]: https://github.com/rust-lang/rust/tree/master/src/etc/CONFIGS.md

Le second point est le `println!()`. On appelle ça une [macro][macro], et c'est
ainsi que l'on fait de la métaprogrammation en Rust. Si c'était une fonction à
la place, ça ressemblerait plutôt à `println()`. Pour nos objectifs, nous n'avons
pas besoin de nous inquiéter de cette différence. Sachez juste que, de temps en
temps, vous verrez un `!`, ce qui voudra dire que vous appelerez une macro plutôt
qu'une fonction ordinaire. Rust implémente `println!` comme une macro plutôt que
comme une fonction pour de bonnes raisons, mais c'est un cours d'un niveau avancé.
Une dernière chose à mentionner : les macros de Rust sont très différentes de
celles que l'on peut croiser en C, si vous vous en êtes déjà servi. N'ayez pas
peur d'utiliser des macros. Nous verrons cela en détail, mais vous allez devoir
nous faire confiance pour le moment.

[macro]: macros.html

Ensuite, `"Hello, world!` est une "string". Les Strings sont un sujet étonnament
compliqué dans un langage de programmation système, et c'est une string
"allouée statiquement". Si vous voulez en savoir plus à propos de l'allocation,
lisez [la stack et la heap][allocation], mais vous n'en avez pas besoin dans
l'immédiat. Nous passons cette string comme argument à `println!` qui l'affiche
à l'écran. Facile !

[allocation]: the-stack-and-the-heap.html

Finalement, la ligne se termine avec un point-virgule (`;`). Rust est [un langage
"orienté expression"][expression-oriented language], ce qui signifie que la
plupart des choses sont des expressions plutôt que des déclarations. Le `;`
est utilisé pour indiquer que l'expression prend fin et que la suivante est
prête à commencer. La plupart des lignes de code Rust se terminent par un `;`.

[expression-oriented language]: sommaire.html#langage-orienté-expression

Finalement, pour compiler nous pouvons utiliser directement notre compilateur,
`rustc`, en passant le nom du fichier source :

```bash
$ rustc main.rs
```

C'est similaire à `gcc` ou `clang`, si vous avez déjà fait du C ou du C++. Rust
créera un fichier exécutable. Vous pouvez le voir utilisant `ls` :

```bash
$ ls
main  main.rs
```

Ou sur Windows :

```bash
$ dir
main.exe  main.rs
```

Il y a maintenant deux fichiers : notre fichier source, avec l'extension
`.rs`, et l'exécutable (`main.exe` sur Windows, `main` partout ailleurs).

```bash
$ ./main  # or main.exe on Windows
```

Cela affiche notre texte `Hello, world!` sur notre terminal.

Si vous venez d'un langage dynamique comme Ruby, Python ou Javascript,
vous risquez de ne pas être habitué à cette façon de faire en deux
étapes séparées. Rust est un "langage compilé en amont", ce qui signifie
que vous pouvez compiler un programme, le donner à quelqu'un d'autre et
ils n'auront pas besoin d'avoir installés Rust pour pouvoir l'exécuter.
Si vous donnez un `.rb` ou un `.py` ou encore un `.js` à quelqu'un,
cette personne aura besoin d'avoir installé Ruby/Python/Javascript
pour pouvoir s'en servir, mais vous n'avez besoin que d'une seule
commande pour compiler et exécuter votre programmae. Tout est un
compromis dans la conception d'un langage et Rust a fait son choix.

Félicitations ! Vous avez officiellement écrit un programme Rust. Vous
êtes maintenant un développeur Rust ! Bienvenue. 🎊🎉👍

Ensuite, j'aimerais vous présenter à un autre outil, Cargo, qui est utilisé
pour écrire des programmes Rust "réels" (real-world). Juste utiliser `rustc`
est utile pour des choses simples, mais quand votre projet grandira, vous
voudrez quelque chose pour gérer toutes ses options. Cela rendra aussi le
partage de code avec d'autres personnes et d'autres projets plus facile.
