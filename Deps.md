Dependencies
============

Forks
=====

For some dependencies we maintain temporary "forks", which are simply private branches with a few extra patches, while we wait for the upstream project to merge the patches. This is done instead of maintaining the patches as (fragile) CMake `PATCH_COMMAND` steps.

* For Windows OS some of these patches are required.
* For all other cases, Nvim builds against and supports both the "vanilla" dependency (without Nvim's patches) and typically much older versions.

The complete, current list of forked dependencies is as follows:

* ~https://github.com/neovim/libuv~
  * ~Neovim fork lives on the `nvim` branch: https://github.com/libuv/libuv/compare/v1.x...neovim:nvim~
  * Not used since https://github.com/neovim/neovim/pull/15889
* https://github.com/neovim/libvterm
  * Neovim fork lives on the `nvim` branch: https://github.com/neovim/libvterm/compare/master...nvim
* https://github.com/neovim/libtermkey
* https://github.com/neovim/unibilium
  * The original project [was abandoned](https://github.com/neovim/neovim/issues/10302), so the [neovim/unibilium](https://github.com/neovim/unibilium) fork is considered "upstream" and is maintained on the `master` branch.
