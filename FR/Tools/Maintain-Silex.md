# Mettre à jour et entretenir Silex

Affichez la version effectivement trouvée dans votre `PATH` avant de diagnostiquer
une différence de comportement :

```sh
silex version
```

`silex --version` et `silex -V` produisent la même information. Pour connaître
la cible de la machine et les autres cibles reconnues, lancez :

```sh
silex targets
```

## Mettre le compilateur à jour

Remplacez une installation officielle par la dernière version publiée avec :

```sh
silex update
```

Silex conserve le dossier du binaire en cours d'exécution. Il télécharge la
nouvelle distribution, vérifie sa somme SHA-256, puis remplace l'exécutable.
Sous Windows, le remplacement se termine en arrière-plan après l'arrêt de la
commande afin que le fichier puisse être libéré.

La commande `update` vise toujours la dernière version publiée. Une variable
`SILEX_VERSION` laissée dans le terminal ne bloque pas cette mise à jour
explicite.

## Installer les outils complémentaires

Les programmes sans frontière native fonctionnent après l'installation du
seul exécutable Silex. Avant de compiler des shaders HLSL ou d'utiliser un
package qui demande une liaison native, préparez une fois la chaîne vérifiée :

```sh
silex setup
```

Silex installe les outils adaptés à l'hôte sous le dossier utilisateur
`.silex/toolchain`. Une nouvelle exécution ne télécharge pas les outils déjà
présents et valides. Ils restent des détails du compilateur, pas des
dépendances à déclarer dans l'application.

## Distinguer les deux dossiers `.silex`

| Emplacement | Contenu principal |
| --- | --- |
| `.silex/` dans le dossier de travail | cache de compilation, exécutables privés de `run` et `test`, liens du workspace |
| `~/.silex/` pour l'utilisateur | packages installés, liens utilisateur, outils du compilateur, cache du registre |

Sous Windows, le dossier utilisateur est `%USERPROFILE%\.silex`. Supprimer le
`.silex` d'un projet force seulement la régénération de ses artefacts et retire
ses liens de workspace. Les packages et outils globaux restent séparés.

## Retirer Silex

Supprimez l'exécutable situé dans le dossier choisi pendant l'installation :
`~/.local/bin/silex` par défaut sur macOS et Linux, ou
`%LOCALAPPDATA%\Silex\bin\silex.exe` sous Windows.

Le dossier utilisateur `.silex` n'est pas supprimé automatiquement. Vous
pouvez le conserver pour une réinstallation ultérieure. Ne le supprimez que si
vous voulez également effacer les packages, liens, outils et caches de votre
compte.

[Revenir aux outils](README.md) · [Installer Silex](Installation.md) ·
[Consulter la référence de la CLI](CLI-reference.md)
