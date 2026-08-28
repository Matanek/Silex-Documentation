# Install the Silex extension for Zed

The Silex extension for Zed provides syntax highlighting, diagnostics,
completion, and navigation through the server launched by `silex lsp`.

For Silex 0.42, the extension is not yet available in the Zed extension
gallery. Install it as a development extension from its official repository.

## Prepare the required tools

[Install Silex](Installation.md), then verify that its command is available:

```sh
silex --version
```

Also install Git and Rust with `rustup`, then prepare the target used by Zed
extensions:

```sh
rustup target add wasm32-wasip2
```

If Silex was added to `PATH` while Zed was open, restart Zed so the extension
can find the command.

## Install the repository in Zed

Clone the extension into a folder you will keep:

```sh
git clone https://github.com/Matanek/Silex-Extension-Zed.git
```

In Zed:

1. open the command palette;
2. run `zed: install dev extension`;
3. select the `Silex-Extension-Zed` folder containing `extension.toml`.

Then open an `.sx` file. Zed should select the Silex language and automatically
start `silex lsp`.

## Update the extension

Fetch the new repository version:

```sh
git -C Silex-Extension-Zed pull --ff-only
```

In Zed's command palette, then run `zed: rebuild dev extension`. If only the
`silex` command was updated, `editor: restart language server` is enough.

If the server or extension compilation fails, open the log with
`zed: open log` and check `silex --version` again in a terminal.

See the [extension repository](https://github.com/Matanek/Silex-Extension-Zed)
and the [official Zed development-extension instructions](https://zed.dev/docs/extensions/developing-extensions#developing-an-extension-locally).

[Back to the tools](README.md) · [Install the extension for VS Code](Install-VSCode-extension.md)
