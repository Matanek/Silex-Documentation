# Develop with local packages

A development link tells Silex to read a local package's sources directly.
Package changes are then visible at the next compilation without another
installation.

From a stable working root, link the package folder:

```sh
silex link UserPackages/MyPackage
```

The command reads and validates its `Package.json`, prepares any artifacts for
the current target, then records a user link. The name subsequently used by
manifests and imports remains the one declared by the package, not the folder
path.

## Limit the link to one workspace

A user link affects every compatible project. To test a checkout in only one
workspace, specify its root:

```sh
silex link UserPackages/MyPackage --workspace Project
```

Silex stores this link under `Project/.silex/links`. When resolving a source,
it searches for the nearest `.silex/links` folder among the project and its
parents.

A workspace link takes precedence over a user link of the same name. A user
link in turn takes precedence over installed versions. In every case, the
linked version must still satisfy the constraint declared by the project.

## Remove a link

Remove a user link with the package name:

```sh
silex unlink MyPackage
```

To remove the workspace-specific link, repeat its exact scope:

```sh
silex unlink MyPackage --workspace Project
```

Removing a link deletes neither the source folder nor installed versions. On
the next graph resolution, Silex may therefore select the compatible installed
version again.

## Check which package was selected

Display known global packages with:

```sh
silex packages
```

Then inspect resolution for a specific project:

```sh
silex packages resolve Project/Main.sx
```

The `workspace-link`, `user-link`, or `installed` origin immediately explains
why that path was selected.

## Prepare a different target

A package containing a native boundary may require target-specific
preparation. Add its name when creating the link:

```sh
silex link UserPackages/MyPackage --target linux-x64
```

This option does not make the link target-specific: it selects the target used
to validate and prepare the package when the command runs.

Before publishing, use `silex check` to validate the installable contract
without modifying the repository. The complete workflow is described in
[Publish a package in the registry](Publish-package.md).

[Back to the tools](README.md) ·
[Install and select packages](Install-packages.md) ·
[Read the CLI reference](CLI-reference.md)
