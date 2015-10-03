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

The second point is the `println!()` part. This is calling a Rust [macro][macro],
which is how metaprogramming is done in Rust. If it were a function instead, it
would look like this: `println()`. For our purposes, we don’t need to worry
about this difference. Just know that sometimes, you’ll see a `!`, and that
means that you’re calling a macro instead of a normal function. Rust implements
`println!` as a macro rather than a function for good reasons, but that's an
advanced topic. One last thing to mention: Rust’s macros are significantly
different from C macros, if you’ve used those. Don’t be scared of using macros.
We’ll get to the details eventually, you’ll just have to trust us for now.

[macro]: macros.html

Next, `"Hello, world!"` is a ‘string’. Strings are a surprisingly complicated
topic in a systems programming language, and this is a ‘statically allocated’
string. If you want to read further about allocation, check out
[the stack and the heap][allocation], but you don’t need to right now if you
don’t want to. We pass this string as an argument to `println!`, which prints the
string to the screen. Easy enough!

[allocation]: the-stack-and-the-heap.html

Finally, the line ends with a semicolon (`;`). Rust is an [‘expression oriented’
language][expression-oriented language], which means that most things are
expressions, rather than statements. The `;` is used to indicate that this
expression is over, and the next one is ready to begin. Most lines of Rust code
end with a `;`.

[expression-oriented language]: glossary.html#expression-oriented-language

Finally, actually compiling and running our program. We can compile with our
compiler, `rustc`, by passing it the name of our source file:

```bash
$ rustc main.rs
```

This is similar to `gcc` or `clang`, if you come from a C or C++ background. Rust
will output a binary executable. You can see it with `ls`:

```bash
$ ls
main  main.rs
```

Or on Windows:

```bash
$ dir
main.exe  main.rs
```

There are now two files: our source code, with the `.rs` extension, and the
executable (`main.exe` on Windows, `main` everywhere else)

```bash
$ ./main  # or main.exe on Windows
```

This prints out our `Hello, world!` text to our terminal.

If you come from a dynamic language like Ruby, Python, or JavaScript,
you may not be used to these two steps being separate. Rust is an
‘ahead-of-time compiled language’, which means that you can compile a program,
give it to someone else, and they don't need to have Rust installed. If you
give someone a `.rb` or `.py` or `.js` file, they need to have a
Ruby/Python/JavaScript implementation installed, but you just need one command
to both compile and run your program. Everything is a tradeoff in language
design, and Rust has made its choice.

Congratulations! You have officially written a Rust program. That makes you a
Rust programmer! Welcome. 🎊🎉👍

Next, I'd like to introduce you to another tool, Cargo, which is used to write
real-world Rust programs. Just using `rustc` is nice for simple things, but as
your project grows, you'll want something to help you manage all of the options
that it has, and to make it easy to share your code with other people and
projects.
