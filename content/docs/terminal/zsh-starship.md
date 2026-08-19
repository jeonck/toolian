---
weight: 2020
title: "zsh and Starship"
description: "Completion and history settings, plus a prompt that shows your branch and language versions."
icon: "chevron_right"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Your prompt is a status bar answering *where am I and what am I doing?* Being able to
see the current branch or Python virtualenv at a glance cuts out a lot of `git branch`
and `which python`.

## Baseline zsh settings

zsh is the default shell on macOS. To switch on Linux:

```bash
sudo apt install zsh
chsh -s $(which zsh)
```

These lines in `~/.zshrc` make the biggest difference:

```bash
# generous history, no duplicates, shared across windows
HISTSIZE=50000
SAVEHIST=50000
setopt SHARE_HISTORY HIST_IGNORE_ALL_DUPS HIST_REDUCE_BLANKS

# type a directory name to cd into it
setopt AUTO_CD

# case-insensitive tab completion
autoload -Uz compinit && compinit
zstyle ':completion:*' matcher-list 'm:{a-z}={A-Za-z}'
```

## Two plugins are enough

Skip the heavy frameworks; install only the two with the largest payoff.

```bash
brew install zsh-autosuggestions zsh-syntax-highlighting
```

```bash
# at the bottom of ~/.zshrc
source $(brew --prefix)/share/zsh-autosuggestions/zsh-autosuggestions.zsh
source $(brew --prefix)/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

- **autosuggestions** greys in a command you ran before. Press `→` to accept it.
- **syntax-highlighting** turns unknown commands red, so you catch typos before you
  press Enter.

## Starship

A prompt that works the same regardless of which shell you use.

```bash
brew install starship
echo 'eval "$(starship init zsh)"' >> ~/.zshrc
```

Configuration is one file, `~/.config/starship.toml`.

```toml
# only show command duration when it was actually slow
[cmd_duration]
min_time = 2000
format = " [$duration]($style)"

# keep the path to three segments
[directory]
truncation_length = 3
truncate_to_repo = true

# make failures obvious
[character]
success_symbol = "[❯](green)"
error_symbol = "[❯](red)"
```

Git branch, Node/Python/Go versions, Kubernetes context and so on appear automatically
only inside a relevant project. Turn modules on and off with `disabled`.

```toml
[kubernetes]
disabled = false
```

## A few aliases

```bash
alias ll='ls -alh'
alias gs='git status -sb'
alias gd='git diff'
alias ..='cd ..'
alias ...='cd ../..'
```

Aliases are convenient, but overusing them leaves you stranded on other people's
machines. Scripts you share with a team should spell out the real commands.

## Next

With the shell comfortable, stop opening extra windows and manage one session instead →
[tmux](/docs/terminal/tmux/)
