# Publier un package dans le registre

Depuis Silex 0.45.0, `silex publish` envoie un instantané du dossier local au
registre Cloudflare sur `registry.silex-lang.org`. `--dry-run` permet de préparer
la publication sans connexion. Le registre conserve les sources et les
artefacts déclarés ; une
installation ultérieure n'a besoin ni du dépôt GitHub du package ni de votre
machine. Vous pouvez publier depuis un dossier sans dépôt Git.

## Préparer et prévisualiser le package

Placez `Package.json` dans un dossier portant le nom du package. Un manifeste
minimal pour le client actuel ressemble à ceci :

```json
{
  "name": "MonPackage",
  "version": "1.0.0",
  "requires": {
    "silex": ">=0.44.0"
  }
}
```

Ajoutez les sources dans `Module/`, ou choisissez un autre dossier avec
`sources`. Pour voir exactement ce qui partirait, lancez depuis le dossier
parent :

```sh
silex publish MonPackage --dry-run
```

L'aperçu nomme les fichiers source inclus, les artefacts transmis séparément et
les éléments exclus. Il donne le SHA-256 de l'instantané complet. Il ne demande
pas de connexion, ne contacte pas le registre et ne publie rien. Corrigez tout
fichier manquant ou toute exclusion inattendue avant l'envoi. Le détail des
champs figure dans [Définir un package avec `Package.json`](Package-manifest.md).

Un `repository` facultatif peut indiquer aux contributeurs où participer au
développement :

```json
{
  "repository": "https://github.com/exemple/MonPackage"
}
```

Cette adresse est un lien fourni par l'auteur. Elle n'est pas la destination de
la publication, ne détermine pas les octets envoyés et n'accorde aucun droit
sur le nom. Aucun commit, tag ou push n'est créé par `silex publish`.

## Se connecter comme auteur

Lancez une fois :

```sh
silex login
```

Silex affiche une adresse GitHub et un code temporaire, puis tente d'ouvrir le
navigateur. Autorisez l'application d'identité dédiée au registre. Elle demande
votre identité GitHub, sans permission de lecture ou d'écriture sur vos dépôts.
Avec `silex login --no-browser`, ouvrez vous-même l'adresse indiquée dans le
terminal.

Le registre attache les noms à votre identifiant GitHub stable ; votre pseudo
peut changer sans transférer ces droits. Il conserve cet identifiant, le pseudo
utile à l'attribution, les droits sur les noms et une empreinte de l'accès du
client. Le client conserve son accès localement ; sous Windows, ce secret est
protégé par DPAPI. L'accès expire au plus tard après 24 heures. `silex logout`
le révoque auprès du registre et supprime la copie locale lorsque le service
est joignable. Révoquer seulement l'application sur GitHub ne garantit pas
l'invalidation immédiate d'un accès Silex déjà émis ; il expire au plus tard
après 24 heures. Reconnectez-vous si nécessaire.

Une panne de GitHub peut empêcher une nouvelle connexion. Les accès Silex déjà
émis restent utilisables jusqu'à leur expiration ou leur révocation, et les
packages déjà publiés restent installables sans connexion.

## Envoyer la version

Une fois l'aperçu vérifié et la connexion établie :

```sh
silex publish MonPackage
```

La première version réserve automatiquement le nom pour votre identité. Les
versions suivantes empruntent le même chemin. Le CLI annonce la réussite quand
tous les objets sont conservés et que la version est visible. Si la réponse se
perd, relancer la même publication reprend ou retrouve son état ; une version
déjà publiée ne peut pas être remplacée par d'autres octets.

Vérifiez la lecture publique depuis un autre magasin local :

```sh
silex install MonPackage@1.0.0
```

L'installation est anonyme. Pour publier `1.1.0`, changez `version` dans le
manifeste, vérifiez le nouvel aperçu, puis relancez `silex publish`. Un ancien
client qui attend l'inscription par pull request et les tags Git utilise
l'ancien protocole ; il ne sait pas lire les versions conservées par ce
registre. Installez Silex 0.45.0 ou une version plus récente.

[Revenir aux outils](README.md) ·
[Développer avec des packages locaux](Develop-packages.md)
