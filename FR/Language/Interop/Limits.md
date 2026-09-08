# Connaître la frontière d'interopérabilité actuelle

La surface Silex 0.44 est volontairement limitée aux adaptateurs privés de
packages.

## Cibles et fournisseurs

Les tranches STD implémentées composent `macos-arm64`, `macos-x64`,
`linux-arm64`, `linux-x64`, `windows-arm64` et `windows-x64`. Un fournisseur
`Boundary.<Provider>` est sélectionné par cible et peut réunir archive,
bibliothèques système nommées et frameworks Apple. Les fournisseurs historiques
de la toolchain restent admis pour compatibilité.

Les capacités implémentées couvrent les appels C nommés ou par adresse, les
graines aléatoires, horloges monotones et civiles locales, entrées-sorties
d'octets, terminaux, fichiers, métadonnées et sous-processus, système de
fichiers, sockets, résolution de noms, threads système et messages Objective-C
typés nécessaires au WebView système de macOS.

Sous Windows, les bindings de console couvrent les octets UCRT, modes de
console, pages d'entrée UTF-8, attentes de handles et dimensions du tampon
d'écran. Leurs imports PE et leur comportement sont vérifiés nativement sur
X64 et ARM64.

macOS utilise les façades fixes `__open` et `__ioctl` lorsque les fonctions C
publiques sont variadiques sous l'ABI Apple ARM64.

## Exécution et pointeurs

`silex run` construit et exécute la cible native de l'hôte ; une frontière de
plateforme fonctionne donc sans compilation manuelle séparée. Le chemin de
référence explicite `silex interpret` émule seulement `arc4random` et refuse les
autres frontières.

Les sources de pointeurs admises sont les octets UTF-8 d'un `str` en lecture,
un tampon chaîne privé modifiable pour une sortie système en octets, et le
stockage stable d'un scalaire ou tableau fixe pour `C.MutablePointer<T>`.

## Capacités absentes

Les pointeurs conservés généraux, callbacks capturants, structures C de premier
rang, appels variadiques, chemins arbitraires de bibliothèques et fournisseurs
étrangers publics ne sont pas implémentés. Les callbacks nommés avec contexte
de classe opaque existent pour les adaptateurs de threads.

Une structure C brute reste un stockage scalaire fixe et contigu, privé à un
module de plateforme et accompagné de sa disposition explicite. La matrice de
distribution vérifie les adaptateurs système sur les six hôtes, sans rendre
leurs ABI ou dispositions accessibles aux applications.

## Masquer la plateforme derrière une API Silex

Le package propriétaire importe `Interop.Boundary` et nomme le fournisseur de
son manifeste actif. Une cible sans déclaration correspondante produit le
diagnostic de frontière manquante ; le source ne bascule jamais vers `MacOS`,
`Linux` ou `Windows`.

Un module portable et son fragment de plateforme peuvent composer une API
commune. `STD.Randomizer`, par exemple, garde son algorithme dans le fragment
portable et reçoit `Platform.system_seed()` d'un fragment privé. Les appelants
manipulent `Randomizer`, jamais `arc4random`, `getrandom` ou `ProcessPrng`.

Le qualificateur `Platform` identifie l'origine physique sans créer de module
public ni demander d'import.

[Revenir à l'interopérabilité](README.md) ·
[Composer des fragments ciblés](../Modules/Fragments.md)
