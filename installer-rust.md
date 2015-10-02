% Installer Rust

La première étape pour utiliser Rust est de l'installer ! Il y a plusieurs
façons de faire, mais la plus simple est d'utiliser le script `rustup`. Si
vous êtes sur Linux ou sur Mac, vous n'avez besoin de ne faire que ceci :

> Note: vous n'avez pas besoin d'écrire les `$`, ils sont juste là pour
> indiquer le début de chaque commande. Vous verrez beaucoup de tutoriels
> et d'exemples sur internet qui utilisent cette convention : `$` pour
> les commandes lancées en tant que simple utilisateur, et `#` pour les
> commandes qui doivent être lancées en administrateur.

```bash
$ curl -sf -L https://static.rust-lang.org/rustup.sh | sh
```

Si vous êtes inquiets au sujet d'une [potentielle insécurité][insecurity] en
utilisant `curl | sh`, veuillez continuer de lire et regardez notre démenti
en bas. N'hésitez pas à utiliser une version en deux étapes de l'installation
et à examiner notre script d'installation :

```bash
$ curl -f -L https://static.rust-lang.org/rustup.sh -O
$ sh rustup.sh
```

[insecurity]: http://curlpipesh.tumblr.com

Si vous êtes sur Windows, veuillez télécharger l'[installeur][install-page]
approprié.
**NOTE :** Par défaut, l'installeur Windows n'ajoutera pas Rust à la variable
système %PATH%. Si c'est la seule version de Rust que vous installez et que vous
voulez être capable de la lancer depuis une console, cliquez sur "Advanced" sur
la boîte de dialogue d'installation et vérifiez sur la page "Product Features"
que "Add to PATH" est installé sur un disque dur local.


[install-page]: https://www.rust-lang.org/install.html

## Désinstallation

Si vous avez décidé que vous ne voulez plus utiliser Rust, nous serons un peu
tristes, mais pas de souci. Tous les langages de programmation ne conviennent
pas à tout le monde. Lancez juste le script de désinstallation :

```bash
$ sudo /usr/local/lib/rustlib/uninstall.sh
```

Si vous avez utilisé l'installeur Windows, relancez le `.msi` et vous verrez
l'option de désinstallation.

## That disclaimer we promised

Some people, and somewhat rightfully so, get very upset when we tell you to
`curl | sh`. Basically, when you do this, you are trusting that the good
people who maintain Rust aren't going to hack your computer and do bad things.
That's a good instinct! If you're one of those people, please check out the
documentation on [building Rust from Source][from-source], or [the official
binary downloads][install-page].

[from-source]: https://github.com/rust-lang/rust#building-from-source

## Platform support

Oh, we should also mention the officially supported platforms:

* Windows (7, 8, Server 2008 R2)
* Linux (2.6.18 or later, various distributions), x86 and x86-64
* OSX 10.7 (Lion) or later, x86 and x86-64

We extensively test Rust on these platforms, and a few others, too, like
Android. But these are the ones most likely to work, as they have the most
testing.

Finally, a comment about Windows. Rust considers Windows to be a first-class
platform upon release, but if we're honest, the Windows experience isn't as
integrated as the Linux/OS X experience is. We're working on it! If anything
does not work, it is a bug. Please let us know if that happens. Each and every
commit is tested against Windows just like any other platform.

## After installation

If you've got Rust installed, you can open up a shell, and type this:

```bash
$ rustc --version
```

You should see the version number, commit hash, and commit date. If you just
installed version 1.2.0, you should see:

```bash
rustc 1.2.0 (082e47636 2015-08-03)
```

If you did, Rust has been installed successfully! Congrats!

If you didn't and you're on Windows, check that Rust is in your %PATH% system
variable. If it isn't, run the installer again, select "Change" on the "Change,
repair, or remove installation" page and ensure "Add to PATH" is installed on
the local hard drive.

This installer also installs a copy of the documentation locally, so you can
read it offline. On UNIX systems, `/usr/local/share/doc/rust` is the location.
On Windows, it's in a `share/doc` directory, inside wherever you installed Rust
to.

If not, there are a number of places where you can get help. The easiest is
[the #rust IRC channel on irc.mozilla.org][irc], which you can access through
[Mibbit][mibbit]. Click that link, and you'll be chatting with other Rustaceans
(a silly nickname we call ourselves), and we can help you out. Other great
resources include [the user’s forum][users], and
[Stack Overflow][stackoverflow].

[irc]: irc://irc.mozilla.org/#rust
[mibbit]: http://chat.mibbit.com/?server=irc.mozilla.org&channel=%23rust
[users]: https://users.rust-lang.org/
[stackoverflow]: http://stackoverflow.com/questions/tagged/rust
