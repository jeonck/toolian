---
weight: 1030
title: "Package Managers"
description: "Installing, updating, and removing CLI tools with Homebrew, winget, and apt."
icon: "inventory_2"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Every install command in the rest of this site assumes a package manager. Downloading
installers from websites makes updates and removals awkward, and leaves you with no
record of what you installed.

## macOS — Homebrew

One line to install:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

| Goal | Command |
|---|---|
| Install a CLI tool | `brew install ripgrep` |
| Install a GUI app | `brew install --cask visual-studio-code` |
| Search | `brew search fzf` |
| Show details | `brew info jq` |
| Update everything | `brew update && brew upgrade` |
| Remove | `brew uninstall ripgrep` |
| List installs | `brew leaves` (only what you asked for) |

## Windows — winget

Bundled since Windows 10. If it's missing, install "App Installer" from the Microsoft
Store.

```powershell
winget search ripgrep
winget install BurntSushi.ripgrep.MSVC
winget upgrade --all
winget uninstall BurntSushi.ripgrep.MSVC
```

For a repository with more developer tooling, add [Scoop](https://scoop.sh/) alongside it.

```powershell
irm get.scoop.sh | iex
scoop install ripgrep fd bat
```

## Linux — apt / dnf

```bash
sudo apt update && sudo apt install ripgrep fd-find bat
sudo dnf install ripgrep fd-find bat
```

Distribution repositories sometimes lag well behind. When you need current versions,
grab the `.deb`/`.rpm` from each tool's GitHub releases, or use Homebrew, which works
on Linux as well as macOS.

## Keep the list in a file

To rebuild a machine in one shot, record what you have.

```bash
# save
brew leaves > ~/dotfiles/brew-list.txt
brew list --cask > ~/dotfiles/brew-cask-list.txt

# restore
xargs brew install < ~/dotfiles/brew-list.txt
```

Homebrew also has a dedicated format, the `Brewfile`.

```bash
brew bundle dump --file=~/dotfiles/Brewfile --force
brew bundle install --file=~/dotfiles/Brewfile
```

## Next

With the install list in a file, it's time to manage the configuration itself →
[Dotfiles](/docs/getting-started/dotfiles/)
