# User Interface (UI) architecture 

The present paragraph is meant to help developers understand the UI source code.

One of Neovim's goals is to make it possible to create (N)vim GUIs (Graphical UIs) without touching the core code.
This is done via UI events sent over msgpack-rpc. These UIs ([nvim-qt](https://github.com/equalsraf/neovim-qt), oni, nyaovim, ...) are called **remote UI clients**.

The source files most directly involved with UI events are:
1. `src/nvim/ui.*`: calls handler functions of registered UI structs (independent from msgpack-rpc)
2. `src/nvim/api/ui.*`: forwards messages over msgpack-rpc to remote UIs.

UI events are defined in `src/nvim/api/ui_events.in.h` , this file is not compiled directly, rather it parsed by `src/nvim/generators/gen_api_ui_events.lua` which autogenerates wrapper functions used by the source files above. It also generates metadata accessible as `api_info().ui_events`.

UI events are deferred to UIs, which implies to do deepcopy of the UI events data. 

Once you've finished your changes to neovim UI protocol, don't forget to update `set(NVIM_API_LEVEL 2)         # Bump this after any API change.` in `CMakeLists.txt`

Here are a few more references:
* :help msgpack-rpc
* :help ui
* https://github.com/neovim/neovim/pull/3246
* https://github.com/neovim/neovim/pull/18375
* https://github.com/neovim/neovim/pull/21605
* http://tarruda.github.io/articles/neovim-smart-ui-protocol/