# Connect to the registry with GitHub

This workflow is implemented in the package-publishing candidate, but has not
yet shipped with Silex or been deployed to the public registry. Local tests are
complemented by a successful real GitHub flow, through access revocation.
The server requests an empty scope and rejects any additional scope.
The commands below describe this candidate, not a feature already available
in the public distribution.

## Authorize the connection

In a configured qualification environment, run:

```sh
silex login
```

The terminal displays `https://github.com/login/device` and a code to enter
on that page. It also tries to open your browser. To open the page yourself,
particularly in a remote terminal, use:

```sh
silex login --no-browser
```

Check the name of the dedicated registry identity application and the
permissions displayed by GitHub before consenting. This workflow requests
no repository permissions or access to your private email. Reject any request
that asks for them. Sign-in and consent take place on GitHub, never by giving
your password to the terminal.

After authorization, the registry verifies your identity with GitHub and
automatically creates its minimal record. No invitation-only Silex account
is required. The terminal announces the connected username; the registry
associates rights with your stable GitHub ID, not that username.
Changing your username therefore does not change who owns those rights.

## Understand the saved access

The CLI saves only registry access, valid for at most 24 hours, alongside your
GitHub ID, username, and expiration time. It neither receives nor saves the
GitHub token the server uses to verify your identity.
Do not share or commit the credential file.

On macOS and Linux, this file is `~/.silex/auth/registry.json`. The directory
must be private, and only its owner may read or modify the file. A directory
with overly broad permissions, or a symbolic link in place of the directory
or file, is rejected.

On Windows, the candidate encrypts access with DPAPI, tied to the Windows
account, in `%USERPROFILE%\.silex\auth\registry.dpapi`. It saves no plaintext
copy and rejects a tampered file or one that cannot be decrypted. This
protection does not cover a compromised user account.

The real GitHub workflow has been qualified on macOS ARM64. An offline native
matrix additionally verifies storage, session handling, revocation, and the
absence of OAuth traffic on macOS x64, Linux x64 and ARM64, and Windows x64
and ARM64. This matrix does not replace real GitHub consent on every system;
it qualifies the shared native behavior without using a personal account.

If access remains valid, `silex login` announces the existing connection
without replacing it. To use another GitHub account, disconnect first.
Public installation does not require this connection.

## Disconnect

```sh
silex logout
```

The command revokes access with the registry, then deletes the local file.
It does not revoke access from other machines or sign your browser out of
GitHub. It also succeeds when no local credential exists.

If the registry is unreachable, the command fails and keeps the file so you
can retry. Deleting that file manually would not revoke server-side access:
it would remain valid until it expires.

## Recover after a failure

A denied or expired authorization saves no local credential. Run `silex login`
again to start a new attempt. An interrupted or already consumed attempt does
not issue the same access again; if the final response was lost, start a new
login. Any access issued but not received expires on its own.

Network requests are limited to 25 seconds each, and the consent wait is
bounded. The CLI respects the polling delay requested by the server. Another
login or logout using the same storage is rejected rather than overwriting
its state.

This workflow is separate from the older `silex register`, which prepares a
pull request and requests GitHub repository permissions. It does not reuse
that authorization. The `silex publish` candidate does reuse the registry
access described here; `login` alone publishes no package. See
[Publish the local contents of a package](Publish-package.md) to inspect a
snapshot without signing in, publish it, and resume a transfer.

[Back to tools](README.md) · [Read the CLI reference](CLI-reference.md)
