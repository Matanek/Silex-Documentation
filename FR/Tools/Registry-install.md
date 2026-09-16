# Installer depuis le registre v2 en qualification

Le candidat `silex install` lit désormais les publications v2 sans connexion
GitHub. Il n'est pas encore livré dans Silex ni déployé sur le registre public.
Le parcours ci-dessous décrit le candidat et son banc de test local ; la
distribution actuelle continue d'utiliser le [registre Git existant](Install-packages.md).

## Installer un package pour un projet

Déclarez d'abord la dépendance dans le `Package.json` du projet. Dans le banc
de qualification, placez-vous dans ce projet et installez la version voulue :

```sh
silex install MonPackage@1.0.0
```

Le banc active le lecteur v2 avec `SILEX_REGISTRY_V2=test` et lui fournit son
serveur sur `127.0.0.1`. Ces variables sont réservées aux essais de la Spec ;
la commande ci-dessus n'installe pas aujourd'hui `MonPackage` depuis un service
v2 public. Hors de ce mode explicite, `silex install` conserve le parcours
actuel du registre Git. Le mode `public` pointe vers le futur domaine officiel,
mais ne signifie pas que le service v2 y est déjà disponible.

Le client lit la liste des versions, vérifie le hash du descripteur, l'archive
source et chacun des fichiers qu'elle contient. Il installe les dépendances
transitives, puis télécharge uniquement les artefacts déclarés pour la cible
choisie (`--target` permet de la préciser). Chaque package n'apparaît dans le
magasin qu'après la vérification de sa source et de ses artefacts. Une archive
ou un artefact altéré est refusé sans publier de dossier installé. Ce parcours
ne lit pas le fichier de connexion au registre et n'appelle pas Git.
Pendant cette qualification, utilisez une racine `SILEX_DATA_ROOT` distincte
pour chaque cible : une même version déjà installée avec les artefacts d'une
autre cible est refusée, et non complétée dans le magasin existant.

## Garder les versions du projet stables

L'installation lancée depuis un dossier possédant `Package.json` écrit
`Silex.lock.json` auprès du manifeste. Ce fichier conserve pour toute la
fermeture les versions, empreintes de publication, source et manifeste, arêtes
de dépendances et déclarations d'artefacts de toutes les cibles. Committez-le
avec le projet. Une nouvelle version installée ailleurs dans le magasin ne
change pas le graphe de ce projet.

Pour choisir volontairement une autre version, lancez de nouveau
`silex install Nom@VERSION` depuis le projet. La contrainte du `Package.json` doit
l'accepter. Si vous modifiez le manifeste du projet, réinstallez depuis ce
dossier pour actualiser le verrou avant de compiler ; un verrou qui ne
correspond plus au manifeste est refusé. Une installation lancée hors d'un
projet installe globalement la fermeture demandée, mais n'écrit pas de verrou
de projet.

Un lien de développement créé avec `silex link` pour ce workspace remplace
intentionnellement le package correspondant pendant la résolution.
`silex packages resolve` affiche alors `workspace-link`. Le lien ne réécrit pas le
verrou ; après `silex unlink`, la version verrouillée est reprise. Les options
`--suite` et `--dev` restent disponibles dans le mode v2 : la première ajoute
les membres compatibles d'une suite enregistrée, la seconde les dépendances
de développement du package demandé.

## Isoler un essai

`SILEX_DATA_ROOT` peut désigner un dossier absolu dédié. Le candidat y place
les packages, le cache, les outils et les accès locaux au lieu du dossier
`~/.silex` (ou `%USERPROFILE%\.silex` sous Windows). Cette variable évite
qu'un essai ne dépende des packages ou des accès du compte habituel. Le banc
exige en plus un serveur local et un dossier de test sous `TestState` ; ne
réutilisez pas ces variables de test comme configuration de production.

Le banc de Task‑04 publie deux packages hors Git, installe leur fermeture
dans une racine vide, vérifie l'artefact de la cible et exécute un consommateur
qui affiche `42`. Il couvre aussi les objets corrompus, un état d'installation
interrompue, l'apparition d'une nouvelle version, l'actualisation explicite du
verrou, les liens locaux, une suite et les dépendances de développement. Ces
preuves locales ne qualifient pas encore le service déployé ni l'exécution
native du client sur toutes les plateformes.

[Revenir à l'installation des packages](Install-packages.md) ·
[Se connecter pour publier](Registry-login.md)
