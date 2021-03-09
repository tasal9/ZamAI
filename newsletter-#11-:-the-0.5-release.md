This is an attempt at a collaborative newsletter for the 0.5 release.

The goal is not to dive too deep 

The final one will be pushed at https://github.com/neovim/neovim.github.io/pull/200.

---
layout: newsletter
title: "Newsletter #11 - the promised one
category: newsletter
permalink: /news/2020/09/
---

Newsletter notes

## Something something Covid

## [0.5 Milestones](https://github.com/neovim/neovim/milestone/19)

## LSP & Tree-sitter - Maximizing Vim's potential/The future of text editing

- lightweight
- tight integration

## What is LSP

[Language Server Protocol](https://microsoft.github.io/language-server-protocol/) (LSP) is an open, JSON-RPC-based protocol for use between source code editors or integrated development environments (IDEs) and servers that provide programming language-specific features.
The goal of the protocol is to allow programming language support to be implemented and distributed independently of any given editor or IDE.

[Language specific implementations and feature support](https://langserver.org/)

- (auto)completion
- Code Actions (automatic formatting, organize imports, ...)
- Go to definition
- Show method signatures
- Show/go to references
- Snippets

## What is Tree Sitter

"A really awesome parser"
"Tree sitter offers a way to navigate ASTs"
"tree-sitter queries are the new snippets"?
Error recovery
"power of vim editing == textobjects" and "tree-sitter == smarter textobjects"

@norcalli - "every file you edit (which has a treesitter grammar definition available) will have the syntactic information of what you're working on available to use for anything you want. It's like a core intelligence was added to neovim which was fundamentally missing before."
@norcalli - "Syntax highlighting is just a consequence of finally being able to understand the program you're working on. There are many, many, many other consequences which are possible, and those text objects are ones for which I'm also super excited."

Came from Atom

A presentation from the author on what Tree-sitter is and how it came to be:
[Tree-sitter - A new parsing system for programming tools - Max Brunsfield](https://www.youtube.com/watch?v=Jes3bD6P0To&feature=youtu.be&t=232)

Both [`bash-language-server`](https://github.com/bash-lsp/bash-language-server) and [`wasm-language-server`](https://github.com/wasm-lsp/wasm-language-server) leverage Tree-sitter behind the scenes.

### Tree-sitter and Neovim

- Semantic Highlighting: Consistent syntax highlighting.
  Performance difference between syntax highlighting from a language server and syntax highlighting from TreeSitter

- `incremental selection` Expand/contract visual selection based on the named nodes from the grammar.
- `highlight_definitions` Highlight definition and usages of the current symbol under the cursor.
- `highlight_current_scope` Highlight a block that visualizes the current scope based on the current cursor position.
- `smart_rename` Rename the symbol under the cursor within the current scope (and current file).
- `navigation` Create mappings to list and jump to definitions or next/previous usage of the object under the cursor.
- `textobjects` Define custom Tree-Sitter based text objects similar to `ip` (inner paragraph) and `ap` (a paragraph). Create mappings to easily swap and move objects.
- Syntax based code folding and Statusline Indicator.

## Lua! Lua! Lua!

Some excerpts from Justin M Keyes ["We can have nice things"](https://www.youtube.com/watch?v=Bt-vmPC_-Ho) presentation at Vim Conf 2019

### Lua vs Vimscript

```text
Worse is better

Vim's missing 50%:

- Imperfect design => bad perf; macros, long lines, syntax
- Vimscript is slow: n AST, ad-hoc impl
- :vimgrep is slow, :syntax is slow
- Legacy arch: 600+ globals, high coupling, TUI assumption
- Inconsistent UI behaviour: win_getid() vs getwininfo()

```

```vim
foo.vim
  let s:sum = 0
  for i in range(1, 9999999)  " Parsed 10M times.
    let s:sum = s:sum + i     " Parsed 10M times.
  endfor                      " Parsed 10M times.
  call append('$', s:sum)
```

`ex_docmd.c:do_cmdline():`

- copies command (script line), sends to ex_docmd.c:do_one_cmd()
- ex_docmd.c:do_one_cmd() recursively parses the line
- ...everytime, for all lines in a Vimscript loop (for/while)

mention [Vim9 script](https://vimhelp.org/vim9.txt.html), the issues it addresses and any statements made by Neovim regarding compatibility.

### Lua stdlib

"Lua is designed for embedding"
"Lua is fast, LuaJit is *ridiculously* fast"
"Less is more: Lua language is super small, simple, _complete_(frozen)"

### Extensibility: Nvim is the "stdlib"

```text
Standard modules:
- inspect
- treesitter
- loop
- ... trivial to add more(mention luarocks/nvimrocks/plenary)
```

### Core Features Implemented in Lua

- [nvim-lspconfig](https://github.com/neovim/nvim-lspconfig) - Collection of common configurations for the Nvim LSP client.
- [completion-nvim](https://github.com/nvim-lua/completion-nvim)  - An auto completion framework that aims to provide a better completion experience with neovim's built-in LSP.
- [lsp-status.nvim](https://github.com/nvim-lua/lsp-status.nvim)  - A Neovim plugin/library for generating statusline components from the built-in LSP client.

### Extensibility: Fennel (Lisp) and MoonScript

- [Fennel(Lisp)](https://fennel-lang.org)
  - try [fennel-nvim](https://github.com/jaawerth/fennel-nvim) to auto-execute `init.fnl`
- [Aniseed](https://github.com/Olical/aniseed) - Bridges the gap between Fennel and Neovim. Aims to allow easy creation of plugins or configuration in a Clojure-like Lisp with great runtime performance.
- [Ratatoskr](https://github.com/vigoux/ratatoskr.nvim) - An experiment to use Fennel to generate queries in Neovim.
- [MoonScript](https://github.com/leafo/moonscript)
  - [nvim-moonmaker](https://github.com/svermeulen/nvim-moonmaker) - Moonscript to compiled Lua

### NVIM Dev tools - (which are the most useful?)

- [Plenary](https://github.com/nvim-lua/plenary.nvim) 
- [Packer](https://github.com/wbthomason/packer.nvim) mention luarocks integration
- [nlua](https://github.com/tjdevries/nlua.nvim)
- [nvim.lua](norcalli/nvim.lua)
- [nvim-luadev](https://github.com/bfredl/nvim-luadev)

- The nvim-lua family
  - Plenary/ popup.nvim, things that might become part of core
- tree-sitter: Rethinking regex : Expand region comparison and ideas for future plugins
  - speak about what could be replaced, word-motion? fancy markdown stuff, vim-exchange

### Lua Plugins and Configuration

Community member 'Nanotee' has created a collection of resources in his [Getting started using Lua in Neovim](https://github.com/nanotee/nvim-lua-guide) guide that provide a good starting point for developers.

---
- [architext](https://github.com/vigoux/architext.nvim) A structural editing plugin for neovim, powered by treesitter.
- [snippets](https://github.com/norcalli/snippets.nvim) A powerful snippet engine with live previews.
- [packer](https://github.com/wbthomason/packer.nvim) An ambitious new package manager
- [colorizer](https://github.com/norcalli/nvim-colorizer.lua) A high-performance color highlighter for Neovim which has no external dependencies.
- [formatter](https://github.com/mhartington/formatter.nvim) A plugin to asynchronously execute external formatting tools on the current buffer/range.
- [daedalus](https://github.com/hkupty/daedalus.nvim) Intended to be used by plugin developers, so their clients can call remote APIs through their plugins.
- [telescope](https://github.com/nvim-telescope/telescope.nvim) A modular fuzzy finder, written in pure lua.

## [Vim Conf](https://www.vimconf.live)

Covid preamble - online conf.

[Vim Conf YouTube channel](https://www.youtube.com/channel/UCPK_UHtbfcWABCi0F0GPG6w)

### Neovim Related Presentations

| | | |
|---|---|---|
| [Conversational Software Development](https://www.youtube.com/watch?v=RU28xy9JXxs) | Oliver Caldwell |
| [From User to Contributer](https://www.youtube.com/watch?v=EReNOGuMBmo) | Thomas Vigouoroux |
| [Neovim Builtin LSP](https://www.youtube.com/watch?v=C9X5VF9ASac) | TJ DeVries |
| [Neovim 0.5 work](https://www.youtube.com/watch?v=FxDBdbuvcU8) | Björn Linse |
| [What can't you do in Neovim?](https://www.youtube.com/watch?v=78WrSwEKNuM) | Ashkan Kiani |
| [Why is Lua a good fit for Neovim?](https://www.youtube.com/watch?v=IP3J56sKtn0) | TJ DeVries |



## UI's

### Changes related to UI

- mouse focus on window
- ext_popupmenu: completion menu
- ext_tabline: tab line
- ext_cmndline: command line
- ext_hlstate: highlight state
- ext_messages: messages
- ext_multigrid: windows, grids
- remote TUI (GSoC 2019)

[FireNvim](https://github.com/glacambre/firenvim) Turn your browser into a Neovim client.
[FVim](https://github.com/yatli/fvim) Cross platform Neovim UI, built with F# + Avalonia.
[GNvim](https://github.com/vhakulinen/gnvim) Neovim GUI aiming for rich code editing experience without any unnecessary web bloat.
[Neovide](https://github.com/Kethku/neovide) A graphically improved user interface for Neovim that acts functionally like the terminal UI.
[VV](https://github.com/vv-vim/vv) A minimalistic Vim experience with good macOS intergration. Optimized for speed and font rendering.
[Uivonim](https://github.com/smolck/uivonim) Fork of Veonim with GUI elements for the builtin LSP and more

## Notable commits/merges
libuv / libvterm ?

- fold characters can be customized via fillchars neovim/neovim#11568
- the foldcolumn can adapt its width depending on foldlevel neovim/neovim#11716
- better support for screen thanks to erw


### Highlight yanked region

Neovim now has a built-in function to briefly highlight the yanked region (similarly to https://github.com/machakann/vim-highlightedyank), configurable from Lua. To use it, add the following to your `init.vim`:
```
au TextYankPost * silent! lua vim.highlight.on_yank {higroup="IncSearch", timeout=150, on_visual=true}
```

(mention edge cases with block-visual selection for virtual edit (https://github.com/neovim/neovim/pull/13320) and multi-byte characters (https://github.com/neovim/neovim/pull/13896))

### LuaHL
## LibGit2

## PR's to watch

nvim__screenshot?
### Python2 deprecation

buf_set_text ? what will it enable?
### #12249 https://github.com/neovim/neovim/pull/12249 nvim_buf_set_text

### #12870 https://github.com/neovim/neovim/pull/12870 Color themes per window/line
### #12823 https://github.com/neovim/neovim/pull/12823 Doc ; Lua concepts
### #12593 https://github.com/neovim/neovim/pull/12593 fswatch autoread

### missing functionality for Lua developers

### #12378 https://github.com/neovim/neovim/pull/12378 [WIP] lua: Add autocmd with Lua callback natively #12378

## Merged (needs more thorough review of PR's from 0.4 -> 0.5)

### New Decorations API #12816 - `nvim_buf_get_extmark` / `nvim_buf_set_extmark`
### https://github.com/neovim/neovim/pull/12549 Lua: highlight.on_yank #12549
### https://github.com/neovim/neovim/pull/12536/files Execute lua callback on keystroke #12536




## Stats

Changed lines since the last newsletter:
```
git log v0.4.4..v0.5.0 --numstat --pretty=tformat: --numstat | nawk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s removed lines: %s total lines: %s\n", add, subs, loc }'
```

Merged commits:
```
git log v0.4.4..v0.5.0 --pretty=oneline | wc -l
```

Different commit authors:
```
git shortlog -e -s -n v0.4.4..v0.5.0 | wc -l
```

Documentation changes:
```
git log v0.3.0..v0.4.4 --numstat --pretty=tformat: --numstat runtime/doc | nawk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s removed lines: %s total lines: %s\n", add, subs, loc }'
added lines: 9226 removed lines: 7002 total lines: 2224
```

### Community efforts - notable blogposts and twitch/youtube content

Note that two of the core contributors have started streaming some of their neovim development sessions:
- @tjdevries https://www.twitch.tv/teej_dv
- @norcalli at 

We also welcome to the core team:
- @janlazo has been hard at work porting vim patches to neovim patches.
- @erw7 has fixed an impressive number of bugs, added [screen](https://www.gnu.org/software/screen/) support along with windows-related improvements
- @vigoux has become our "lead tree-sitter dev" and with @theHamsta and @kyazdani42 (and others), these madmen [experiment with tree-sitter](https://github.com/nvim-treesitter/nvim-treesitter) until they find something worth upstreaming
- @h-michael reigns over https://github.com/neovim/nvim-lspconfig and oversaw its renaming from the confusing `nvim-lsp` to the clearer `nvim-lspconfig`
- @mjlbach put his back in and carried the ball over the finish line


### What's next ?

A shoutout to our gsoc students who have done a great job on *big* features. With LSP and treesitter merged, we hope to merge these contributions soon. As a reminder:

## GSoC 2019

[TUI (Terminal UI) remote attachment](https://github.com/neovim/neovim/pull/10071)
[Multiprocessing Feature](https://github.com/neovim/neovim/pull/9943)

## GSoC 2020

[Project Proposals](https://github.com/neovim/neovim/wiki/GSoC-2020-Ideas)

[fswatch autoread](https://github.com/neovim/neovim/pull/12593)
[inccommand: plugin support: cmnd_can_preview for user commands](https://github.com/neovim/neovim/pull/11985)
[logging API](https://github.com/neovim/neovim/pull/7062)

## Thanks

Once more thanks to all contributors, sponsors, bug-reporters, supporters.
Note that you can now sponsor neovim via [github Sponsors](https://github.com/sponsors/neovim) or on [OpenCollective](https://opencollective.com/neovim).
Thank you **@justinmk** for the awesome project and thank you **@brammool** for your foundational work.
[##](posts/##.md) Github Sponsors - [12959](https://github.com/neovim/neovim/pull/12249)


- [GitHub will match all contributions (up to $5k)](https://github.blog/2019-05-23-announcing-github-sponsors-a-new-way-to-contribute-to-open-source/) within a developers first year.
- [BountySource charges fees](https://www.bountysource.com/fees), [GitHub only charges for payment processing](https://github.blog/2019-05-23-announcing-github-sponsors-a-new-way-to-contribute-to-open-source/).
As mentioned in our last newsletter, you can now sponsor neovim's development via both 
- [BountySource began introducing worrying changes to their Terms of Service agreement.](https://diziet.dreamwidth.org/5938.html)

