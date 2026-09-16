# Publish the local contents of a package

The direct-publishing candidate can freeze and publish the contents present in
a local folder. The folder does not need to be a Git repository, and the files
do not need to be committed or tagged.

This workflow is implemented and tested against the qualification server, but
has not yet shipped in Silex or been deployed to the public registry. The older
`silex register` workflow, based on GitHub and Git tags, remains unchanged
during this qualification.

## Prepare the package

The folder must contain an installable `Package.json` with a name, a version,
and a `requires.silex` requirement. For example:

```json
{
  "name": "MyPackage",
  "version": "1.0.0",
  "requires": {
    "silex": ">=0.44.0"
  }
}
```

The `sources` field selects the portable source root; it defaults to `Module`.
Silex adds the sources present under every `Platform` and `Target` variant, not
only those for the publishing machine. It analyzes these sources to find the
files read by `embed_text` and `embed_bytes`, including when their path comes
from a `let` known at compile time.

The snapshot contains:

- `Package.json`;
- portable, platform, and target sources;
- resources embedded by these sources;
- root files present among `README.md`, `README`, `LICENSE`, `LICENSE.md`, and
  `NOTICE`.

The `.git` and `.silex` folders, unselected files, and declared artifacts are
not included in the source archive. Preparation stops if a selected file is
missing, changes while it is read, is behind a symbolic link, or has multiple
hard links. A resource requested by a local source cannot leave the package
folder.

Every artifact declared for `macos-arm64`, `macos-x64`, `linux-arm64`,
`linux-x64`, `windows-arm64`, or `windows-x64` must already exist at its local
path and match the manifest's `sha256`. Silex freezes it as a separate object
instead of downloading it during publication or copying it into the source
archive. If an artifact is missing, first run
`silex install <package-directory>` to prepare the package.

Every published path must be in Unicode NFC form. Silex also rejects
collisions after Unicode lowercase conversion, including between a source file
and an artifact destination. Two native targets may use the same destination,
however, because an installation selects only one of them.

## Inspect the snapshot without signing in

Start with:

```sh
silex publish MyPackage --dry-run
```

The command compiles and analyzes the sources, copies their bytes once, builds
the deterministic archive, and displays each included file with its path,
size, and SHA-256. Every excluded file is displayed with its reason. Artifacts
are reported with their target, name, destination, size, and digest. The
command ends with the archive size and digest, followed by the publication
digest.

This simulation reads no registry access and makes no network request. A
change made to the folder after the copy therefore does not alter the displayed
snapshot. Running the command again after a change produces a new snapshot
and, when the bytes differ, a new digest.

The qualification server reapplies admission checks, limits, and digests
before making the version visible. A local success therefore does not let you
ignore a precise server rejection, particularly when its state has changed
since the simulation.

## Sign in, then publish

Publication reuses the private access created by the
[registry GitHub workflow](Registry-login.md). Sign in when needed:

```sh
silex login
silex publish MyPackage
```

The CLI prepares the same kind of snapshot as `--dry-run`, then the registry
checks the GitHub identity, rights to the name, manifest, and content limits.
The GitHub token never enters this command: it sends only the registry's own
temporary access.

A successful publication displays the name, version, publication identifier,
and its SHA-256. The registry then makes the version immutable and visible to
public reads. Reusing the same version with different content is rejected;
republishing the same content finds the existing result.

## Resume after an interruption

The server retains the durable offset of every object. If a response is lost
after a segment is written, simply run the same command again:

```sh
silex publish MyPackage
```

The CLI rebuilds the snapshot from the local folder. If its digest has remained
identical, the registry finds the attempt, reports the objects and offsets
already received, then resumes the transfer without blindly appending the
segment. Running the command again after finalization reports that the version
is already published.

If the local content changed between the two commands, its digest changes and
it cannot resume the previous attempt. Restore the folder to the intended
state or inspect the new snapshot with `--dry-run` before publishing.

## Know the candidate's limits

Direct publication remains a qualification feature:

- the public registry does not yet serve this protocol;
- this page announces no production version, name migration, or installation
  from this new registry.

For the registry that currently ships, continue to use `silex register`, a
clean GitHub repository, and `vMAJOR.MINOR.PATCH` tags. Do not mix the
authorizations: the older repository registration and the new registry
identity access are separate.

[Back to the tools](README.md) ·
[Define the manifest](Package-manifest.md) ·
[Connect to the registry with GitHub](Registry-login.md)
