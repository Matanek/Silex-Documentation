# Publier un package dans le registre

Le registre associe une seule fois le nom public d'un package à son dépôt Git.
Il ne reçoit ni ses sources ni ses versions : chaque version publiée reste un
commit du dépôt identifié par un tag `vMAJOR.MINOR.PATCH`.

## Préparer le dépôt du package

Avant la première publication, vérifiez ces points :

- le nom du dossier correspond au champ `name` de `Package.json` ;
- le manifeste déclare une version et sa compatibilité avec Silex ;
- le dépôt GitHub possède un remote `origin` canonique ;
- tous les fichiers destinés à la version sont commités et le dépôt est propre.

Un manifeste minimal possède notamment cette identité :

```json
{
  "name": "MonPackage",
  "version": "1.0.0",
  "requires": {
    "silex": ">=0.42.0"
  }
}
```

Les métadonnées, dépendances, permissions, frontières et artefacts disponibles
sont regroupés dans
[Définir un package avec `Package.json`](Package-manifest.md).

## Vérifier la version sans rien modifier

Depuis le dossier qui contient le package, lancez :

```sh
silex check MonPackage
```

Silex valide le manifeste et annonce le tag attendu :

```text
silex: package MonPackage@1.0.0 is valid; its release tag is v1.0.0
```

Cette vérification est facultative, mais elle permet de corriger le contrat du
package avant de créer un tag public.

## Inscrire le package une seule fois

Demandez ensuite l'inscription du nom et du dépôt :

```sh
silex register MonPackage
```

Lors de la première utilisation, Silex demande une autorisation GitHub par
code d'appareil. La commande prépare automatiquement la proposition, crée si
nécessaire un fork du registre et ouvre une pull request. Son résultat contient
l'adresse de cette pull request afin que vous puissiez suivre sa validation.

L'inscription devient immuable après sa fusion : elle contient seulement le
nom du package et l'URL canonique de son dépôt. Une nouvelle version ne demande
jamais une nouvelle pull request dans le registre.

## Publier la version avec un tag Git

Poussez d'abord le commit complet de la version vers le dépôt canonique. Créez
ensuite un tag qui correspond exactement au champ `version` du manifeste :

```sh
git tag -a v1.0.0 -m "MonPackage 1.0.0"
git push origin v1.0.0
```

Le registre découvre les versions en lisant les tags `vMAJOR.MINOR.PATCH`. Le
`Package.json` présent dans le commit tagué doit conserver le même nom et la
même version que le tag.

Une fois la première inscription acceptée, vous pouvez vérifier le parcours
public avec :

```sh
silex install MonPackage@1.0.0
```

Pour publier `1.1.0`, modifiez le manifeste, validez et commitez la nouvelle
version, puis poussez seulement le tag `v1.1.0`. L'inscription initiale reste
inchangée.

Le [contrat du registre](https://github.com/Matanek/Silex-Registry/blob/main/CONTRIBUTING.md)
détaille les règles d'identité, de transfert et de révocation.

[Revenir aux outils](README.md) ·
[Développer avec des packages locaux](Develop-packages.md)
