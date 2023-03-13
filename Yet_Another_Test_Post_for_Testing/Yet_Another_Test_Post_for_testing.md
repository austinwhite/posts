---
title: Yet Another Test Post for Testing
tags:
  - linux
  - test tag
  - another test tag
  - tagtag
  - 5tag
  - 6 tag
  - 7
date: 08-24-2022
summary: Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo. 
---

[GNU Stow](https://www.gnu.org/software/stow/) is a symlink manager. Its 
intended use to manage multiple versions of the same software on a single 
system, however it's perfectly suited to manage Linux configuration files 
as well. 

See an example configuration on my
[GitHub](https://github.com/austinwhite/dotfiles)
(Managed with GNU Stow at the time of this write-up)

## How it Works
I keep all of my config files in a single git repository. This makes setting 
up new systems incredibly simple.

Stow is easier to understand using an example.

Say you want your _.vimrc_ file at the path _$HOME/.config/vim/.vimrc_

To accomplish this the following file structure would exist in your dotfiles
repo.

```
vim/.config/vim/.vimrc
```

The first level directroy can be named anything, stow will treat it as if
it's your home directory and create a symlink to wherever that path
terminates. This could be a single file or a directory that contains 1 or 
more files.

### Deployment Scripts
You can deploy your configurations by running _'stow /*'_ to stow all
directories or _'stow [dir name]'_ to stow an individual directroy. I prefer 
a more granular approach so I've written my own scripts.

**Environment Setup**

Create a file with a space separated list at the root of the directory 
containing your files to be stowed. You can add and remove any directory 
on the fly depending on your current use case.

```
file:
stow-dirs.txt

contents:
dir1 dir2 dir3 dirN
```

These scripts will bootstrap stow using pacman if it's not installed on your
system. If you don't use an Arch based distro switch that statement out 
with the package manager of your choice.

I set an environment variable $DOTFILES to the path where my dotfiles git 
repository resides.

**Stow Script**

```bash
#!/usr/bin/env bash

if ! command -v stow $> /dev/null
then
    echo "command not found: stow"
    echo "bootstrapping gnu stow..."
    sudo pacman -S stow
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

**Unstow Script**

```bash
#!/usr/bin/env bash

if ! command -v stow $> /dev/null
then
    echo "command not found: stow"
    echo "bootstrapping gnu stow..."
    sudo pacman -S stow
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

Sed ut perspiciatis, unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam eaque ipsa, quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt, explicabo. Nemo enim ipsam voluptatem, quia voluptas sit, aspernatur aut odit aut fugit, sed quia consequuntur magni dolores eos, qui ratione voluptatem sequi nesciunt, neque porro quisquam est, qui dolorem ipsum, quia dolor sit amet consectetur adipisci[ng] velit, sed quia non numquam [do] eius modi tempora inci[di]dunt, ut labore et dolore magnam aliquam quaerat voluptatem. 

![test image](posts/Managing_Linux_Dotfiles_With_GNU_Stow/images/hero.jpg)

Test katex:

$$ d(p, q) =\sum_{i=0}^{n}\sqrt{(p_{i} - q_{i})^{2}} $$
