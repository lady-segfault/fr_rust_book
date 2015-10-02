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

## Le démenti promis

Certaines personnes, et à juste titre, n'apprécient pas quand on leur dit de
faire `curl | sh`. Basiquement, quand vous faites ça, vous faites confiance
que les gentilles peronnes qui maintiennent Rust ne vont pas pirater votre
ordinateur et faire de mauvaises choses. C'est un bon instinct ! Si vous êtes
l'une de ces personnes, veuillez vérifier la documentation sur [compiler
Rust depuis le code source][from-source], ou [les téléchargements officiels
des binaires][install-page].

[from-source]: https://github.com/rust-lang/rust#building-from-source

## Support des plateformes

Oh, nous devrions aussi mentionner les plateformes supportées officiellement :

* Windows (7, 8, Server 2008 R2)
* Linux (2.6.18 or later, various distributions), x86 and x86-64
* OSX 10.7 (Lion) or later, x86 and x86-64

Nous testons Rust de manière intense sur ces plateformes, et quelques autres
aussi, comme Android. Mais celles citées précédemment sont celles qui sont
le plus supposé fonctionner car ce sont elles qui possèdent le plus de tests.

Finalement, un commentaire au sujet de Windows. Rust considère Windows comme
une plateforme de release de premier ordre, mais pour être honnête,
l'expérience Windows n'est pas aussi intégrée que celle de Linux/OS X. Nous
travaillons dessus ! Si quoi que ce soit ne fonctionne pas, c'est un bug.
Merci de nous tenir au courant si cela vous arrive. Chaque commit est aussi
testé sur Windows tout comme les autres plateformes.

## Après l'installation

Si vous avez installé Rust, vous pouvez ouvrir un shell et taper :

```bash
$ rustc --version
```

Vous devriez voir le numéro de version, le hash du commit et sa date. Si vous
avez installé la version 1.2.0, vous devriez voir :

```bash
rustc 1.2.0 (082e47636 2015-08-03)
```

Si c'est bien le cas, Rust a été correctement installé, félicitations !

Si ce n'est pas le cas et que vous êtes sous Windows, vérifiez si Rust est
bien dans votre variable système %PATH%. Sinon, relancez l'installeur,
sélectionnez "Change" sur la page "Change, repair, or remove installation"
et assurez-vous que "Add to PATH" est installé sur le disque dur local.

Cet installeur installe aussi une copie de la documentation localement pour
que vous puissiez y avoir accès hors-ligne. Sur les systèmes UNIX,
`/usr/local/share/doc/rust` est l'emplacement. Sur Windows, c'est dans le
dossier `share/doc`, à l'intérieur de l'endroit où vous avez installé Rust.

Si ce n'est toujours pas bon, il y a plusieurs endroits où vous pourrez
obtenir de l'aide. Le moyen le plus simple est [le channel #rust sur l'IRC à
l'adresse irc.mozilla.org][irc], auquel vous pouvez accéder grâce à
[Mibbit][mibbit] (ou tout autre client IRC). Cliquez sur ce lien et vous
pourrez discuter avec d'autres Rustacés (un gentil petit surnom que l'on s'est
donnés) pour que l'on puisse vous aider. Il y a aussi le [forum utilisateur][users]
et [Stack Overflow][stackoverflow].

[irc]: irc://irc.mozilla.org/#rust
[mibbit]: http://chat.mibbit.com/?server=irc.mozilla.org&channel=%23rust
[users]: https://users.rust-lang.org/
[stackoverflow]: http://stackoverflow.com/questions/tagged/rust
