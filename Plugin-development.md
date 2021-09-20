This page contains advice to guide (lua) plugin authors.

One goal is to converge towards a standard in plugins so that some automation can be reached, e.g., in option discovery, setup or even plugin installation (see https://github.com/neovim/neovim/issues/14375 for a WIP).


# Lua dependency management

To avoid every user having to specify manually each plugin dependencies as it is common nowadays, it is possible to leverage luarocks. See [this blogpost](https://teto.github.io/posts/2021-09-17-neovim-plugin-luarocks.html) for more context.

As a plugin author, you can
1. Copy for instance this rockspec https://github.com/nvim-lua/plenary.nvim/blob/master/plenary.nvim-scm-1.rockspec in your repo , update the different fields to match your project and carefully rename the file (luarocks fails if the file prefix doesn’t match the project name)
2. (optional) If your plugin can be leveraged/referenced by other plugins as is the case for plenary.nvim, you should register your plugin on www.luarocks.org (either via the website or via `luarocks upload`).
3. (optional) Configure a CI to check your rockspeck works at all times. (TODO)


