---
title: Building an Elegant and Highly Modular Neovim Config
tags:
  - tag
date: 04-26-2023
summary: You wont find a better way to configure neovim. Written in 100% lua because vimscript is lame.
---

## Overview
A place for everything and everything in its place. This is the philosophy I organize my life by and my software configurations are no exception. I've designed this Neovim configuration to follow an intuitive structure that allows it to grow or shrink seamlessly depending on how I want to use my editor at a given point in time.

The following isn't intended to be a full walk through but rather a high level explanation of how I structure everything and how you could do the same. Some previous experience with lua and vim configuration are assumed.

See my full config on [Github](https://github.com/austinwhite/nvim-config).

### Directory Structure
    nvim
    ├─ after          # everything here is autoloaded
    │  ├─ ftplugin    # file type configurations
    │  └─ plugin      # plugin configurations
    ├─ colors         # colorschemes
    ├─ lua            # anything here can be required in the base init.lua
    │  ├─ core        # core nvim functionality
    │  └─ plugin      # nvim plugin management
    └─ init.lua       # nvim reads this file first

## Configuring Neovim
Technically, all that's required is an _init.lua_ file. When the lua interpreter looks at a directory, it searches for and init file as it's entry point. Only use this base _init.lua_ file to tell nvim where to find the other components.

Create an _init.lua_ file in the root directory.

```lua
-- base init.lua contents
-- note: core and plugin will be created later
require("core")
require("plugin")
```

### Core Functionality
    lua
    └─ core
       ├─ init.lua       # runs the other files in this directory
       ├─ option.lua     # non-plugin options
       ├─ mapping.lua    # non-plugin keymappings
       └─ autocmd.lua    # non-plugin auto commands

The _lua_ directory is special in that anything inside of it can be _required_ in the base _init.lua_ file. Inside of the lua directory there will be 2 sub directories, one for core functionality and one for plugins (discussed in the next section).

As mentioned before the lua interpreter always searches for an _init.lua_ when it looks at a directory. These core and plugin directories will each contain their own _init.lua_ file. Only use these files to _require_ the additional functionality provided in the option, mapping, and autocmd files. Create any additional files needed to suit your specific needs.

```lua
-- 'core' init.lua contents
require("core.mapping")
require("core.option")
require("core.autocmd")
```

### Plugins
    nvim 
    ├─ after                # anything here is autoloaded
    │  └─ plugin            # plugin configurations
    │     ├─ plugin1.lua
    │     ├─ plugin2.lua
    │     └─ pluginN.lua
    └─ lua
       └─ plugin
          ├─ init.lua       # runs the other files in this directory
          └─ packer.lua     # plugin manager via packer

Use your plugin manager of choice, packer is used here. In the _lua/plugin_ subdirectory, create an init and packer file. The packer file here will only be used to install plugins and setup plugins that don't require any configuration.


```lua
-- 'plugin' init.lua contents
require("plugin.packer")
```

All plugin configuration will be put in the _after/plugin_ subdirectory. No init file is required here because anything in _after_ is autoloaded on startup.


### Filetype Settings
    nvim 
    └─ after                  # anything here is autoloaded
       └─ ftplugin            # language specific settings
          ├─ language1.lua
          ├─ language2.lua
          └─ languageN.lua

_ftplugin_ stands for file type plugin. Nvim will detect the file type in a buffer and, if an ftplugin file exists for that type, apply the settings locally to that buffer.

For example, you can disable text wrap globally in _lua/core/options.lua_ and enable it in _after/plugin/markdown.lua_ so that when you open a markdown file it's easier to read. Another common use case is to change the tabstop size depending on a given languages style guide.


### Colorschemes
    nvim
    └─ colors                 # colorschemes
       ├─ colorscheme1.lua
       ├─ colorscheme2.lua
       └─ colorschemeN.lua

Configuring colorschemes in the _colors_ directory allows you to select your theme with the _colorscheme [colorscheme]_ option. Colorschemes will typically be installed using your package manager. Add the configurations here.

For example, I used packer to install the [github-nvim-theme](https://github.com/projekt0n/github-nvim-theme). I then ran the setup in _colors/github.lua_, then set the colorscheme in my _lua/core/option.lua_ file. You can also preview any theme by running the same command in command mode.

```lua
-- colors/github.lua
require("github-theme").setup({})


-- lua/core/option.lua
-- using nvim generic command interface
vim.api.nvim_cmd({
	cmd = "colorscheme",
	args = { "github_dimmed" },
}, {})

-- or using vim script
vim.opt.colorscheme = "github_dimmed"
```

