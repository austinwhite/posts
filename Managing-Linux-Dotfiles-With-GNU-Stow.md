---
title: Managing Linux Dotfiles With GNU Stow
tags:
  - placeholder
date: 04-05-2023
summary: Looking for an easier way to manage your Linux configuration files? Try GNU Stow, the command-line tool that simplifies organization, version control, and deployment.
---

## Overview
[GNU Stow](https://www.gnu.org/software/stow/) is a command-line tool that simplifies the management of configuration files on Linux systems. Instead of manually copying or symlinking files to their respective locations in the file system, Stow creates symbolic links from a central location to the correct destination folders. This allows for easier organization and version control of configuration files, and makes it simpler to apply changes across multiple machines or users. Additionally, using Stow makes it easier to remove configuration files cleanly, as all related files are located in one directory.

See an example configuration on my
[GitHub](https://github.com/austinwhite/dotfiles)

### Example Directory Structure
    dotfiles
    ├─ nvim
    │  └─ .confg
    │     └─ nvim
    │        └─ init.lua
    ├─ git
    │  ├─ .gitconfig
    │  └─ .config
    │     └─ git
    │        └─ .global-gitignore
    ├─ scripts
    │  └─ .local
    │     └─ bin
    │        ├─ script1
    │        ├─ script2
    │        └─ scriptN
    └─ starship
       └─ .config
          └─ starship.toml

When run from the root directory stow will create symlinks from your home directory to each terminating directory/file in _dotfiles_. Think of the 2nd level directory names (nvim, git, scripts, starship) as your home directory.

For example stow would create the following sym links for the directory structure above. Each pointing to their respective directories/files in _dotfiles_.

    ~/.config/nvim/
    ~/.gitconfig
    ~/.config/git/
    ~/.config/.starship.toml
    ~/.local/bin/

**NOTE**: The paths laid out in _dotfiles_ don't need to exist. When you unstow if any subdirectory in that path is being by an item not under stow management, that path will be created for you and those items will remain intact.

## Deployment
Deploy your configurations by running _stow /*_ to stow all
directories or _stow [dir name]_ to stow an individual directory.

I created some wrapper scripts to make adding and removing configurations on the fly more easy.

### Environment Setup

Create a file containing a space separated list at the root of the directory containing the directory names you want stowed. You can add and remove any directory on the fly depending on your current use case and restow.

    filename:
    stow-dirs.txt

    contents:
    dir1 dir2 dir3 dirN

These scripts will bootstrap stow it's not already installed on your system. I always have the path to my dotfiles saved in an environment variable, if that doesn't exist on your system, the scripts will still work.

### Stow Script

```bash
#!/usr/bin/env bash

if ! command -v stow $>/dev/null
then
    echo "command not found: stow"
    echo "installing gnu stow..."
    sudo pacman -S stow 2>/dev/null
    sudo apt-get install stow 2>/dev/null
    sudo yum install stow 2>/dev/null
    sudo brew install stow 2>/dev/null 
fi

if [[ -z "${DOTFILES}" ]]; then
    echo "DOTFILES environment variable does not exist."
    echo "setting DOTFILES to \$HOME/.dotfiles"
    export DOTFILES="$HOME/.dotfiles"
fi

dotfile_dirs=`cd ${DOTFILES} && head ./stow-dirs.txt` 

for dir in $dotfile_dirs
do
    cd ${DOTFILES}
    echo "stowing: $dir"
    stow -D $dir
    stow $dir
done
```

### Unstow Script

```bash
#!/usr/bin/env bash

if ! command -v stow $> /dev/null
then
    echo "command not found: stow"
    echo "installing gnu stow..."
    sudo pacman -S stow 2>/dev/null
    sudo apt-get install stow 2>/dev/null
    sudo yum install stow 2>/dev/null
    sudo brew install stow 2>/dev/null 
fi

if [[ -z "${DOTFILES}" ]]; then
    echo "DOTFILES environment variable does not exist."
    echo "setting DOTFILES to \$HOME/.dotfiles"
    export DOTFILES="$HOME/.dotfiles"
fi

dotfile_dirs=`cd ${DOTFILES} && head ./stow-dirs.txt` 

for dir in $dotfile_dirs
do
    cd ${DOTFILES}
    echo "un-stowing: $dir"
    stow -D $dir
done
```

