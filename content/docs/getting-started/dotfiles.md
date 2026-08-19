---
weight: 1030
title: "Dotfiles"
description: "Collecting your shell, editor, and Git configuration into one Git repository you can restore anywhere."
icon: "settings_backup_restore"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Configuration files whose names start with a dot — `.zshrc`, `.gitconfig`, your editor
settings — are collectively called dotfiles. Gather them into one Git repository and
setting up a new machine takes minutes, and "the setup that used to work" is always
recoverable.

## The simplest approach: a repo plus symlinks

```bash
mkdir -p ~/dotfiles && cd ~/dotfiles
git init

# move the real file into the repo, leave a link where it was
mv ~/.zshrc ~/dotfiles/zshrc
ln -s ~/dotfiles/zshrc ~/.zshrc

mv ~/.gitconfig ~/dotfiles/gitconfig
ln -s ~/dotfiles/gitconfig ~/.gitconfig

git add -A && git commit -m "Start dotfiles"
```

On a new machine, clone and re-link:

```bash
git clone https://github.com/<you>/dotfiles ~/dotfiles
ln -sf ~/dotfiles/zshrc ~/.zshrc
ln -sf ~/dotfiles/gitconfig ~/.gitconfig
```

## Automating the links: GNU Stow

Once you have more than a handful of files, linking by hand becomes its own chore.
`stow` unfolds a directory structure into your home directory as symlinks.

```bash
brew install stow

# with files arranged as ~/dotfiles/zsh/.zshrc
cd ~/dotfiles && stow zsh git nvim
```

`stow -D zsh` reverses it.

## What goes in, what stays out

| In | Out |
|---|---|
| `.zshrc`, `.bashrc`, `.profile` | SSH private keys, `.aws/credentials` |
| `.gitconfig` (minus your identity) | Token-bearing `.npmrc`, `.netrc` |
| Editor settings and keymaps | Caches and history files |
| `Brewfile`, package lists | Company-internal URLs |

Never put secrets in the repository. To make mistakes harder, put the patterns in
`.gitignore` up front.

```
*.pem
*_rsa
.env
credentials
```

## Per-machine differences

If your work and personal machines need different Git emails, have the shared config
pull in a local file.

```bash
# ~/dotfiles/gitconfig
[include]
    path = ~/.gitconfig.local
```

`~/.gitconfig.local` stays out of the repository and differs per machine.

## Next

Groundwork done. Time to start layering actual tools on top, beginning with
[Terminal & Shell](/docs/terminal/).
