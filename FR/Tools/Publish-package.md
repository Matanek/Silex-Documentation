# Publier le contenu local d'un package

Le candidat de publication directe peut figer puis publier le contenu présent
dans un dossier local. Le dossier n'a pas besoin d'être un dépôt Git, et les
fichiers n'ont besoin d'être ni commités ni tagués.

Ce parcours est implémenté et testé contre le serveur de qualification, mais
n'est pas encore livré dans Silex ni déployé sur le registre public. L'ancien
parcours `silex register`, fondé sur GitHub et les tags Git, reste inchangé
pendant cette qualification.

## Préparer le package

Le dossier doit contenir un `Package.json` installable avec un nom, une version
et une exigence `requires.silex`. Par exemple :

```json
{
  "name": "MonPackage",
  "version": "1.0.0",
  "requires": {
    "silex": ">=0.44.0"
  }
}
```

Le champ `sources` choisit la racine portable ; sa valeur par défaut est
`Module`. Silex ajoute les sources présentes sous toutes les variantes
`Platform` et `Target`, pas seulement celles de la machine qui publie. Il
analyse ces sources pour retrouver les fichiers lus par `embed_text` et
`embed_bytes`, y compris quand leur chemin vient d'un `let` connu à la
compilation.

L'instantané contient :

- `Package.json` ;
- les sources portables, de plateforme et de cible ;
- les ressources embarquées par ces sources ;
- les fichiers racine présents parmi `README.md`, `README`, `LICENSE`,
  `LICENSE.md` et `NOTICE`.

Les dossiers `.git` et `.silex`, les fichiers non sélectionnés et les artefacts
déclarés ne vont pas dans l'archive source. Un fichier sélectionné absent,
modifié pendant sa lecture, placé derrière un lien symbolique ou possédant
plusieurs liens physiques arrête la préparation. Une ressource demandée par une
source locale ne peut pas sortir du dossier du package.

Chaque artefact déclaré pour `macos-arm64`, `macos-x64`, `linux-arm64`,
`linux-x64`, `windows-arm64` ou `windows-x64` doit déjà être présent à son
chemin local et correspondre au `sha256` du manifeste. Silex le fige comme un
objet distinct au lieu de le télécharger pendant la publication ou de le
recopier dans l'archive source. Si un artefact manque, exécutez d'abord
`silex install <package-directory>` pour préparer le package.

Tous les chemins publiés doivent être en forme Unicode NFC. Silex refuse aussi
les collisions après conversion en minuscules Unicode, y compris entre un
fichier source et la destination d'un artefact. Deux cibles natives peuvent en
revanche employer la même destination, car une installation n'en sélectionne
qu'une.

## Examiner l'instantané sans se connecter

Lancez d'abord :

```sh
silex publish MonPackage --dry-run
```

La commande compile et analyse les sources, copie leurs octets une seule fois,
construit l'archive déterministe et affiche pour chaque fichier inclus son
chemin, sa taille et son SHA-256. Chaque fichier exclu est affiché avec sa
raison. Les artefacts sont annoncés avec leur cible, leur nom, leur destination,
leur taille et leur empreinte. La commande termine par la taille et l'empreinte
de l'archive, puis l'empreinte de publication.

Cette simulation ne lit aucun accès au registre et n'effectue aucune requête
réseau. Une modification apportée au dossier après la copie ne change donc pas
l'instantané affiché. Relancer la commande après une modification produit un
nouvel instantané et, si les octets changent, une nouvelle empreinte.

Le serveur de qualification réapplique les contrôles d'admission, les limites
et les empreintes avant de rendre la version visible. Un succès local ne permet
donc pas d'ignorer un refus précis du serveur, notamment si son état a changé
depuis la simulation.

## Se connecter puis publier

La publication réutilise l'accès privé créé par le
[parcours GitHub du registre](Registry-login.md). Connectez-vous si nécessaire :

```sh
silex login
silex publish MonPackage
```

Le CLI prépare exactement le même type d'instantané que `--dry-run`, puis le
registre vérifie l'identité GitHub, les droits sur le nom, le manifeste et les
limites de contenu. Le jeton GitHub ne passe jamais dans cette commande : elle
envoie seulement l'accès temporaire propre au registre.

Une publication réussie affiche le nom, la version, l'identifiant de
publication et son SHA-256. Le registre rend alors la version immuable et
visible aux lectures publiques. Réutiliser la même version avec un contenu
différent est refusé ; republier le même contenu retrouve le résultat existant.

## Reprendre après une interruption

Le serveur conserve l'offset durable de chaque objet. Si une réponse est
perdue après l'écriture d'un segment, relancez simplement la même commande :

```sh
silex publish MonPackage
```

Le CLI recrée l'instantané depuis le dossier local. Si son empreinte est restée
identique, le registre retrouve la tentative, indique les objets et offsets
déjà reçus, puis le transfert reprend sans concaténer le segment à l'aveugle.
Une relance après finalisation annonce que la version est déjà publiée.

Si le contenu local a changé entre les deux commandes, son empreinte change et
il ne peut pas reprendre la tentative précédente. Remettez le dossier dans
l'état voulu ou examinez le nouvel instantané avec `--dry-run` avant de publier.

## Connaître les limites du candidat

La publication directe reste une fonctionnalité de qualification :

- le registre public ne sert pas encore ce protocole ;
- aucune version de production, migration de nom ou installation depuis ce
  nouveau registre n'est annoncée par cette page.

Pour le registre actuellement livré, continuez à utiliser `silex register`, un
dépôt GitHub propre et des tags `vMAJOR.MINOR.PATCH`. Ne mélangez pas les
autorisations : l'ancien enregistrement de dépôt et le nouvel accès d'identité
du registre sont distincts.

[Revenir aux outils](README.md) ·
[Définir le manifeste](Package-manifest.md) ·
[Se connecter au registre avec GitHub](Registry-login.md)
