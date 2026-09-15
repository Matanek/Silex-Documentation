# Se connecter au registre avec GitHub

Ce parcours est implémenté dans le candidat de publication de packages, mais
n'est pas encore livré dans Silex ni déployé sur le registre public. Ses tests
locaux sont complétés par un parcours GitHub réel réussi, jusqu'à la révocation
de l'accès. Le serveur demande un scope vide et refuse tout scope supplémentaire.
Les commandes ci-dessous décrivent ce candidat, pas une fonctionnalité déjà
disponible dans la distribution publique.

## Autoriser la connexion

Sur un environnement de qualification configuré, lancez :

```sh
silex login
```

Le terminal affiche l'adresse `https://github.com/login/device` et un code à
saisir sur cette page. Il tente aussi d'ouvrir le navigateur. Pour ouvrir la
page vous-même, notamment dans un terminal distant, utilisez :

```sh
silex login --no-browser
```

Vérifiez le nom de l'application dédiée à l'identité du registre et les
permissions affichées par GitHub avant de consentir. Ce parcours ne demande
aucune permission de dépôt ni accès à votre e-mail privé. Refusez une demande
qui en réclame. La connexion personnelle et le consentement se font sur GitHub,
jamais en donnant votre mot de passe au terminal.

Après votre autorisation, le registre vérifie votre identité auprès de GitHub
et crée automatiquement son enregistrement minimal. Aucun compte Silex sur
invitation n'est nécessaire. Le terminal annonce le pseudo connecté ; le
registre rattache les droits à l'identifiant GitHub stable, pas à ce pseudo.
Un changement de pseudo ne change donc pas le propriétaire des droits.

## Comprendre l'accès conservé

Le CLI conserve seulement un accès au registre, valable au plus 24 heures,
avec l'identifiant GitHub, le pseudo et la date d'expiration. Il ne reçoit ni
ne conserve le jeton GitHub utilisé par le serveur pour vérifier l'identité.
Ne partagez pas le fichier d'accès et ne le commitez pas.

Sur macOS et Linux, ce fichier est `~/.silex/auth/registry.json`. Le dossier
doit être privé et le fichier lisible et modifiable uniquement par son
propriétaire. Un dossier trop permissif ou un lien symbolique à la place du
dossier ou du fichier est refusé.

Sur Windows, le candidat chiffre l'accès avec DPAPI, lié au compte Windows,
dans `%USERPROFILE%\.silex\auth\registry.dpapi`. Il n'enregistre pas de copie
en clair et refuse un fichier altéré ou impossible à déchiffrer. Cette protection
ne couvre pas un compte utilisateur compromis.

Le stockage est testé sur macOS ARM64. L'exécution native sur les autres cibles,
notamment Linux et Windows, reste à qualifier ; une compilation réussie ne suffit
pas à annoncer leur prise en charge.

Si un accès reste valide, `silex login` annonce la connexion existante sans la
remplacer. Pour utiliser un autre compte GitHub, déconnectez-vous d'abord.
L'installation publique n'a pas besoin de cette connexion.

## Se déconnecter

```sh
silex logout
```

La commande révoque l'accès auprès du registre, puis supprime le fichier local.
Elle ne révoque pas les accès d'autres machines et ne déconnecte pas votre
navigateur de GitHub. Sans accès local, elle réussit également.

Si le registre est inaccessible, la commande échoue et conserve le fichier
pour permettre de réessayer. Supprimer ce fichier à la main ne révoquerait pas
l'accès serveur : celui-ci resterait valable jusqu'à son expiration.

## Reprendre après un échec

Une autorisation refusée ou expirée n'enregistre aucun accès local. Relancez
`silex login` pour obtenir une nouvelle tentative. Une tentative interrompue
ou déjà consommée n'émet pas une seconde fois le même accès ; si la réponse
finale a été perdue, recommencez la connexion. L'accès éventuellement émis mais
non reçu expire de lui-même.

Les requêtes réseau sont limitées à 25 secondes chacune et l'attente de
consentement est bornée. Le CLI respecte le délai de sondage demandé par le
serveur. Une autre connexion ou déconnexion en cours sur le même stockage
est refusée plutôt que d'écraser son état.

Ce parcours est distinct de l'ancien `silex register`, qui prépare une pull
request et demande des droits GitHub de dépôt. Il ne réutilise pas cette
autorisation. La publication directe du contenu local reste une étape suivante
du candidat ; `login` seul ne publie aucun package.

[Revenir aux outils](README.md) · [Consulter la référence de la CLI](CLI-reference.md)
