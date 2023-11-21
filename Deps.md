Dependencies
============

See https://github.com/neovim/neovim/blob/master/MAINTAIN.md.

Forks
=====

For some dependencies we maintain temporary "forks", which are simply private branches with a few extra patches, while we wait for the upstream project to merge the patches. This is done instead of maintaining the patches as (fragile) CMake `PATCH_COMMAND` steps.

* Nvim builds against and supports both the "vanilla" dependency (without Nvim's patches) and typically much older versions.

The complete, current list of forked dependencies is as follows:

* https://github.com/neovim/unibilium
  * The original project [was abandoned](https://github.com/neovim/neovim/issues/10302), so the [neovim/unibilium](https://github.com/neovim/unibilium) fork is considered "upstream" and is maintained on the `master` branch.
* https://github.com/neovim/libtermkey
  * We are waiting for the maintainer of libtermkey to release version 0.23.
