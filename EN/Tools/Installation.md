# Install Silex

Silex 0.42 is distributed as a standalone executable. Using it requires
neither Zig, Git, nor a local copy of the repository.

Published distributions support:

- macOS on Apple Silicon;
- Linux x64;
- Windows x64.

## Install on macOS or Linux

You can [inspect the official script](https://github.com/Matanek/Silex/blob/v0.42.0/install.sh)
before running it:

```sh
curl -fsSL https://raw.githubusercontent.com/Matanek/Silex/v0.42.0/install.sh \
    | SILEX_VERSION=0.42.0 sh
```

The script selects the appropriate distribution, verifies its SHA-256
checksum, and installs `silex` in `~/.local/bin`.

### Make the command available

The installation script does not modify your shell configuration. If the
terminal cannot immediately find `silex`, add the installation folder to the
current session's `PATH`:

```sh
export PATH="$HOME/.local/bin:$PATH"
```

If you set `SILEX_INSTALL_DIR`, replace `~/.local/bin` with your chosen folder.

To preserve this setting in future terminals, add the same line to your shell
configuration file:

- `~/.zshrc` with Zsh, notably on macOS;
- `~/.bashrc` with Bash, common on Linux.

With Zsh, reload the file without closing the terminal:

```sh
source ~/.zshrc
```

With Bash:

```sh
source ~/.bashrc
```

On some Linux distributions, `~/.profile` already adds `~/.local/bin` to
`PATH`. In that case, `source ~/.profile` is enough after the first
installation.

## Install on Windows

Run this command in PowerShell:

```powershell
$env:SILEX_VERSION = "0.42.0"
irm https://raw.githubusercontent.com/Matanek/Silex/v0.42.0/install.ps1 | iex
```

The script verifies the SHA-256 checksum and installs `silex.exe` in
`%LOCALAPPDATA%\Silex\bin`. The Windows executable is not signed yet, so
Windows may show a reputation warning the first time you use it.

The script does not modify `PATH` on Windows either. If PowerShell cannot find
`silex`, make the command available in the current session:

```powershell
$env:PATH = "$env:LOCALAPPDATA\Silex\bin;$env:PATH"
```

To preserve this setting, add `%LOCALAPPDATA%\Silex\bin` to your account's
`Path` in the Windows environment variables, then open a new terminal.

## Verify the installation

In the terminal where you just reloaded the configuration, or in a new
terminal, run:

```sh
silex --version
```

For this documentation, the command must print:

```text
silex 0.42.0
```

You can now [write and run your first program](../Learn/First-program.md).

## Choose the installation folder

On macOS or Linux, place the binary in another folder with
`SILEX_INSTALL_DIR`:

```sh
curl -fsSL https://raw.githubusercontent.com/Matanek/Silex/v0.42.0/install.sh \
    | SILEX_VERSION=0.42.0 SILEX_INSTALL_DIR="$HOME/bin" sh
```

In PowerShell:

```powershell
$env:SILEX_VERSION = "0.42.0"
$env:SILEX_INSTALL_DIR = "$HOME\bin"
irm https://raw.githubusercontent.com/Matanek/Silex/v0.42.0/install.ps1 | iex
```

[Back to the Silex tools](README.md) ·
[Update and maintain Silex](Maintain-Silex.md)
