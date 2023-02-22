# NvChad Version 2.0 Guide

**New guide project on NvChad**

Version 2.0 brings numerous new features and changes to the `custom` folder configuration. The main changes are necessary to adapt the plugin installation according to the guidelines provided by `lazy.nvim` and the new structure of the `custom` folder.

The installation proposes the creation of the folder structure for customization:

> Do you want to install chadrc template? (y/n) :

and has been modified with the updates of `02.19.2023`, the structure is as follows:

```text
custom
├── chadrc.lua
├── configs
│   ├── lspconfig.lua
│   ├── null-ls.lua
│   └── overrides.lua
├── highlights.lua
├── init.lua
├── mappings.lua
└── plugins.lua
```

The structure thus is cleaner and more logical, from the main `custom` folder we have the file _plugins.lua_ instead of _plugins/init.lua_ and also the `configs` folder is from the logical point of view more adherent to its function, to contain the configuration files.
I think I will edit the guide following this structure.

Structure v2.0 without template

```text
nvim
├── init.lua
├── lazy-lock.json
├── LICENSE
└── lua
    ├── core
    │   ├── bootstrap.lua
    │   ├── default_config.lua
    │   ├── init.lua
    │   ├── mappings.lua
    │   ├── options.lua
    │   └── utils.lua
    └── plugins
        ├── configs
        │   ├── cmp.lua
        │   ├── lazy_nvim.lua
        │   ├── lspconfig.lua
        │   ├── mason.lua
        │   ├── nvimtree.lua
        │   ├── others.lua
        │   ├── telescope.lua
        │   ├── treesitter.lua
        │   └── whichkey.lua
        └── init.lua
```

**Note:** To be checked with the old one, especially lazy-lock.json

A configuration update mechanism seems to have been introduced, this should eliminate the need to close and reopen the editor. `To Verify`

![Update Config](/nvchad/images/reloading_config.png)

And this works globally, with two instances of _nvim_ open the change is also reported in the inactive instance as soon as it is accessed. And the localization also seems to work; I seem to remember that in the previous version the dialogues were all in English.

The settings used in the guide in `init.lua` also work correctly in the new configuration.

```lua
local opt = vim.opt

opt.tabstop = 4
opt.softtabstop = 4
opt.shiftwidth = 4
opt.shiftround = false
opt.expandtab = true
opt.autoindent = true
opt.smartindent = true
```

## Differences lazy.nvim vs. packer.nvim

At the user level with the introduction of `lazy.nvim` it is no longer necessary to know the various commands (as in packer), there is only one command `:Lazy` and everything else is done from a unified interface.

![Lazy](/nvchad/images/lazy_nvim.png)

With packer `wants`, `requires` and `after` can be used to manage dependencies. With lazy, this isn't needed for most of the lua dependencies.

## Comparative table:

### packer.nvim -> lazy.nvim

- `setup` -> `init`
- `requires` -> `dependencies`
- `as` -> `name`
- `opt` -> `lazy`
- `run` -> `build`
- `lock` -> `pin`
- `disable=true` -> `enabled = false`
- `tag='*'` -> `version="*"`
- `after` information_source not needed for most use-cases. Use dependencies otherwise.
- `wants` information_source not needed for most use-cases. Use dependencies otherwise.
- `config` don't support string type, use fun(LazyPlugin) instead.
- `module` is auto-loaded. No need to specify
- `keys` spec is different
- `rtp` can be accomplished with:

```lua
config = function(plugin)
    vim.opt.rtp:append(plugin.dir .. "/custom-rtp")
end
```

It also changes the way the plugin is written in the `plugins.lua` file, there are no more square brackets in the plugin name and everything is enclosed by curly brackets.

- packer.nvim

```lua
["folke/which-key.nvim"]
```

- lazy.nvim

```lua
"folke/which-key.nvim",
```

Changing the logic for enabling/disabling plugins involves changing the `which-key` configuration: 

Configuration with `packer`

```lua
["folke/which-key.nvim"] = { disable = false },
```
Configuration with `lazy`

```lua
  -- Enable which-key
  {
  "folke/which-key.nvim",
    enabled = "true",
  },
```

`which-key` is also disabled by default in version 2.0.

## Plugins

### Plugins to add:

`natecraddock/workspaces.nvim` for project management, allows you to switch or place in the project folder once you open _nvim_.

Tested configuration for lazy.nvim:

```lua
  -- Workspaces
  {
  "natecraddock/workspaces.nvim",
    cmd = { "WorkspacesList", "WorkspacesAdd", "WorkspacesOpen", "WorkspacesRemove" },
    config = function()
      require("workspaces").setup {
        hooks = {
          open = "NvimTreeFocus",
        },
      }
    end,
  },
```

**Note:** Currently from what we have seen we must always make the commands explicit in the plugin configuration with `cmd` otherwise the commands are not available in the IDE.

Confirmed with the installation of the LazyGit plugin:

```lua
  {
  "kdheepak/lazygit.nvim",
    cmd = "LazyGit",
  },
```


## Language Servers

Configuring language servers is hassle-free, simply enter the required servers in `custom/configs/lspconfig.lua`.

```lua
local servers = { "html", "cssls", "tsserver", "clangd", "marksman" }
```

Add the server to Mason in `custom/configs/overrides.lua`:

```lua
  -- markdown
    "marksman"
```

Close the editor and reopen it, and when you restart Mason will take care of installing the missing server and making it available.

## Remarks:

For a more homogeneous display the screens should all be made with the same theme and its complementary.
A theme that lends itself to this purpose is `onedark` and the complementary `one_light`. The others either lack the complementary or are not readable enough in the light version.

And set in the custom configuration of NvChad in `custom/chadrc.md`:

```lua
  theme_toggle = { "onedark", "one_light" },
  theme = "onedark",
```

## Issues:

Currently none of the markdown-preview plugins tried work.
