# distant.nvim: A fork for me.

Tested on windows platform.

Compatible (not fully but can be run as almost normal) with local username contains whitespace like `C:\Users\fa fa\.ssh\known_hosts`

## Configuration

If your ssh-related path contains whitespace, you might want to use mklink to link .ssh folder to another place.

```dos
cd C:\Users\Public
mklink /D .ssh "C:\Users\fa fa\.ssh"
```

Put these code below in your nvim init.lua

```lua
-- ===
-- === Use case for `DistantConnect`
-- ===

local user = 'yourname' -- Remote machine user name.
local ip = '127.0.0.1' -- Can also be like example.com
local port = '22' -- The port you use.
-- If your local matchine username contains whitespace,
-- you should move your known_hosts file and id_rsa to other path that does not contains whitespace.
local known_hosts = 'C:\\Users\\Public\\.ssh\\known_hosts' -- (Optional) known_hosts file loaction path.
local identity_files = 'C:\\Users\\Public\\.ssh\\id_rsa' -- (Optional) id_rsa file loaction path.
local cmd = {
  args = {
    ('ssh://%s@%s:%s'):format(user, ip, port)
  },
  opts = {
    options = {
      -- ['ssh.backend'] = 'libssh',
      ['ssh.user_known_hosts_files'] = known_hosts,
      ['ssh.identity_files'] = identity_files
    }
  }
}

-- Bind <Leader>t to start a new ssh connection to visit remote machine file system.
vim.keymap.set('n', '<Leader>t', function()
  require('distant.command').connect(cmd)
end)
```

## Notice

This plugin conflicts with `jbgutierrez/vim-better-comments`.

---

# distant.nvim

[![CI](https://github.com/chipsenkbeil/distant.nvim/actions/workflows/ci.yml/badge.svg)](https://github.com/chipsenkbeil/distant.nvim/actions/workflows/ci.yml)

A wrapper around [`distant`](https://github.com/chipsenkbeil/distant) that
enables users to edit remote files from the comfort of their local environment.

- **Requires neovim 0.7+**
- **Requires distant 0.20.0-alpha.4**

🚧 **(Alpha stage software) This plugin is in rapid development and may
break or change frequently!** 🚧

## Features

Supports the following features against remote machines:

- [X] Retrieving a list of available files & directories
- [X] Editing remote files
- [X] Creating and deleting files & directories
- [X] Copying files & directories
- [X] Renaming files & directories
- [X] Running [LSPs](https://neovim.io/doc/lsp/) remotely and getting live results locally
- [X] [telescope.nvim](https://github.com/nvim-telescope/telescope.nvim) integration via `:Telescope distant search`

## Installation & Setup

> It is **highly** recommended to not use the master branch of this plugin.
> Instead, prefer either a branch (`v0.1` or `v0.2`) or a specific tag (`v0.1.1`)
> to lock in the plugin. When using a branch, you will get rolling updates to
> that branch. When using a tag, you are locked into the features and stability
> of the plugin at that point!

Using [packer.nvim](https://github.com/wbthomason/packer.nvim), the quickest
way to get up and running is the following:

```lua
use {
  'chipsenkbeil/distant.nvim',
  branch = 'v0.2',
  config = function()
    require('distant').setup {
      -- Applies Chip's personal settings to every machine you connect to
      --
      -- 1. Ensures that distant servers terminate with no connections
      -- 2. Provides navigation bindings for remote directories
      -- 3. Provides keybinding to jump into a remote file's parent directory
      ['*'] = require('distant.settings').chip_default()
    }
  end
}
```

The above will initialize the plugin with the following:

1. Any spawned distant server will shutdown after 60 seconds of inactivity
2. Standard keybindings are assigned for remote buffers (described below)

#### Within a file

| Key | Action                         |
|-----|--------------------------------|
| `-` | `lua distant.nav.actions.up()` |

#### Within a directory

| Key        | Action                              |
|------------|-------------------------------------|
| `<Return>` | `lua distant.nav.actions.edit()`    |
| `-`        | `lua distant.nav.actions.up()`      |
| `K`        | `lua distant.nav.actions.mkdir()`   |
| `N`        | `lua distant.nav.actions.newfile()` |
| `R`        | `lua distant.nav.actions.rename()`  |
| `D`        | `lua distant.nav.actions.remove()`  |

#### Post-setup

Run `:DistantInstall` to complete the setup.

* For more information on installation, check out `:help distant-installation`
* For more information on settings, check out `:help distant-settings`

## Getting Started

In order to operate against a remote machine, we first need to establish
a connection to it. To do this, we run `:DistantLaunch {host}` where the host
points to the remote machine and can be an IP address like `127.0.0.1` or
a domain like `example.com`.

The launch command will attempt to SSH into the remote machine using port 22
by default and start an instance of [`distant`](https://github.com/chipsenkbeil/distant).

Once started, all remote operations will be sent to that machine! You can try
out something simple like displaying a list of files, directories, and symlinks
by running `:DistantOpen /some/dir`, which will open a dialog that displays
all of the contents of the specified directory.

## Documentation

For more details on available functions, settings, commands, and more,
please check out the vim help documentation via
[`:help distant.txt`](doc/distant.txt).

## Demo

### Intro Video

Demonstrates using distant.nvim to edit files and use a language server on a
remote machine.

[![Intro Video](https://img.youtube.com/vi/BuW2b1Ii0RI/0.jpg)](https://www.youtube.com/watch?v=BuW2b1Ii0RI)

### v0.1.0 Update

Demonstrates the new release of distant.nvim (v0.1.0) leveraging distant's new
lua module (v0.15.0). Main highlights include:

- integrated ssh authentication
- ssh mode
- refactored and simplified vim & Lua APIs
- complete help documentation

[![v0.1.0 update](https://img.youtube.com/vi/wVAsbpByQ3o/0.jpg)](https://www.youtube.com/watch?v=wVAsbpByQ3o)

## License

This project is licensed under either of

Apache License, Version 2.0, (LICENSE-APACHE or
[apache-license][apache-license]) MIT license (LICENSE-MIT or
[mit-license][mit-license]) at your option.

[apache-license]: http://www.apache.org/licenses/LICENSE-2.0
[mit-license]: http://opensource.org/licenses/MIT
