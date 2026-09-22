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
nouvelle distribution du même système et de la même architecture, vérifie sa
somme SHA-256, puis remplace l'exécutable. Un Silex ARM64 n'est donc pas
remplacé par une archive x64. Sous Windows, le remplacement se termine en
arrière-plan après l'arrêt de la commande afin que le fichier puisse être
libéré.

La commande `update` vise toujours la dernière version publiée. Une variable
`SILEX_VERSION` laissée dans le terminal ne bloque pas cette mise à jour
explicite.

## Installer les outils complémentaires

Après l'installation ou une mise à jour, préparez une fois la chaîne vérifiée :

```sh
silex setup
```

Silex installe les outils adaptés à l'hôte sous le dossier utilisateur
`.silex/toolchain`. Sur macOS ARM64, cela comprend LLVM 21.1.8 pour le backend
LLVM explicite. La commande installe aussi Shadercross pour les shaders HLSL
et le linker privé nécessaire aux frontières natives. Une nouvelle
exécution ne télécharge pas les outils déjà présents et valides. Ils restent
des détails du compilateur, pas des dépendances à déclarer dans l'application.

Le backend natif par défaut reste utilisable sans LLVM. `--backend native`
permet de rendre ce choix explicite ; la construction de Silex et ce backend
ne chargent pas les outils LLVM.

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
