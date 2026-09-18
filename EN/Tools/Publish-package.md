# Publish a package in the registry

As of Silex 0.45.0, `silex publish` sends a snapshot of a local directory to
the Cloudflare registry at `registry.silex-lang.org`. `--dry-run` previews the
publication without connecting to the service. The registry stores the sources
and declared artifacts.
Installing a published version needs neither the package's GitHub repository
nor the author's machine. A package can be published without a Git repository.

## Prepare and preview the package

Place `Package.json` in a directory named after the package. A minimal manifest
for the compatible client is:

```json
{
  "name": "MyPackage",
  "version": "1.0.0",
  "requires": { "silex": ">=0.44.0" }
}
```

Put sources in `Module/`, or select another directory with `sources`. From the
parent directory, preview the exact publication:

```sh
silex publish MyPackage --dry-run
```

The preview lists included source files, separately uploaded artifacts, and
excluded files. It gives the SHA-256 digest of the complete snapshot. It does
not authenticate, contact the registry, or publish anything. Check missing
files and unexpected exclusions before uploading. See
[Define a package with `Package.json`](Package-manifest.md) for manifest fields.

The optional `repository` field can point contributors to the development
repository:

```json
{ "repository": "https://github.com/example/MyPackage" }
```

This author-provided link is not the publication destination, does not
determine the uploaded bytes, and grants no ownership of the package name.
`silex publish` creates no Git commit, tag, or push.

## Sign in as an author

Run:

```sh
silex login
```

Silex shows a GitHub address and temporary code, then attempts to open a
browser. Authorize the dedicated registry identity application. It requests
your GitHub identity without read or write permission for repositories. With
`silex login --no-browser`, open the displayed address yourself.

The registry attaches package names to your stable GitHub user ID; changing
your handle does not transfer these rights. It stores that ID, a handle for
attribution, name ownership, and a digest of the client access token. The
client stores its token locally; on Windows, DPAPI protects it. Access expires
within 24 hours. `silex logout` revokes it at the registry and removes the
local copy when the service is reachable. Revoking the GitHub application
alone does not guarantee immediate invalidation of an already issued Silex
token; it expires within 24 hours. Sign in again when needed.

If GitHub is unavailable, new logins may fail. Already issued Silex tokens
remain usable until expiration or revocation, and published packages remain
installable without signing in.

## Upload the version

After reviewing the preview and signing in:

```sh
silex publish MyPackage
```

The first publication reserves the name for your identity. Later versions use
the same command. The CLI reports success once all objects are stored and the
version is visible. If a response is lost, retrying the same publication
resumes or finds its state. A published version cannot be replaced with
different bytes.

Check public retrieval from a separate local package store:

```sh
silex install MyPackage@1.0.0
```

Installation is anonymous. To publish `1.1.0`, change `version` in the
manifest, review the new preview, and run `silex publish` again. An older
client expecting pull-request registration and Git tags uses the former
protocol; it cannot read versions stored by this registry. Install Silex
0.45.0 or a newer version.

[Back to the tools](README.md) ·
[Develop with local packages](Develop-packages.md)
