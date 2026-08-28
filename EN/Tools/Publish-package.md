# Publish a package in the registry

The registry associates a package's public name with its Git repository once.
It receives neither sources nor versions: every published version remains a
repository commit identified by a `vMAJOR.MINOR.PATCH` tag.

## Prepare the package repository

Before the first publication, verify these points:

- the folder name matches the `name` field in `Package.json`;
- the manifest declares a version and its Silex compatibility;
- the GitHub repository has a canonical `origin` remote;
- all files intended for the version are committed and the repository is
  clean.

A minimal manifest includes this identity:

```json
{
  "name": "MyPackage",
  "version": "1.0.0",
  "requires": {
    "silex": ">=0.42.0"
  }
}
```

## Check the version without modifying anything

From the folder containing the package, run:

```sh
silex check MyPackage
```

Silex validates the manifest and announces the expected tag:

```text
silex: package MyPackage@1.0.0 is valid; its release tag is v1.0.0
```

This check is optional, but lets you correct the package contract before
creating a public tag.

## Register the package once

Then request registration of the name and repository:

```sh
silex register MyPackage
```

On first use, Silex requests GitHub authorization with a device code. The
command prepares the proposal automatically, creates a registry fork when
needed, and opens a pull request. Its result includes the pull request address
so you can follow its validation.

Registration becomes immutable after merging: it contains only the package
name and canonical repository URL. A new version never requires a new registry
pull request.

## Publish the version with a Git tag

First push the complete version commit to the canonical repository. Then create
a tag exactly matching the manifest's `version` field:

```sh
git tag -a v1.0.0 -m "MyPackage 1.0.0"
git push origin v1.0.0
```

The registry discovers versions by reading `vMAJOR.MINOR.PATCH` tags. The
`Package.json` in the tagged commit must retain the same name and version as
the tag.

After the first registration is accepted, verify the public path with:

```sh
silex install MyPackage@1.0.0
```

To publish `1.1.0`, update the manifest, validate and commit the new version,
then push only the `v1.1.0` tag. The initial registration remains unchanged.

The [registry contract](https://github.com/Matanek/Silex-Registry/blob/main/CONTRIBUTING.md)
details identity, transfer, and revocation rules.

[Back to the tools](README.md) ·
[Develop with local packages](Develop-packages.md)
