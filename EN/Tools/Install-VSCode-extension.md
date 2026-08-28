# Install the Silex extension for VS Code

The Silex extension for Visual Studio Code recognizes `.sx` files and provides
syntax highlighting, character pairs, indentation, and line comments.

For Silex 0.42, it has not yet been published to the Visual Studio Marketplace.
Build its VSIX package from the official repository, then install that file in
VS Code.

## Build the VSIX file

Install Git, Node.js, and npm, then clone the repository:

```sh
git clone https://github.com/Matanek/Silex-Extension-VSCode.git
cd Silex-Extension-VSCode
npm ci
npm run package -- --out silex-language-support.vsix
```

The last command creates `silex-language-support.vsix` in the repository
folder.

## Install the extension

If the `code` command is available in your terminal, run:

```sh
code --install-extension silex-language-support.vsix
```

Otherwise, open the VS Code command palette, run
`Extensions: Install from VSIX...`, then select the created file.

Open an `.sx` file and verify that VS Code displays `Silex` as the active
language.

This extension version is declarative: it does not launch the LSP yet and
therefore provides no semantic diagnostics, completion, or go-to-definition.

## Update the extension

Fetch the sources, rebuild the same file, then force its replacement:

```sh
cd Silex-Extension-VSCode
git pull --ff-only
npm ci
npm run package -- --out silex-language-support.vsix
code --install-extension silex-language-support.vsix --force
```

See the [extension repository](https://github.com/Matanek/Silex-Extension-VSCode)
and the [official VSIX installation instructions](https://code.visualstudio.com/docs/configure/extensions/extension-marketplace#_install-from-a-vsix).

[Back to the tools](README.md) · [Install the extension for Zed](Install-Zed-extension.md)
