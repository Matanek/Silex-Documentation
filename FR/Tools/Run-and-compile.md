# Exécuter, interpréter ou compiler un programme

Utilisez `silex run` pendant le développement courant. La commande compile la
source pour la machine hôte, lance l'exécutable obtenu et transmet son code de
sortie au terminal.

```sh
silex run Project
```

Le dossier doit contenir directement un seul fichier `.sx` qui déclare une
fonction `main` de premier niveau. Le nom du fichier est libre : `Main.sx`
n'est qu'une convention. Depuis le dossier de l'application, omettez le
chemin :

```sh
cd Project
silex run
```

Vous pouvez toujours sélectionner explicitement n'importe quel fichier `.sx` :

```sh
silex run Project/Main.sx
```

Cette forme explicite reste disponible lorsque plusieurs fichiers du même
dossier déclarent `main`. La découverte par dossier ne parcourt pas les
sous-dossiers et échoue plutôt que de choisir arbitrairement entre plusieurs
entrées.

## Choisir entre les trois commandes

| Intention | Commande |
| --- | --- |
| Compiler puis lancer sur la machine actuelle | `silex run [Source.sx\|Directory]` |
| Exécuter avec l'interpréteur de référence | `silex interpret Source.sx` |
| Produire un exécutable à un emplacement choisi | `silex compile Source.sx -o Application` |

`silex interpret` exécute l'IR portable sans produire d'exécutable natif. Il
est utile pour comparer le comportement de référence avec celui du backend,
mais il ne peut pas appeler la plupart des frontières système fournies par les
packages. Dans ce cas, utilisez `silex run`.

`silex compile` écrit seulement le programme demandé. Il ne le lance pas :

```sh
silex compile Project/Main.sx --output Application
./Application
```

Sous Windows, choisissez normalement un nom qui se termine par `.exe` :

```powershell
silex compile Project/Main.sx --output Application.exe
.\Application.exe
```

## Choisir le backend de compilation

`run`, `compile` et `test` emploient le backend natif par défaut sur tous les
hôtes pris en charge. Ce choix ne demande pas LLVM.

Préparez une fois les outils complémentaires vérifiés après l'installation ou
une mise à jour de Silex :

```sh
silex setup
```

Vous pouvez rendre le choix natif explicite :

```sh
silex run Project/Main.sx --backend native
silex compile Project/Main.sx --backend native -o Application
```

Utilisez `--backend llvm` pour demander LLVM explicitement. LLVM 21.1.8 est
actuellement qualifié seulement sur l'hôte `macos-arm64` avec macOS 14 ou
ultérieur. Sur les autres hôtes distribués, une demande LLVM échoue sans repli
silencieux.

Les deux backends reçoivent le même programme Silex et la même IR portable. Le
choix concerne la production de l'exécutable, pas la syntaxe ou la sémantique du
langage. `silex interpret` reste une voie de référence distincte et n'accepte
pas `--backend`.

## Compiler en Release ou en Debug

`run` utilise le mode Debug par défaut ; `compile` utilise Release. Le mode
Release applique les optimisations du compilateur sans modifier les règles de
sécurité du langage.

Sélectionnez explicitement Release pour exécuter un programme optimisé, ou
Debug pour diagnostiquer le code produit par `compile` :

```sh
silex run Project/Main.sx --release
silex compile Project/Main.sx --debug -o Application
```

Les formes courtes sont `-d` pour Debug et `-r` pour Release. Les deux modes ne
peuvent pas être demandés dans une même commande.

Lorsqu'un programme compilé est interrompu par un signal, Silex conserve
l'exécutable fautif et affiche une commande de reproduction en Debug ainsi que
la commande du débogueur disponible sur l'hôte.

## Choisir une cible de compilation

Sans `--target`, `silex compile` sélectionne la machine actuelle. Affichez les
cibles reconnues par la version installée avec :

```sh
silex targets
```

Silex 0.44 reconnaît `macos-arm64`, `macos-x64`, `linux-arm64`, `linux-x64`,
`windows-arm64` et `windows-x64`. Passez le nom voulu à la compilation
croisée :

```sh
silex compile Project/Main.sx \
    --target linux-x64 \
    --output Application-linux-x64
```

Les six noms possèdent une distribution native, mais cela ne garantit pas que
chaque hôte puisse produire les cinq autres formats. Consultez les
[limites de Silex 0.44](../Reference/Current-limits.md) avant de distribuer un
exécutable construit pour un autre système.

## Observer l'IR ou ignorer le cache

`run` et `interpret` acceptent `--emit-ir` pour écrire l'IR textuelle avant la
sortie du programme. `test` possède aussi cette option. Cette représentation
sert au diagnostic du compilateur ; son format n'est pas une API stable.

Les commandes de compilation utilisent normalement le cache conservé sous
`.silex`. Ajoutez `--nocache`, ou sa forme courte `-n`, seulement pour forcer
une compilation indépendante du cache :

```sh
silex run Project/Main.sx --nocache
```

Le [premier programme](../Learn/First-program.md#comprendre-le-dossier-silex)
explique où ce dossier est créé et comment éviter de le disperser.

[Revenir aux outils](README.md) · [Écrire et lancer des tests](Tests.md) ·
[Consulter la référence de la CLI](CLI-reference.md)
